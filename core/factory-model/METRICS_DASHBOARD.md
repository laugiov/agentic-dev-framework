# Metrics Dashboard

> Track, measure, and optimize your AI code generation pipeline.

---

## Why Metrics Matter

Without metrics, you can't answer:
- Is AI-generated code getting better or worse?
- Are we actually saving time?
- Which types of tickets work best with AI?
- Where should we invest in improvement?

---

## Core Metrics

### 1. Throughput Metrics

| Metric | Definition | Target |
|--------|------------|--------|
| **Tickets/week** | Tickets completed by AI agents | Increasing |
| **Lines of code/week** | Net LOC added by AI | Context-dependent |
| **Merge rate** | MRs merged / MRs created | > 80% |

### 2. Quality Metrics

| Metric | Definition | Target |
|--------|------------|--------|
| **First-pass approval rate** | MRs approved without changes | > 60% |
| **Rework ratio** | MRs requiring changes / total MRs | < 30% |
| **Post-merge defects** | Bugs found after merge (AI-generated) | < 5% |
| **Average quality score** | From automated gates | > 85 |

### 3. Efficiency Metrics

| Metric | Definition | Target |
|--------|------------|--------|
| **Time to first MR** | Ticket created → MR opened | < 4 hours |
| **Review time** | MR opened → approved/rejected | < 2 hours |
| **Cycle time** | Ticket created → merged | < 24 hours |
| **Human time per MR** | Actual reviewer minutes | < 15 min |

### 4. Cost Metrics

| Metric | Definition | Target |
|--------|------------|--------|
| **API cost per ticket** | LLM API spend / tickets completed | Decreasing |
| **Human hours saved** | Estimated manual time - actual review time | Positive |
| **Cost per merged line** | Total cost / lines merged | Decreasing |

---

## Dashboard Layout

```
╔══════════════════════════════════════════════════════════════════════╗
║                    AI CODE FACTORY DASHBOARD                          ║
║                    Week of Jan 15, 2024                               ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                       ║
║  THROUGHPUT                          QUALITY                          ║
║  ┌─────────────────────────┐        ┌─────────────────────────┐      ║
║  │ Tickets: 42 (+12%)      │        │ Approval Rate: 68%      │      ║
║  │ LOC: 3,247 (+8%)        │        │ Rework Ratio: 24%       │      ║
║  │ Merge Rate: 85%         │        │ Post-merge Bugs: 2      │      ║
║  └─────────────────────────┘        └─────────────────────────┘      ║
║                                                                       ║
║  EFFICIENCY                          COST                             ║
║  ┌─────────────────────────┐        ┌─────────────────────────┐      ║
║  │ Time to MR: 2.3h        │        │ API Cost: $127          │      ║
║  │ Review Time: 1.8h       │        │ Cost/Ticket: $3.02      │      ║
║  │ Cycle Time: 18h         │        │ Hours Saved: ~85        │      ║
║  └─────────────────────────┘        └─────────────────────────┘      ║
║                                                                       ║
╠══════════════════════════════════════════════════════════════════════╣
║  TRENDS (Last 4 Weeks)                                               ║
║                                                                       ║
║  Quality Score        Merge Rate           Review Time               ║
║  100│    ╭──          100│ ╭───           3h│                        ║
║   90│ ╭──╯             90│╭╯               2│──╮                      ║
║   80│─╯                80│╯                1│  ╰──                    ║
║     └────────            └────────          └────────                 ║
║     W1 W2 W3 W4          W1 W2 W3 W4        W1 W2 W3 W4              ║
║                                                                       ║
╠══════════════════════════════════════════════════════════════════════╣
║  TOP ISSUES THIS WEEK                                                 ║
║  • 5 MRs failed security gate (hardcoded strings)                    ║
║  • 3 MRs had insufficient test coverage                              ║
║  • 2 tickets escalated for architecture decisions                    ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## Metric Collection

### Data Sources

| Source | Metrics |
|--------|---------|
| Git/GitHub | Commits, MRs, cycle time, merge rate |
| CI/CD | Gate results, quality scores, test coverage |
| LLM API | Token usage, costs, response times |
| Issue tracker | Ticket counts, complexity, escalations |
| Time tracking | Review time, human effort |

### Collection Script

```python
# Example metric collection
def collect_weekly_metrics(start_date, end_date):
    return {
        "period": {"start": start_date, "end": end_date},

        "throughput": {
            "tickets_completed": count_merged_tickets(start_date, end_date),
            "loc_added": sum_loc_changes(start_date, end_date),
            "merge_rate": merged_mrs / created_mrs,
        },

        "quality": {
            "first_pass_approval": approved_first_try / total_mrs,
            "rework_ratio": mrs_with_changes / total_mrs,
            "post_merge_bugs": count_bugs_from_ai_code(start_date, end_date),
            "avg_quality_score": average_gate_score(start_date, end_date),
        },

        "efficiency": {
            "time_to_mr_hours": avg_time_ticket_to_mr(start_date, end_date),
            "review_time_hours": avg_time_mr_to_approval(start_date, end_date),
            "cycle_time_hours": avg_time_ticket_to_merge(start_date, end_date),
        },

        "cost": {
            "api_cost_usd": sum_api_costs(start_date, end_date),
            "cost_per_ticket": api_cost / tickets_completed,
            "estimated_hours_saved": estimate_manual_hours(tickets_completed),
        }
    }
