# AI-Driven Development Instructions

[![Documentation](https://img.shields.io/badge/docs-59%20files-blue)](./DOCUMENTATION_INDEX.md)
[![Code Examples](https://img.shields.io/badge/examples-500%2B-orange)](./CODE_EXAMPLES_INDEX.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

> **A Security & Platform Engineering blueprint for AI-assisted software delivery.**
>
> This repository demonstrates how to write **unambiguous, auditable, security-by-design documentation** that enables AI coding assistants to implement complex systems **without drifting from architecture, security, and operations standards**.

## What Is This Project?

This repository is a **reference blueprint** to operationalize **secure, consistent delivery** with AI coding assistants: specs, validation checkpoints, security guardrails, and review checklists designed for **enterprise SDLC**.

**The example used**: A cloud-native AI Workflow Processing Platform (PHP 8.3, Symfony 7, Kubernetes).

**The real value**: The methodology for turning security requirements into executable SDLC guardrails.

## What This Is NOT

- Not a prompt library or "magic" AI coding trick
- Not a full product implementation
- It is a **methodology + reference spec set** to make delivery predictable, secure, and auditable with AI assistance

## Hiring Relevance

This repo demonstrates senior-level **Security Engineering / Platform Engineering** capabilities:

- Turning security requirements into **executable SDLC guardrails** (checklists, gates, review criteria)
- Writing **unambiguous specs** that scale across teams and reduce delivery risk
- Designing documentation that supports **auditability**, onboarding, and consistent architecture decisions
- Aligning engineering execution with security-by-design and operational readiness

## Evaluate in 10 Minutes

1. Read [METHODOLOGY.md](METHODOLOGY.md) — the AI-driven documentation methodology
2. Open [02-security/07-security-checklist.md](02-security/07-security-checklist.md) — security-by-design guardrails
3. Review [06-cicd/04-quality-gates.md](06-cicd/04-quality-gates.md) — how controls become pipeline gates
4. Check [05-code-review/02-security-review-checklist.md](05-code-review/02-security-review-checklist.md) — standardized security reviews

## Security & Compliance Focus

Includes security-by-design guidance covering:

- **Zero Trust Architecture**: mTLS, service mesh security, micro-segmentation
- **IAM & Secrets Management**: OAuth2/OIDC, RBAC/ABAC, Vault integration
- **Network & Data Protection**: Encryption at rest/transit, PII handling
- **Incident Response**: Runbooks, escalation procedures, disaster recovery
- **Security Review Checklists**: Pre-deployment validation, pipeline quality gates
- **Compliance Frameworks**: GDPR, SOC2, ISO27001, NIS2 alignment

## Key Principles

### 1. Explicit Over Implicit

Every decision includes justification. AI agents follow documented reasoning, not guesses.

```markdown
Use PostgreSQL 15+ for the following reasons:
- ACID compliance required for financial workflow data
- JSONB support for flexible metadata storage
- Row-level security for multi-tenant isolation
```

### 2. Validation Checkpoints

Every section includes verification criteria for self-validation:

```markdown
## Validation Checkpoint
- [ ] All domain entities use readonly properties
- [ ] Value objects implement equals() method
- [ ] Repository interfaces are in Domain layer
- [ ] PHPStan level 9 passes with no errors
```

### 3. Security Gates as Code

Security requirements become enforceable pipeline gates, not optional guidelines.

## Documentation Structure

```
ai-driven-dev-instructions/
├── METHODOLOGY.md                 # Core methodology (start here)
├── GLOSSARY.md                    # Key terms and definitions
├── LLM_USAGE_GUIDE.md            # AI agent entry point
├── IMPLEMENTATION_ROADMAP.md      # Phased implementation plan
│
├── 01-architecture/               # System design & ADRs
├── 02-security/                   # Security-by-design specs
├── 03-infrastructure/             # Cloud-native infrastructure
├── 04-development/                # Coding standards & practices
├── 05-code-review/                # Review checklists & quality
├── 06-cicd/                       # Pipeline & deployment
├── 07-operations/                 # Monitoring & incident response
└── 08-services/                   # Microservice specifications
```

Full structure: [DOCUMENTATION_INDEX.md](DOCUMENTATION_INDEX.md)

## Example Target Platform

The methodology is demonstrated through a complete platform specification:

| Category | Technology |
|----------|------------|
| Language | PHP 8.3+ / Symfony 7.x |
| Database | PostgreSQL 15+ |
| Message Broker | RabbitMQ 3.12+ |
| Orchestration | Kubernetes 1.28+ / Istio 1.20+ |
| Security | Keycloak, HashiCorp Vault |
| Observability | Prometheus, Grafana, Loki, Tempo |
| CI/CD | GitHub Actions + ArgoCD |

## Metrics

| Metric | Value |
|--------|-------|
| Documentation files | 59 |
| Words | ~213,000 |
| Code examples | 500+ |
| Microservices documented | 7 |
| Cross-references | 200+ |

## Key Differentiators

1. **Security-First**: Security controls embedded in every layer
2. **Auditable**: Every decision justified and traceable
3. **Executable**: Specs translate directly to pipeline gates
4. **LLM-Optimized**: Structured for autonomous agent execution
5. **Enterprise-Grade**: Compliance, operations, and incident response included

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License - See [LICENSE](LICENSE) for details.

## Author

**Laurent Giovannoni**

---

*A methodology for secure, auditable AI-assisted software delivery.*
