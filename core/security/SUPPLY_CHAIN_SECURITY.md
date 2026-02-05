# Supply Chain Security

> Securing dependencies and AI model trust in code generation.

---

## The Risk

AI code generation introduces supply chain risks at multiple levels:

```
┌─────────────────────────────────────────────────────────────┐
│                    SUPPLY CHAIN RISKS                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  AI Model Provider                                           │
│  └─ Model poisoning, prompt leakage, availability           │
│                                                              │
│  Generated Dependencies                                      │
│  └─ AI may suggest malicious/typosquatted packages          │
│                                                              │
│  Build Pipeline                                              │
│  └─ Compromised CI/CD could inject malicious code           │
│                                                              │
│  Container Images                                            │
│  └─ Base images may contain vulnerabilities                 │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 1. AI Model Security

### Model Provider Assessment

| Factor | Questions to Ask |
|--------|------------------|
| Data handling | How is my data used? Is it used for training? |
| Isolation | Is my session isolated from others? |
| Logging | What is logged? For how long? |
| Compliance | SOC 2, GDPR, HIPAA compliance? |
| Availability | What's the SLA? Failover options? |

### Trust Boundaries

```
┌──────────────────────────────────────────────────────────┐
│                    YOUR TRUST BOUNDARY                    │
│                                                           │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ Your Code + Prompts + Context                        │ │
│  │                                                       │ │
│  │  ⚠️ SENSITIVE DATA NEVER CROSSES THIS LINE ⚠️       │ │
│  └─────────────────────────────────────────────────────┘ │
│                          │                                │
│                          ▼                                │
│  ┌─────────────────────────────────────────────────────┐ │
│  │ AI Provider (External)                               │ │
│  │ • Treated as untrusted third party                  │ │
│  │ • Output is untrusted input                         │ │
│  │ • No production secrets in prompts                  │ │
│  └─────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### Prompt Security Rules

| Rule | Description |
|------|-------------|
| No real secrets | Never include API keys, passwords, tokens |
| No PII | Never include real user data |
| Sanitize context | Remove sensitive info from code context |
| Minimize data | Only send what's necessary |

---

## 2. Dependency Security

### The Typosquatting Problem

AI may suggest packages that:
- Don't exist (hallucination)
- Are typosquatted versions of real packages
- Are abandoned/unmaintained
- Have known vulnerabilities

### Verification Process

```
AI suggests: npm install colors-utils
              │
              ▼
┌─────────────────────────────────┐
│ 1. Does package exist?          │
│    npm search colors-utils      │
└─────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│ 2. Is it the right package?     │
│    • Check npm/pypi page        │
│    • Verify author/org          │
│    • Check download stats       │
└─────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│ 3. Is it maintained?            │
│    • Last publish date          │
│    • Open issues/PRs            │
│    • Security advisories        │
└─────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│ 4. Is it safe?                  │
│    • snyk/npm audit             │
│    • License check              │
│    • Source review if small     │
└─────────────────────────────────┘
```

### Package Verification Checklist

Before adding any AI-suggested dependency:

- [ ] Package exists on official registry
- [ ] Package name matches exactly (no typos)
- [ ] Publisher is verified/trusted
- [ ] Downloads > 1000/week (for npm) or similar
- [ ] Last update within 12 months
- [ ] No critical vulnerabilities
- [ ] License is acceptable
- [ ] Not deprecated

### Approved Package Lists

Maintain lists of pre-approved packages:

```yaml
# approved-packages.yml
packages:
  npm:
    - lodash
    - axios
    - express
    - jest
    # ... verified packages

  pip:
    - requests
    - flask
    - pytest
    - sqlalchemy
    # ... verified packages

policy:
  unknown_package: require_review
  deprecated_package: block
  vulnerable_package: block_high_critical
```

---

## 3. Lockfile Management

### Why Lockfiles Matter

- Ensure reproducible builds
- Prevent supply chain attacks via version drift
- Enable audit trail of dependency changes

### Lockfile Requirements

| Package Manager | Lockfile | Commit? |
|-----------------|----------|---------|
| npm | `package-lock.json` | Yes |
| yarn | `yarn.lock` | Yes |
| pnpm | `pnpm-lock.yaml` | Yes |
| pip | `requirements.txt` (pinned) or `poetry.lock` | Yes |
| go | `go.sum` | Yes |
| composer | `composer.lock` | Yes |

