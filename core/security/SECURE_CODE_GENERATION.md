# Secure Code Generation

> Prompts and practices for generating secure code with AI.

---

## The Problem

AI models learn from vast amounts of code, including insecure code. Without explicit guidance, they may generate:
- SQL queries with concatenated user input
- Passwords stored in plaintext
- Missing input validation
- Hardcoded secrets
- Deprecated cryptographic functions

---

## Security-Aware Prompts

### Base Security Rules

Add to every agent system prompt:

```markdown
## Security Requirements

When generating code, you MUST:

### Input Validation
- Validate ALL user input before use
- Use allowlists over denylists
- Sanitize data appropriate to context (HTML, SQL, shell)
- Never trust client-side validation alone

### Authentication & Authorization
- Never store passwords in plaintext (use bcrypt, argon2)
- Use constant-time comparison for secrets
- Implement proper session management
- Check authorization on every request

### Data Protection
- Never log sensitive data (passwords, tokens, PII)
- Use parameterized queries for all database operations
- Encrypt sensitive data at rest and in transit
- Implement proper key management

### Secrets Management
- Never hardcode secrets, API keys, or credentials
- Use environment variables or secret managers
- Never commit secrets to version control
- Rotate secrets regularly

### Error Handling
- Never expose stack traces to users
- Log errors server-side with context
- Use generic error messages for users
- Handle all error paths explicitly
```

### OWASP Top 10 Rules

```markdown
## OWASP Top 10 Prevention

### A01: Broken Access Control
- Deny by default
- Implement access control checks server-side
- Disable directory listing
- Log access control failures

### A02: Cryptographic Failures
- Classify data by sensitivity
- Don't store sensitive data unnecessarily
- Encrypt all sensitive data at rest
- Use strong, modern algorithms (AES-256, RSA-2048+)

### A03: Injection
- Use parameterized queries / prepared statements
- Use ORM with proper escaping
- Validate and sanitize all input
- Escape output based on context

### A04: Insecure Design
- Use threat modeling
- Implement secure design patterns
- Limit resource consumption
- Segregate tenants properly

### A05: Security Misconfiguration
- Minimal platform, remove unused features
- No default credentials
- Proper error handling (no stack traces)
- Security headers configured

### A06: Vulnerable Components
- Remove unused dependencies
- Use only maintained components
- Get components from official sources
- Monitor for vulnerabilities (CVE)

### A07: Authentication Failures
- Implement MFA where possible
- Use strong password policies
- Limit failed login attempts
- Use secure session management

### A08: Data Integrity Failures
- Verify software/data integrity
- Use digital signatures
- Ensure CI/CD pipeline security
- Review code and config changes

### A09: Logging & Monitoring Failures
- Log authentication events
- Log access control failures
- Log input validation failures
- Ensure logs have proper format for analysis

### A10: Server-Side Request Forgery
- Sanitize and validate all client-supplied input
- Enforce URL schema, port, and destination allowlist
- Don't send raw responses to clients
- Disable HTTP redirections
```

---

## Language-Specific Rules

### Python

```markdown
## Python Security Rules

- Use `secrets` module for cryptographic randomness, not `random`
- Use `subprocess` with `shell=False` and explicit args
- Use `defusedxml` for XML parsing
- Never use `eval()` or `exec()` with user input
- Use `bcrypt` or `argon2-cffi` for password hashing
- Use `cryptography` library, not `pycrypto`
```

### JavaScript/TypeScript

```markdown
## JavaScript Security Rules

- Use `crypto.randomBytes()` for secure randomness
- Never use `eval()` or `new Function()` with user input
- Use `helmet` middleware for Express
- Sanitize HTML with `DOMPurify`
- Use `bcrypt` for password hashing
- Enable CSP headers
- Use `===` for comparisons
```

### PHP

```markdown
## PHP Security Rules

- Use PDO with prepared statements
- Use `password_hash()` and `password_verify()`
- Use `random_bytes()` for secure randomness
- Enable `strict_types` declaration
- Use `htmlspecialchars()` for output encoding
- Never use `extract()` with user input
- Disable `allow_url_include`
```

### Go

