# Security Gates

> Automated security checks for AI-generated code.

---

## Gate Overview

Security gates run automatically on every merge request containing AI-generated code.

```
MR Opened
    │
    ▼
┌─────────────────┐
│ SECRETS SCAN    │ → Block if secrets found
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ SAST SCAN       │ → Block if HIGH/CRITICAL
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ DEPENDENCY SCAN │ → Block if vulnerable deps
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ LICENSE CHECK   │ → Warn on risky licenses
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ SECURITY REVIEW │ → Required for sensitive areas
└─────────────────┘
    │
    ▼
Ready for Review
```

---

## Gate 1: Secrets Detection

### Purpose
Prevent credentials, API keys, and other secrets from being committed.

### Tools

| Tool | Strengths |
|------|-----------|
| `gitleaks` | Fast, comprehensive patterns, CI-friendly |
| `trufflehog` | Entropy analysis, verified secrets |
| `detect-secrets` | Baseline support, customizable |

### Configuration (gitleaks)

```toml
# .gitleaks.toml
[extend]
useDefault = true

[[rules]]
id = "custom-api-key"
description = "Custom API Key Pattern"
regex = '''(?i)api[_-]?key\s*[=:]\s*['"]?[\w-]{32,}['"]?'''
tags = ["key", "api"]

[allowlist]
paths = [
  '''\.gitleaks\.toml$''',
  '''test/fixtures/''',
]
```

### CI Integration

```yaml
secrets-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0
    - uses: gitleaks/gitleaks-action@v2
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Handling Findings

| Finding Type | Action |
|--------------|--------|
| Real secret | Block merge, revoke secret immediately |
| False positive | Add to allowlist with justification |
| Test credential | Move to test fixtures, add to allowlist |

---

## Gate 2: Static Application Security Testing (SAST)

### Purpose
Detect security vulnerabilities in source code.

### Tools by Language

| Language | Tools |
|----------|-------|
| Multi-language | `semgrep`, `SonarQube` |
| Python | `bandit`, `safety` |
| JavaScript | `eslint-plugin-security`, `njsscan` |
| PHP | `phpstan`, `psalm`, `phan` |
| Java | `spotbugs`, `find-sec-bugs` |
| Go | `gosec`, `staticcheck` |

### Semgrep Configuration

```yaml
# .semgrep.yml
rules:
  - id: sql-injection
    patterns:
      - pattern: cursor.execute($QUERY % ...)
      - pattern: cursor.execute($QUERY.format(...))
      - pattern: cursor.execute(f"...")
    message: "Possible SQL injection. Use parameterized queries."
    severity: ERROR
    languages: [python]

  - id: hardcoded-password
    pattern: password = "..."
    message: "Hardcoded password detected"
    severity: ERROR
    languages: [python, javascript, java]
```

### Severity Classification

| Severity | Action | Example |
|----------|--------|---------|
| CRITICAL | Block, immediate fix | SQL injection, RCE |
| HIGH | Block, fix required | XSS, auth bypass |
| MEDIUM | Warning, reviewer decides | Info disclosure |
| LOW | Informational | Best practice violation |

### CI Integration

```yaml
sast-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - name: Semgrep
      uses: returntocorp/semgrep-action@v1
      with:
        config: >-
          p/security-audit
          p/owasp-top-ten
```

---

## Gate 3: Dependency Scanning

### Purpose
Identify known vulnerabilities in dependencies.

### Tools

| Tool | Coverage |
|------|----------|
| `snyk` | Multi-language, real-time DB |
| `npm audit` | JavaScript/Node.js |
| `safety` | Python |
| `bundler-audit` | Ruby |
| `cargo audit` | Rust |
| `trivy` | Containers, IaC, multiple languages |

### Configuration (Snyk)

```yaml
# .snyk
version: v1.0.0
ignore:
  SNYK-JS-LODASH-567746:
    - '*':
        reason: 'No user input reaches this code path'
        expires: 2024-06-01
```

### Severity Thresholds

| Severity | Policy |
|----------|--------|
| Critical | Block merge, fix immediately |
| High | Block merge, fix within 1 day |
| Medium | Warning, fix within 1 week |
| Low | Informational, fix in next release |

### CI Integration

```yaml
dependency-scan:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - name: Snyk test
      uses: snyk/actions/node@master
      env:
        SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      with:
        args: --severity-threshold=high
