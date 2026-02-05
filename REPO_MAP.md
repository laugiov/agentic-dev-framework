# Repository Map

> **Find your path in 60 seconds.**

A **methodology framework for AI-assisted software development** — checkpoints, escalation rules, and human oversight to ship quality code faster.

---

## Who Is This For?

| Profile | Start Here | Then Read |
|---------|------------|-----------|
| **Apply to Your Project** | [Bootstrap Guide](runtime/golden-path/BOOTSTRAP_NEW_PROJECT.md) | [Checkpoints](core/spec/CHECKPOINTS.md) → [System Prompt](runtime/prompts/CLAUDE_CODE_SYSTEM.md) |
| **Learn the Workflow** | [Agent Quickstart](runtime/golden-path/QUICKSTART_AGENT.md) | [Escalation Rules](core/agent-operating-model/ESCALATION_RULES.md) |
| **Scale to Teams** | [Factory Model](core/factory-model/) | [Multi-Agent](core/factory-model/MULTI_AGENT_ORCHESTRATION.md) → [Metrics](core/factory-model/METRICS_DASHBOARD.md) |
| **Secure AI Code** | [Security Module](core/security/) | [Security Gates](core/security/SECURITY_GATES.md) → [Audit Trail](core/security/AUDIT_TRAIL.md) |
| **See Examples** | [Case Studies](case-studies/) | [PHP/Symfony Example](examples/php-symfony-k8s/) |

---

## Repository Structure

```
ai-driven-dev-instructions/
│
├── [Core Framework]
│   ├── core/
│   │   ├── agent-operating-model/   # Roles, workflow, escalation
│   │   ├── spec/                    # Checkpoints, templates
│   │   ├── security/                # DevSecOps for AI code [NEW]
│   │   │   ├── SECURE_CODE_GENERATION.md
│   │   │   ├── SECURITY_GATES.md
│   │   │   ├── SUPPLY_CHAIN_SECURITY.md
│   │   │   └── AUDIT_TRAIL.md
│   │   └── factory-model/           # Scale to teams [NEW]
│   │       ├── MULTI_AGENT_ORCHESTRATION.md
│   │       ├── BATCH_PIPELINE.md
│   │       ├── AUTOMATED_GATES.md
│   │       └── METRICS_DASHBOARD.md
│   │
│   ├── runtime/
│   │   ├── golden-path/             # Quickstarts, bootstrap
│   │   ├── prompts/                 # System prompts
│   │   └── quality-gates/           # Definition of Done
│   │
│   ├── bench/                       # Benchmark tasks
│   └── tools/                       # Validation utilities
│
├── [Examples]
│   └── php-symfony-k8s/             # Complete reference (52 docs)
│
├── [Case Studies]
│   └── case-studies/                # Real workflow examples
│
└── [Supporting]
    ├── README.md                    # Project overview
    ├── REPO_MAP.md                  # This file
    ├── METHODOLOGY.md               # Core methodology
    ├── GLOSSARY.md                  # Terminology
    ├── CONTRIBUTING.md
    └── LICENSE
```

---

## Quick Start Paths

### Path A: Apply to Your Project (Fastest)

1. [BOOTSTRAP_NEW_PROJECT.md](runtime/golden-path/BOOTSTRAP_NEW_PROJECT.md) — Choose your setup level
2. [CHECKPOINTS.md](core/spec/CHECKPOINTS.md) — Understand the workflow
3. [ESCALATION_RULES.md](core/agent-operating-model/ESCALATION_RULES.md) — Know when to stop

**Minimum setup**: 2 files (Micro option)

### Path B: Understand the Methodology

1. [README.md](README.md) — What problem this solves
2. [QUICKSTART_AGENT.md](runtime/golden-path/QUICKSTART_AGENT.md) — The core workflow
3. [Case Studies](case-studies/) — See real examples

### Path C: Secure AI-Generated Code

1. [Security README](core/security/README.md) — Overview
2. [SECURE_CODE_GENERATION.md](core/security/SECURE_CODE_GENERATION.md) — Prompts and rules
3. [SECURITY_GATES.md](core/security/SECURITY_GATES.md) — CI/CD integration
4. [AUDIT_TRAIL.md](core/security/AUDIT_TRAIL.md) — Traceability

### Path D: Scale to Teams

1. [Factory Model README](core/factory-model/README.md) — Overview
2. [AUTOMATED_GATES.md](core/factory-model/AUTOMATED_GATES.md) — Start here (highest value)
3. [BATCH_PIPELINE.md](core/factory-model/BATCH_PIPELINE.md) — Overnight generation
4. [MULTI_AGENT_ORCHESTRATION.md](core/factory-model/MULTI_AGENT_ORCHESTRATION.md) — Coordinate agents

### Path E: Benchmark Agent Performance

1. [bench/README.md](bench/README.md) — Benchmark overview
2. [bench/tasks/](bench/tasks/) — 10 benchmark tasks
3. [bench/scoring/](bench/scoring/) — Scoring rubric

---

## Key Concepts

| Term | Definition |
|------|------------|
| **Checkpoint** | Validation gate (C0=scope, C1=plan, C2=implement, C3=PR) |
| **Escalation** | When AI must pause and ask the human |
| **Fast Path** | Minimal workflow for trivial changes (< 10 lines) |
| **Golden Path** | Fastest route from issue to merged PR |
| **Factory Model** | Scale: multiple agents, batch processing, metrics |

See [GLOSSARY.md](GLOSSARY.md) for complete terminology.

---

## Adoption Levels

| Level | What You Get | Files |
|-------|--------------|-------|
| **Micro** | Checkpoints + PR template | 2 |
| **Minimal** | + Issue templates, agent guide | 6 |
| **Full** | + Full operating model, prompts | 15+ |
| **Factory** | + Security, scale, metrics | 25+ |

---

## Component Overview

| Component | Purpose | When to Use |
|-----------|---------|-------------|
| **Checkpoints** | Structure work into stages | Always |
| **Escalation Rules** | Know when AI should ask | Always |
| **Quality Gates** | Define "done" by task type | Always |
| **Security Module** | Secure AI-generated code | When security matters |
| **Factory Model** | Scale beyond 1 dev + 1 AI | Team-scale projects |
| **Benchmarks** | Measure agent performance | Optimization |

---

*Last updated: 2026-02-05*