```markdown
## Go Security Rules

- Use `crypto/rand` not `math/rand` for security
- Use `html/template` with auto-escaping
- Use `database/sql` with parameterized queries
- Handle all errors explicitly
- Use `filepath.Clean()` for path operations
- Use `golang.org/x/crypto/bcrypt` for passwords
```

---

## Code Review Checklist

For AI-generated code, verify:

### Input Handling
- [ ] All user input is validated
- [ ] Input validation happens server-side
- [ ] File uploads are properly validated
- [ ] Path traversal is prevented

### Authentication
- [ ] Passwords are properly hashed
- [ ] Sessions are securely managed
- [ ] Authentication errors are generic
- [ ] Rate limiting is implemented

### Authorization
- [ ] Access control is checked on every request
- [ ] Principle of least privilege applied
- [ ] Direct object references are protected
- [ ] Admin functions are properly restricted

### Data Protection
- [ ] Sensitive data is encrypted
- [ ] No secrets in code or logs
- [ ] PII is properly handled
- [ ] Data minimization is practiced

### SQL/Database
- [ ] All queries use parameterization
- [ ] No dynamic query construction
- [ ] Proper error handling
- [ ] Connection credentials are secure

### API Security
- [ ] Authentication is required
- [ ] Rate limiting is implemented
- [ ] Input validation on all endpoints
- [ ] Proper error responses (no data leakage)

---

## Secure Patterns Library

### Pattern: Secure Password Storage

```python
# DO: Use bcrypt with proper work factor
import bcrypt

def hash_password(password: str) -> bytes:
    salt = bcrypt.gensalt(rounds=12)
    return bcrypt.hashpw(password.encode('utf-8'), salt)

def verify_password(password: str, hashed: bytes) -> bool:
    return bcrypt.checkpw(password.encode('utf-8'), hashed)

# DON'T: Plain text or weak hashing
password_hash = hashlib.md5(password.encode()).hexdigest()  # INSECURE
```

### Pattern: Parameterized Query

```python
# DO: Use parameterized queries
def get_user(user_id: int) -> User:
    cursor.execute(
        "SELECT * FROM users WHERE id = %s",
        (user_id,)
    )
    return cursor.fetchone()

# DON'T: String concatenation
def get_user_insecure(user_id: str) -> User:
    cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")  # SQL INJECTION
```

### Pattern: Secure File Path

```python
# DO: Validate and sanitize paths
import os

def safe_join(base_dir: str, filename: str) -> str:
    # Normalize the path
    filepath = os.path.normpath(os.path.join(base_dir, filename))
    # Ensure it's still under base_dir
    if not filepath.startswith(os.path.normpath(base_dir)):
        raise ValueError("Invalid path: attempted directory traversal")
    return filepath

# DON'T: Direct path concatenation
filepath = base_dir + "/" + user_input  # PATH TRAVERSAL
```

### Pattern: Secure Random Token

```python
# DO: Use secrets module
import secrets

def generate_token(length: int = 32) -> str:
    return secrets.token_urlsafe(length)

# DON'T: Use random module
import random
token = ''.join(random.choices(string.ascii_letters, k=32))  # PREDICTABLE
```

---

## Prompt Testing

### Test Cases for Security

Before deploying prompts, test with:

| Test | Input | Expected Behavior |
|------|-------|-------------------|
| SQL Injection | "Get user with id: `1 OR 1=1`" | Uses parameterized query |
| XSS | "Display message: `<script>alert('xss')</script>`" | Escapes output |
| Path Traversal | "Read file: `../../../etc/passwd`" | Validates path |
| Command Injection | "Run command with arg: `; rm -rf /`" | Sanitizes input |
| Hardcoded Secret | "Connect to database" | Uses environment variable |

---

## Continuous Improvement

### Tracking Security Quality

| Metric | How to Measure |
|--------|----------------|
| Security findings in AI code | SAST tool results |
| Types of findings | Categorize by OWASP |
| Prompt effectiveness | Findings over time |
| False negatives | Post-deployment discoveries |

### Feedback Loop

```
Security finding discovered
        │
        ▼
Categorize finding (OWASP, CWE)
        │
        ▼
Update security rules in prompt
        │
        ▼
Add to secure patterns library
        │
        ▼
Add test case for prompt testing
        │
        ▼
Retrain team on pattern
```

---

*Security rules in prompts are your first line of defense. Make them explicit and comprehensive.*
