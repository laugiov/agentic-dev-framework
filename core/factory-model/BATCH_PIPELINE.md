# Batch Pipeline

> Asynchronous code generation for high-volume throughput.

---

## Use Case

The batch pipeline is designed for scenarios like:
- Generate code overnight, review in the morning
- Process backlog of tickets during off-hours
- Maximize throughput when real-time feedback isn't needed

---

## Pipeline Stages

```
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 1: INTAKE                                    [Evening]    │
├─────────────────────────────────────────────────────────────────┤
│ • Collect tickets marked "ready-for-agent"                      │
│ • Validate ticket completeness                                  │
│ • Prioritize and sequence                                       │
│ • Create batch manifest                                         │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 2: GENERATION                                [Overnight]  │
├─────────────────────────────────────────────────────────────────┤
│ • Agents process tickets sequentially                           │
│ • Each ticket: C0 → C1 → C2                                    │
│ • Commits to feature branches                                   │
│ • Escalations queued for morning                               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 3: AUTOMATED GATES                           [Early AM]   │
├─────────────────────────────────────────────────────────────────┤
│ • Run all automated checks                                      │
│ • Calculate quality scores                                      │
│ • Flag security issues                                          │
│ • Generate review summaries                                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STAGE 4: REVIEW QUEUE                              [Morning]    │
├─────────────────────────────────────────────────────────────────┤
│ • MRs ready with quality scores                                 │
│ • Escalations ready with context                                │
│ • Reviewers start with highest-priority                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## Batch Manifest

Created at the start of each batch run:

```yaml
batch_id: "2024-01-15-overnight"
created_at: "2024-01-15T18:00:00Z"
config:
  max_tickets: 20
  timeout_per_ticket: 30m
  parallel_agents: 3

tickets:
  - id: "PROJ-142"
    priority: high
    complexity: medium
    estimated_files: ["src/auth.py", "tests/test_auth.py"]
    dependencies: []

  - id: "PROJ-138"
    priority: medium
    complexity: small
    estimated_files: ["src/utils.py"]
    dependencies: []

  - id: "PROJ-145"
    priority: medium
    complexity: large
    estimated_files: ["src/api/*", "src/models/*"]
    dependencies: ["PROJ-142"]  # Must complete first

status: pending  # pending → running → completed → reviewed
```

---

## Ticket Readiness Criteria

A ticket is "ready-for-agent" when:

| Criterion | Required |
|-----------|----------|
| Clear acceptance criteria | Yes |
| No blocking questions | Yes |
| Dependencies resolved | Yes |
| Files identified | Recommended |
| Complexity estimated | Recommended |
| Test cases defined | Recommended |

### Readiness Checklist (in ticket)

```markdown
## Agent Readiness
- [x] Acceptance criteria clear
- [x] No open questions
- [x] Dependencies: None / PROJ-XXX completed
- [x] Estimated files: src/feature.py, tests/
- [x] Complexity: Small / Medium / Large
```

---

## Generation Rules

### Per-Ticket Process

```
FOR each ticket in batch:
  1. Create feature branch: agent/<ticket-id>
  2. Execute C0: Understand scope, identify files
  3. Execute C1: Plan approach, identify risks
  4. Check escalation triggers
     IF escalation needed:
       Log to escalation queue
       Move to next ticket
  5. Execute C2: Implement and test
  6. Create merge request (draft)
  7. Log completion status
```

### Commit Conventions

```
feat(PROJ-142): implement user authentication

- Add login endpoint with JWT tokens
- Add logout endpoint with token invalidation
- Add middleware for protected routes

Agent: batch-2024-01-15
Checkpoint: C2-complete
Quality-Score: pending-gates
```

---

## Handling Edge Cases

### Ticket Too Complex

If agent detects complexity exceeds estimate:

```
1. Complete what's feasible
2. Document remaining work in MR
3. Create follow-up ticket (draft)
4. Mark original as "partial"
```

### Dependency Failure

If a dependency ticket fails:

```
1. Skip dependent tickets
2. Log skip reason
3. Keep in queue for next batch (after dependency resolved)
```

### Rate Limits

```
ON rate_limit_hit:
  1. Pause batch
  2. Wait for reset (typically 1 hour)
  3. Resume from last checkpoint
  4. If > 3 rate limits, abort batch
```

---

## Morning Review Protocol

### Reviewer Dashboard

```
╔══════════════════════════════════════════════════════════════╗
║             BATCH REVIEW: 2024-01-15-overnight               ║
╠══════════════════════════════════════════════════════════════╣
║ Summary:                                                      ║
║   Total tickets: 15                                          ║
║   Completed: 12                                              ║
║   Escalated: 2                                               ║
║   Failed: 1                                                  ║
╠══════════════════════════════════════════════════════════════╣
║ Ready for Review (sorted by quality score):                  ║
║   ✓ PROJ-142: Auth feature      [Score: 94] [Security: OK]  ║
║   ✓ PROJ-138: Utils cleanup     [Score: 91] [Security: OK]  ║
║   ⚠ PROJ-140: API refactor      [Score: 78] [Security: 1]   ║
║   ...                                                        ║
╠══════════════════════════════════════════════════════════════╣
║ Escalations Pending:                                         ║
║   ⚠ PROJ-145: Architecture decision needed                  ║
║   ⚠ PROJ-147: Multiple valid approaches                     ║
╚══════════════════════════════════════════════════════════════╝
```

### Review Priority

1. **Security issues first** — Any security flag gets immediate attention
2. **High-priority tickets** — Business-critical features
3. **High-quality MRs** — Score > 90, likely quick approvals
4. **Escalations** — Unblock next batch
5. **Lower-quality MRs** — May need rework

---

## Scheduling

### Recommended Schedule

| Time | Activity |
|------|----------|
| 17:00 | Batch intake starts |
| 18:00 | Generation begins |
| 06:00 | Automated gates run |
| 08:00 | Review queue ready |
| 09:00-12:00 | Human review window |
| 14:00 | Escalations resolved |
| 17:00 | Next batch intake |

### Batch Frequency

| Team Size | Recommended Frequency |
|-----------|----------------------|
| 1-3 devs | Weekly or on-demand |
| 4-10 devs | Daily overnight |
| 10+ devs | Continuous with batched reviews |

---

## Metrics

### Batch Metrics

| Metric | Target |
|--------|--------|
| Tickets per batch | 10-20 |
| Completion rate | > 80% |
| Average quality score | > 85 |
| Escalation rate | < 20% |
| Review time per MR | < 15 min |

### Trend Tracking

```
Week 1: 12 tickets, 75% completion, avg score 82
Week 2: 15 tickets, 80% completion, avg score 84
Week 3: 18 tickets, 85% completion, avg score 87
Week 4: 20 tickets, 88% completion, avg score 89
```

---

## Implementation Checklist

- [ ] Batch manifest format defined
- [ ] Ticket readiness criteria documented
- [ ] Branch naming convention set
- [ ] Escalation queue integrated
- [ ] Automated gates pipeline ready
- [ ] Review dashboard built
- [ ] Scheduling mechanism in place
- [ ] Metrics collection active

---

*Start with weekly batches and increase frequency as the process matures.*
