# Automated Gates

> Pre-review quality checks that run automatically on AI-generated code.

---

## Purpose

Automated gates serve two goals:
1. **Reduce reviewer burden** — Catch obvious issues before human review
2. **Ensure consistency** — Same checks on every merge request

---

## Gate Categories

### Gate 1: Syntax & Style

**What it checks:**
- Code compiles/parses without errors
- Linting rules pass
- Formatting is consistent
- No syntax errors in configurations

**Tools by language:**

| Language | Tools |
|----------|-------|
| Python | `ruff`, `black`, `mypy` |
| JavaScript/TypeScript | `eslint`, `prettier`, `tsc` |
| PHP | `phpstan`, `php-cs-fixer` |
| Go | `golint`, `gofmt`, `go vet` |
| Rust | `clippy`, `rustfmt` |

**Pass criteria:** Zero errors (warnings allowed with justification)

---

### Gate 2: Tests

**What it checks:**
- All existing tests pass
- New code has test coverage
- No flaky tests introduced

**Metrics:**

| Metric | Minimum | Target |
|--------|---------|--------|
| Test pass rate | 100% | 100% |
| New code coverage | 60% | 80% |
| Overall coverage delta | -0% | +1% |

**Pass criteria:** All tests pass, coverage not decreased

---

### Gate 3: Security (Critical)

**What it checks:**
- No hardcoded secrets
- No known vulnerable dependencies
- No OWASP Top 10 patterns
- No dangerous functions

**Tools:**

| Category | Tools |
|----------|-------|
| Secrets detection | `gitleaks`, `trufflehog`, `detect-secrets` |
| SAST | `semgrep`, `bandit` (Python), `brakeman` (Ruby) |
| Dependency scan | `snyk`, `npm audit`, `safety` (Python) |
| Container scan | `trivy`, `grype` |

**Pass criteria:** Zero HIGH/CRITICAL findings

**Findings classification:**

| Severity | Action |
|----------|--------|
| CRITICAL | Block merge, immediate fix required |
| HIGH | Block merge, fix required |
| MEDIUM | Warning, reviewer decides |
| LOW | Informational only |

---

### Gate 4: Quality Score

Composite score based on multiple factors:

```
Quality Score = (
  syntax_score * 0.2 +
  test_score * 0.3 +
  security_score * 0.3 +
  complexity_score * 0.1 +
  documentation_score * 0.1
)
```

**Scoring rules:**

| Component | Calculation |
|-----------|-------------|
| Syntax | 100 if pass, 0 if fail |
| Tests | (tests_passed / tests_total) * 100 |
| Security | 100 - (critical * 50 + high * 20 + medium * 5) |
| Complexity | 100 - (cyclomatic_complexity_delta * 2) |
| Documentation | 100 if docstrings present, 50 if missing |

**Pass criteria:** Score >= 80

---

## Gate Pipeline

```yaml
# Example GitHub Actions workflow
name: Automated Gates

on:
  pull_request:
    branches: [main, develop]

jobs:
  syntax-style:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Lint
        run: make lint
      - name: Type check
        run: make typecheck

  tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: make test
      - name: Coverage report
        run: make coverage

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Secrets scan
        run: gitleaks detect --source . --verbose
      - name: SAST scan
        run: semgrep --config auto .
      - name: Dependency scan
        run: snyk test

  quality-score:
    needs: [syntax-style, tests, security]
    runs-on: ubuntu-latest
    steps:
      - name: Calculate score
        run: ./scripts/calculate-quality-score.sh
      - name: Post score to PR
        run: ./scripts/post-pr-comment.sh
```

---

## Gate Results Format

Posted as PR comment:

```markdown
## Automated Gates Results

| Gate | Status | Details |
|------|--------|---------|
| Syntax & Style | PASS | 0 errors, 3 warnings |
| Tests | PASS | 47/47 passed, 82% coverage (+2%) |
| Security | PASS | 0 critical, 0 high, 1 medium |
| Quality Score | **87/100** | Above threshold (80) |

### Security Findings
- MEDIUM: Possible SQL injection in `src/api.py:42` (semgrep)
  - Reviewer action: Verify parameterized query is used

### Recommendations
- Consider adding docstring to `process_data()` function
- Cyclomatic complexity increased by 3 in `handler.py`

---
Ready for human review.
```

---

## Handling Failures

### Syntax Failure

```
Action: Block PR, notify agent
Agent response: Fix and push
Re-run: Automatic on push
```

### Test Failure

```
Action: Block PR, identify failing tests
Agent response:
  - If new test: Fix implementation
  - If existing test: Investigate regression
Re-run: Automatic on push
```

### Security Failure

```
CRITICAL/HIGH:
  Action: Block PR, flag for security review
  Agent response: Not allowed to fix security issues autonomously
  Human action: Required

MEDIUM:
  Action: Warning, reviewer decides
  Agent response: Add justification comment if false positive
```

---

## Custom Rules

### Adding Project-Specific Rules

```yaml
# .github/gate-rules.yml
rules:
  # Block any changes to auth module without security review
  - name: auth-changes-require-review
    paths: ["src/auth/*", "src/security/*"]
    action: require_review
    reviewers: ["@security-team"]

  # Warn on database schema changes
  - name: schema-change-warning
    paths: ["migrations/*"]
    action: warn
    message: "Database schema change detected. Ensure migration is reversible."

  # Block direct changes to production config
  - name: no-prod-config-changes
    paths: ["config/production/*"]
    action: block
    message: "Production config changes require manual deployment."
```

---

## Metrics

### Gate Metrics to Track

| Metric | Purpose |
|--------|---------|
| Pass rate by gate | Identify problematic areas |
| Average quality score | Track improvement over time |
| Time to pass all gates | Identify slow checks |
| False positive rate | Tune sensitivity |
| Security findings trend | Monitor security posture |

### Sample Dashboard

```
╔════════════════════════════════════════════════════════════╗
║              AUTOMATED GATES - LAST 7 DAYS                 ║
╠════════════════════════════════════════════════════════════╣
║ PRs Processed: 45                                          ║
║ First-Pass Rate: 78% (35/45)                              ║
║ Average Quality Score: 86                                  ║
╠════════════════════════════════════════════════════════════╣
║ Pass Rates by Gate:                                        ║
║   Syntax & Style: 95% ████████████████████░                ║
║   Tests:          89% ██████████████████░░░                ║
║   Security:       82% █████████████████░░░░                ║
╠════════════════════════════════════════════════════════════╣
║ Top Failure Reasons:                                       ║
║   1. Missing test coverage (8)                            ║
║   2. Linting errors (5)                                   ║
║   3. Hardcoded credentials (3)                            ║
╚════════════════════════════════════════════════════════════╝
```

---

## Implementation Checklist

- [ ] CI/CD pipeline configured
- [ ] Linting tools installed and configured
- [ ] Test framework with coverage reporting
- [ ] Security scanning tools integrated
- [ ] Quality score calculation script
- [ ] PR comment automation
- [ ] Custom rules defined
- [ ] Metrics dashboard created

---

*Automated gates are the highest-value, lowest-risk component of the Factory Model. Implement these first.*