### Lockfile CI Checks

```yaml
lockfile-check:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4

    - name: Check lockfile exists
      run: |
        if [ ! -f package-lock.json ]; then
          echo "ERROR: package-lock.json missing"
          exit 1
        fi

    - name: Check lockfile in sync
      run: npm ci --ignore-scripts

    - name: Detect lockfile changes
      run: |
        if git diff --name-only origin/main | grep -q "package-lock.json"; then
          echo "⚠️ Lockfile changed - verify dependency updates"
        fi
```

---

## 4. Build Pipeline Security

### CI/CD Hardening

| Control | Implementation |
|---------|----------------|
| Minimal permissions | GitHub Actions: `permissions: read-all` by default |
| Pin action versions | `uses: actions/checkout@v4.1.1` (not `@v4`) |
| Verify checksums | Hash verification for downloaded tools |
| Isolated builds | No network access during build where possible |
| Signed artifacts | Sign release artifacts |

### Secure Pipeline Example

```yaml
name: Secure Build

on:
  push:
    branches: [main]

permissions:
  contents: read
  packages: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1

      - name: Setup Node (pinned)
        uses: actions/setup-node@60edb5dd545a775178f52524783378180af0d1f8  # v4.0.2
        with:
          node-version-file: '.nvmrc'
          cache: 'npm'

      - name: Install with integrity check
        run: npm ci --ignore-scripts

      - name: Build
        run: npm run build

      - name: Generate SBOM
        run: npx @cyclonedx/cyclonedx-npm --output-file sbom.json
```

---

## 5. Container Image Security

### Base Image Selection

| Criterion | Guideline |
|-----------|-----------|
| Source | Official images or verified publishers |
| Size | Minimal images (alpine, distroless) |
| Updates | Regularly updated base images |
| Signatures | Verify image signatures |

### Container Scanning

```yaml
container-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4

    - name: Build image
      run: docker build -t myapp:${{ github.sha }} .

    - name: Scan with Trivy
      uses: aquasecurity/trivy-action@master
      with:
        image-ref: 'myapp:${{ github.sha }}'
        severity: 'CRITICAL,HIGH'
        exit-code: '1'
```

### Dockerfile Security

```dockerfile
# Use specific digest, not just tag
FROM node:20-alpine@sha256:abc123...

# Don't run as root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Minimize layers and installed packages
RUN apk add --no-cache dumb-init

# Don't copy sensitive files
COPY --chown=appuser:appgroup package*.json ./
RUN npm ci --only=production
COPY --chown=appuser:appgroup src/ ./src/

# Use non-root port
EXPOSE 3000

ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["node", "src/index.js"]
```

---

## 6. Software Bill of Materials (SBOM)

### Why SBOM

- Inventory of all components
- Required for compliance (EO 14028)
- Enables rapid vulnerability response
- Audit trail for supply chain

### SBOM Generation

| Tool | Format | Languages |
|------|--------|-----------|
| `cyclonedx-npm` | CycloneDX | JavaScript |
| `cyclonedx-python` | CycloneDX | Python |
| `syft` | CycloneDX, SPDX | Multi-language |
| `trivy` | CycloneDX, SPDX | Multi-language |

### SBOM in CI

```yaml
generate-sbom:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4

    - name: Generate SBOM
      uses: anchore/sbom-action@v0
      with:
        artifact-name: sbom.spdx.json
        format: spdx-json

    - name: Upload SBOM
      uses: actions/upload-artifact@v4
      with:
        name: sbom
        path: sbom.spdx.json
```

---

## Implementation Checklist

### Immediate

- [ ] No secrets in AI prompts
- [ ] Lockfiles committed and checked
- [ ] Dependency scanning in CI

### Short-term

- [ ] Package approval process
- [ ] Pin CI action versions
- [ ] Container image scanning

### Long-term

- [ ] SBOM generation
- [ ] Signed releases
- [ ] AI provider security assessment

---

## Metrics

| Metric | Target |
|--------|--------|
| Dependencies from approved list | > 95% |
| Dependencies with known vulnerabilities | 0 critical/high |
| Lockfile coverage | 100% |
| SBOM completeness | 100% |

---

*Supply chain security is often overlooked but critical. AI-generated code can introduce risks at every level.*