```

---

## Segmentation

### By Ticket Complexity

| Complexity | Approval Rate | Avg Review Time |
|------------|---------------|-----------------|
| Trivial | 92% | 5 min |
| Small | 78% | 12 min |
| Medium | 61% | 25 min |
| Large | 34% | 45 min |

**Insight**: Focus AI on small/medium tickets. Large tickets need human involvement earlier.

### By Code Area

| Area | Merge Rate | Post-merge Bugs |
|------|------------|-----------------|
| API endpoints | 88% | 1% |
| Business logic | 72% | 4% |
| Database/ORM | 65% | 8% |
| Authentication | 45% | 12% |

**Insight**: AI excels at API work. Auth code needs more human oversight.

### By Agent

| Agent | Tickets/Week | Quality Score |
|-------|--------------|---------------|
| Agent A | 15 | 89 |
| Agent B | 12 | 86 |
| Agent C | 18 | 82 |

**Insight**: Agent C is fast but lower quality. May need prompt tuning.

---

## Alerts

### Quality Alerts

| Condition | Severity | Action |
|-----------|----------|--------|
| Quality score < 70 | HIGH | Pause agent, investigate |
| Post-merge bug rate > 10% | HIGH | Review recent changes |
| Rework ratio > 50% | MEDIUM | Improve ticket specs |
| First-pass approval < 50% | MEDIUM | Review agent prompts |

### Efficiency Alerts

| Condition | Severity | Action |
|-----------|----------|--------|
| Cycle time > 48h | MEDIUM | Check for bottlenecks |
| Review queue > 10 MRs | MEDIUM | Add reviewer capacity |
| Agent idle > 2h | LOW | Check for issues |

### Cost Alerts

| Condition | Severity | Action |
|-----------|----------|--------|
| Daily API cost > $50 | HIGH | Check for runaway agents |
| Cost/ticket > 2x average | MEDIUM | Investigate inefficiency |

---

## Reporting

### Weekly Report Template

```markdown
# AI Code Factory - Weekly Report

**Period**: Jan 15-21, 2024
**Author**: Automated

## Executive Summary
- 42 tickets completed (+12% vs last week)
- 85% merge rate (target: 80%)
- $3.02 cost per ticket (-8% vs last week)

## Highlights
- First-pass approval rate improved to 68%
- New security gates caught 5 potential issues
- Authentication module showing 15% lower quality scores

## Concerns
- Post-merge bug rate increased slightly (3% → 4%)
- 2 agents hitting rate limits during peak hours

## Recommendations
1. Improve ticket specs for auth-related work
2. Stagger agent schedules to avoid rate limits
3. Add integration tests to security gate

## Next Week Goals
- Target 45 tickets
- Improve first-pass approval to 72%
- Reduce auth module rework ratio
```

### Monthly Trend Report

```
Month     Tickets   Merge%   Quality   Cost/Ticket   Hours Saved
Oct 2024     120      78%       82         $4.50          180
Nov 2024     145      81%       84         $3.80          220
Dec 2024     168      83%       86         $3.20          270
Jan 2024     185      85%       87         $3.00          310
```

---

## Implementation Checklist

- [ ] Define core metrics and targets
- [ ] Set up data collection from all sources
- [ ] Build dashboard (Grafana, Metabase, or custom)
- [ ] Configure alerts
- [ ] Create weekly report automation
- [ ] Establish review cadence (weekly metrics review)
- [ ] Document baseline (first 2 weeks of data)

---

## Tools

| Category | Options |
|----------|---------|
| Visualization | Grafana, Metabase, Superset, custom |
| Data storage | PostgreSQL, ClickHouse, BigQuery |
| Collection | Custom scripts, GitHub webhooks, CI artifacts |
| Alerting | PagerDuty, Slack, email |

---

*Start collecting metrics from day one. You can't optimize what you don't measure.*
