# Factory Model — AI Code Generation at Scale

> From single developer + AI to industrial code generation pipeline.

This module extends the core agentic framework to support team-scale and batch code generation scenarios.

---

## When You Need This

The base framework (checkpoints, escalation, roles) works well for:
- 1 developer + 1 AI session
- Synchronous, interactive work
- Real-time feedback loops

The Factory Model is for:
- Multiple developers + multiple AI agents
- Batch/async code generation (overnight runs)
- High-volume merge request pipelines
- Metrics-driven optimization

---

## Components

| Component | Purpose | Document |
|-----------|---------|----------|
| **Multi-Agent Orchestration** | Coordinate N agents on M tasks | [MULTI_AGENT_ORCHESTRATION.md](MULTI_AGENT_ORCHESTRATION.md) |
| **Batch Pipeline** | Async code generation workflow | [BATCH_PIPELINE.md](BATCH_PIPELINE.md) |
| **Automated Gates** | Pre-review quality checks | [AUTOMATED_GATES.md](AUTOMATED_GATES.md) |
| **Metrics Dashboard** | Track and optimize performance | [METRICS_DASHBOARD.md](METRICS_DASHBOARD.md) |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        TICKET QUEUE                              │
│  [Issue #1] [Issue #2] [Issue #3] ... [Issue #N]                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     ORCHESTRATOR                                 │
│  • Assigns tickets to agents based on capacity                  │
│  • Manages agent pool                                           │
│  • Handles failures and retries                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
    ┌──────────┐        ┌──────────┐        ┌──────────┐
    │ Agent 1  │        │ Agent 2  │        │ Agent N  │
    │ C0→C1→C2 │        │ C0→C1→C2 │        │ C0→C1→C2 │
    └──────────┘        └──────────┘        └──────────┘
          │                   │                   │
          ▼                   ▼                   ▼
┌─────────────────────────────────────────────────────────────────┐
│                    AUTOMATED GATES                               │
│  • Static analysis (lint, type check)                           │
│  • Security scan (SAST, secrets detection)                      │
│  • Test execution                                               │
│  • Quality score calculation                                    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    REVIEW QUEUE                                  │
│  Prioritized by: quality score, risk level, reviewer load       │
│  [MR #1: 92/100] [MR #2: 87/100] [MR #3: 95/100]               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    METRICS COLLECTOR                             │
│  • Time to merge                                                │
│  • Acceptance rate                                              │
│  • Rework ratio                                                 │
│  • Agent performance                                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## Integration with Core Framework

The Factory Model builds on top of the core framework:

| Core Component | Factory Extension |
|----------------|-------------------|
| Checkpoints (C0-C3) | Same checkpoints, automated verification |
| Escalation Rules | Automated escalation detection → human queue |
| Roles | Manager role becomes Orchestrator |
| Quality Gates | Automated gates before human review |

---

## Adoption Path

### Level 1: Single Agent + Automation
- Add automated gates to existing workflow
- Track basic metrics
- Same human oversight

### Level 2: Multi-Agent (Small Team)
- 2-3 concurrent agents
- Simple round-robin assignment
- Shared review queue

### Level 3: Full Factory
- N agents with orchestrator
- Batch overnight generation
- Metrics-driven optimization
- SLA-based prioritization

---

## Prerequisites

Before implementing Factory Model:

1. **Core framework working** — Checkpoints and escalation rules tested
2. **CI/CD pipeline** — Automated tests and deployments
3. **Code review process** — Established review culture
4. **Metrics baseline** — Know your current velocity and quality

---

## Next Steps

1. Start with [Automated Gates](AUTOMATED_GATES.md) — highest value, lowest risk
2. Add [Metrics Dashboard](METRICS_DASHBOARD.md) — establish baselines
3. Then [Batch Pipeline](BATCH_PIPELINE.md) if needed
4. Finally [Multi-Agent Orchestration](MULTI_AGENT_ORCHESTRATION.md) for scale

---

*The Factory Model is optional. Many teams get excellent results with the core framework alone.*