```

---

## Gate 4: License Compliance

### Purpose
Ensure dependencies have acceptable licenses.

### License Categories

| Category | Licenses | Action |
|----------|----------|--------|
| Permissive | MIT, Apache 2.0, BSD | Allow |
| Weak copyleft | LGPL, MPL | Review required |
| Strong copyleft | GPL, AGPL | Block (usually) |
| Unknown | Unlicensed | Block |

### Tools

| Tool | Features |
|------|----------|
| `license-checker` | npm packages |
| `pip-licenses` | Python packages |
| `FOSSA` | Multi-language, enterprise |
| `Snyk` | Included in security scan |

### CI Integration

```yaml
license-check:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - run: npm ci
    - run: npx license-checker --failOn "GPL;AGPL;UNLICENSED"
```

---

## Gate 5: Sensitive Area Review

### Purpose
Require human security review for changes to sensitive code.

### Sensitive Areas

| Area | Files | Required Reviewers |
|------|-------|-------------------|
| Authentication | `src/auth/*`, `**/login*` | @security-team |
| Authorization | `**/permission*`, `**/rbac*` | @security-team |
| Cryptography | `**/crypto*`, `**/encrypt*` | @security-team |
| Payment | `**/payment*`, `**/billing*` | @security-team, @finance |
| PII handling | `**/user*`, `**/profile*` | @privacy-team |

### CODEOWNERS Configuration

```
# .github/CODEOWNERS
/src/auth/           @security-team
/src/crypto/         @security-team
**/password*         @security-team
**/secrets*          @security-team
/src/payment/        @security-team @finance-team
```

---

## Gate Pipeline

### Complete CI Configuration

```yaml
name: Security Gates

on:
  pull_request:
    branches: [main, develop]

jobs:
  secrets-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: gitleaks/gitleaks-action@v2

  sast-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: returntocorp/semgrep-action@v1
        with:
          config: p/security-audit

  dependency-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}

  security-summary:
    needs: [secrets-scan, sast-scan, dependency-scan]
    runs-on: ubuntu-latest
    steps:
      - name: Generate security report
        run: |
          echo "## Security Scan Results" >> $GITHUB_STEP_SUMMARY
          echo "- Secrets: ✓ Passed" >> $GITHUB_STEP_SUMMARY
          echo "- SAST: ✓ Passed" >> $GITHUB_STEP_SUMMARY
          echo "- Dependencies: ✓ Passed" >> $GITHUB_STEP_SUMMARY
```

---

## Handling Failures

### Secrets Found

```
1. Block merge immediately
2. Alert security team
3. Developer must:
   - Remove secret from code
   - Rotate the exposed secret
   - Add test to prevent recurrence
4. Security team verifies rotation
5. Re-run scan
```

### SAST Finding

```
1. Evaluate severity
2. For CRITICAL/HIGH:
   - Block merge
   - Developer fixes or justifies
   - Security review if complex
3. For MEDIUM:
   - Reviewer decides
   - May proceed with tracking issue
4. For LOW:
   - Informational only
```

### Vulnerable Dependency

```
1. Check if upgrade available
2. If yes: upgrade and re-scan
3. If no upgrade:
   - Evaluate exploitability
   - Document mitigation
   - Create tracking issue
   - Set review date
```

---

## Metrics

### Security Gate Metrics

| Metric | Target | Alert |
|--------|--------|-------|
| Gate pass rate | > 90% | < 85% |
| CRITICAL findings | 0 | > 0 |
| Time to remediate HIGH | < 1 day | > 2 days |
| False positive rate | < 5% | > 15% |

### Tracking Dashboard

```
╔═══════════════════════════════════════════════════════════╗
║            SECURITY GATES - LAST 30 DAYS                  ║
╠═══════════════════════════════════════════════════════════╣
║ Scans: 156 MRs                                            ║
║ Pass Rate: 94% (147/156)                                  ║
╠═══════════════════════════════════════════════════════════╣
║ Findings by Type:                                         ║
║   Secrets: 3 (all remediated)                            ║
║   SAST HIGH: 5 (all remediated)                          ║
║   Vulnerable deps: 8 (6 upgraded, 2 mitigated)          ║
╠═══════════════════════════════════════════════════════════╣
║ Top Finding Categories:                                   ║
║   1. Missing input validation (4)                        ║
║   2. Outdated dependency (3)                             ║
║   3. Hardcoded string (2)                                ║
╚═══════════════════════════════════════════════════════════╝
```

---

## Implementation Checklist

- [ ] Secrets scanning configured and tested
- [ ] SAST tool selected and configured
- [ ] Dependency scanning integrated
- [ ] License checking enabled
- [ ] CODEOWNERS for sensitive areas
- [ ] CI pipeline configured
- [ ] Team trained on handling findings
- [ ] Metrics collection active

---

*Security gates are non-negotiable. No AI-generated code ships without passing all gates.*
