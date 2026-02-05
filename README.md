# Agentic Development Framework

[![Documentation](https://img.shields.io/badge/docs-99%20files-blue)](./DOCUMENTATION_INDEX.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

> **A methodology framework for AI-assisted software development.** Structure your AI coding workflow with checkpoints, escalation rules, and human oversight to ship quality code faster.

---

## What Problem This Solves

AI coding assistants can generate code fast, but without structure:
- Developers spend too much time validating AI output
- Quality is inconsistent across sessions
- Security issues slip through
- No clear process for when to trust vs. verify

This framework provides **guardrails** that make AI-assisted development predictable, secure, and scalable.

---

## What This Framework Provides

| Component | Purpose |
|-----------|---------|
| **Checkpoints** (C0→C3) | Structured workflow: Scope → Plan → Implement → PR |
| **Escalation Rules** | When AI must stop and ask the human |
| **Quality Gates** | Automated + human verification at each stage |
| **Security Module** | DevSecOps for AI-generated code |
| **Factory Model** | Scale to multiple agents and batch processing |

---

## Start Here

| Your Goal | Start With |
|-----------|------------|
| **Apply to your project** | [Bootstrap Guide](runtime/golden-path/BOOTSTRAP_NEW_PROJECT.md) |
| Learn the workflow | [Agent Quickstart](runtime/golden-path/QUICKSTART_AGENT.md) |
| Understand checkpoints | [Checkpoints Spec](core/spec/CHECKPOINTS.md) |
| See real examples | [Case Studies](case-studies/) |
| Scale to teams | [Factory Model](core/factory-model/) |
| Secure AI code | [Security Module](core/security/) |

---

## The Core Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    BEFORE ANY WORK                          │
│   Check Escalation Triggers → If any apply → STOP → Ask     │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ C0: SCOPE                                                    │
│ • Understand the task                                       │
│ • Define what's in/out of scope                             │
│ • Identify affected files                                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ C1: PLAN (for non-trivial tasks)                            │
│ • Document approach                                         │
│ • Identify risks                                            │
│ • Get human validation if needed                            │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ C2: IMPLEMENT                                                │
│ • Write code following plan                                 │
│ • Run tests                                                 │
│ • Pass automated gates                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ C3: PR & PROOFS                                              │
│ • Document what was done                                    │
│ • Provide evidence (tests, screenshots)                     │
│ • Human reviews and approves                                │
└─────────────────────────────────────────────────────────────┘
```

---

## Escalation Rules — The Highest Value Component

AI agents must **stop and ask** when:

| Trigger | Example |
|---------|---------|
| Architecture decisions | New service, schema change, new dependency |
| Security concerns | Auth changes, crypto, access control |
| Data handling | PII, migrations, retention policies |
| Breaking changes | API changes, deprecations |
| Uncertainty | Multiple valid approaches, unclear requirements |

See [ESCALATION_RULES.md](core/agent-operating-model/ESCALATION_RULES.md)

---

## Task Size → Workflow

Not every task needs the full workflow:

| Size | Lines | Workflow |
|------|-------|----------|
| **Trivial** | < 10 | Fast Path (C0 + C3 only) |
| **Small** | 10-50 | Lite (C0 + C2 + C3) |
| **Medium** | 50-200 | Full (C0 → C1 → C2 → C3) |
| **Large** | > 200 | Full + Extra Review |

---

## Repository Structure

```
├── core/
│   ├── agent-operating-model/   Roles, escalation, golden rules
│   ├── spec/                    Checkpoints, templates
│   ├── security/                DevSecOps for AI code
│   └── factory-model/           Scale: multi-agent, batch, metrics
│
├── runtime/
│   ├── golden-path/             Quickstarts, bootstrap
│   ├── prompts/                 System prompts for agents
│   └── quality-gates/           Definition of done
│
├── case-studies/                Real workflow examples
├── bench/                       Benchmark tasks
├── examples/php-symfony-k8s/    Complete reference implementation
└── tools/                       Validation utilities
```

---

## Security Module — Your Competitive Edge

AI-generated code needs additional security controls:

| Component | Purpose |
|-----------|---------|
| [Secure Code Generation](core/security/SECURE_CODE_GENERATION.md) | Security-aware prompts, OWASP rules |
| [Security Gates](core/security/SECURITY_GATES.md) | SAST, secrets detection, dependency scanning |
| [Supply Chain Security](core/security/SUPPLY_CHAIN_SECURITY.md) | Dependencies, AI model trust |
| [Audit Trail](core/security/AUDIT_TRAIL.md) | Traceability for all AI-generated code |

---

## Factory Model — Scale to Teams

When you need more than 1 developer + 1 AI:

| Component | Purpose |
|-----------|---------|
| [Multi-Agent Orchestration](core/factory-model/MULTI_AGENT_ORCHESTRATION.md) | Coordinate N agents on M tasks |
| [Batch Pipeline](core/factory-model/BATCH_PIPELINE.md) | Overnight code generation |
| [Automated Gates](core/factory-model/AUTOMATED_GATES.md) | Pre-review quality checks |
| [Metrics Dashboard](core/factory-model/METRICS_DASHBOARD.md) | Track and optimize |

---

## Adoption Levels

| Level | Files | Time to Start |
|-------|-------|---------------|
| **Micro** | 2 files | 10 minutes |
| **Minimal** | 6 files | 30 minutes |
| **Full** | Complete framework | 2 hours |
| **Factory** | + Scale modules | 1 week |

See [Bootstrap Guide](runtime/golden-path/BOOTSTRAP_NEW_PROJECT.md)

---

## Key Principles

### Human at the Checkpoints
AI executes autonomously within bounds. Humans approve at defined gates.

### Escalation Over Assumption
When in doubt, the AI asks. It's faster to clarify than to fix bad code.

### Proportional Process
Trivial tasks get fast path. Complex tasks get full workflow.

### Security by Default
Every line of AI-generated code goes through security gates.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

MIT License — See [LICENSE](LICENSE) for details.

## Author

**Laurent Giovannoni**

---

*A methodology framework for AI-assisted software development — ship quality code faster with human oversight.*
