# Checkpoints

> Validation gates for agentic workflow progression.

Checkpoints are mandatory validation stages that ensure quality and enable human oversight. Each checkpoint has entry criteria, exit criteria, and required proofs.

---

## Task Size Classification

**First step**: Assess task size to determine the appropriate workflow.

| Size | Lines Changed | Files | Workflow | Example |
|------|---------------|-------|----------|---------|
| **Trivial** | < 10 | 1 | Fast Path | Fix typo, update version |
| **Small** | 10-50 | 1-2 | Lite (C0+C2+C3) | Add function, fix bug |
| **Medium** | 50-200 | 2-5 | Full (C0-C3) | New feature, refactor |
| **Large** | > 200 | 5+ | Full + Extra Review | Major feature, migration |

**Default to Full workflow** if uncertain about size.

---

## Fast Path (Trivial Tasks Only)

For trivial changes (< 10 lines, single file, no escalation triggers):

### Fast Path Requirements

1. **Quick C0**: One-line scope statement
2. **Skip C1**: No planning document needed
3. **Quick C2**: Implement + verify
4. **Minimal C3**: Short PR description

### Fast Path Criteria (ALL must be true)

- [ ] Change is < 10 lines
- [ ] Only 1 file modified
- [ ] No escalation triggers apply
- [ ] Documentation or bug fix only
- [ ] No new dependencies
- [ ] No architecture changes

### Fast Path PR Template

```markdown
## Summary
[One sentence]

## Fast Path Confirmation
- [x] < 10 lines, single file
- [x] No escalation triggers
- [x] Tests pass (if applicable)

## Change
[Brief description]
```

**If ANY doubt**: Use standard workflow, not Fast Path.

---

## Checkpoint Overview

| Checkpoint | Name | Purpose |
|------------|------|---------|
| **C0** | Comprehension & Scope | Confirm understanding before planning |
| **C1** | Plan & Risks | Validate approach before implementation |
| **C2** | Implementation & Tests | Verify code quality before PR |
| **C3** | PR & Proofs | Confirm deliverable completeness |
| **C4** | Post-Merge | Validate production behavior (optional) |

```
[Issue] → C0 → C1 → C2 → C3 → [PR Merged] → C4
           │     │     │     │               │
         scope  plan  code  proof         monitor
```

---

## C0: Comprehension & Scope

**Purpose**: Ensure the agent understands what is being asked before starting work.

### Entry Criteria
- Issue or task description received
- Access to relevant codebase

### Required Activities
- Read and parse the task description
- Identify ambiguities or missing information
- Determine affected files and components
- Estimate scope and complexity

### Exit Criteria
- [ ] Task objective stated in agent's own words
- [ ] Scope boundaries defined (what's in, what's out)
- [ ] Questions raised if clarification needed
- [ ] No blocking ambiguities remain

### Proofs
- Scope statement document
- List of affected components
- Escalation record (if questions raised)

---

## C1: Plan & Risks

**Purpose**: Validate the implementation approach before writing code.

### Entry Criteria
- C0 completed
- Scope confirmed

### Required Activities
- Analyze existing code in affected areas
- Design implementation approach
- Identify risks and dependencies
- Create structured plan

### Exit Criteria
- [ ] Implementation plan documented
- [ ] Files to create/modify listed
- [ ] Risks identified and tagged
- [ ] Escalations raised for architecture/security decisions
- [ ] Plan approved (human or automated)

### Proofs
- Implementation plan document
- Risk assessment (using RISK_MODEL)
- ADR (if significant decisions made)
- Escalation records (if any)

---

## C2: Implementation & Local Tests

**Purpose**: Ensure code quality before requesting review.

### Entry Criteria
- C1 completed
- Plan approved

### Required Activities
- Implement changes per plan
- Run local tests
- Run lint and static analysis
- Fix issues iteratively

### Exit Criteria
- [ ] All planned changes implemented
- [ ] Tests pass locally
- [ ] Lint passes (no new violations)
- [ ] Static analysis passes (PHPStan level 9 or equivalent)
- [ ] No regressions in existing tests

### Proofs
- Diff of changes
- Test execution output
- Lint output
- Static analysis output

---

## C3: PR & Proofs

**Purpose**: Confirm the deliverable is complete and ready for merge.

### Entry Criteria
- C2 completed
- All local checks pass

### Required Activities
- Create pull request with required sections
- Attach all proof artifacts
- Self-review changes
- Request human review

### Exit Criteria
- [ ] PR created with complete description
- [ ] All checkpoints documented in PR
- [ ] Proof artifacts attached
- [ ] CI pipeline passes
- [ ] Human review approved

### Proofs
- PR description (following template)
- CI pipeline results
- Review approval
- Checkpoint completion checklist

### PR Template Requirements

```markdown
## Summary
[1-3 sentences describing the change]

## Checkpoints Completed
- [x] C0: Comprehension & Scope
- [x] C1: Plan & Risks
- [x] C2: Implementation & Tests
- [x] C3: PR & Proofs

## Changes
[List of files modified]

## Testing
[Test commands run + output summary]

## Risks
[Identified risks and mitigations]

## Escalations
[Any escalations raised and decisions made]
```

---

## C4: Post-Merge (Optional)

**Purpose**: Validate behavior in production environment.

### Entry Criteria
- C3 completed
- PR merged
- Deployment completed

### Required Activities
- Monitor error rates
- Check performance metrics
- Validate feature behavior
- Confirm no regressions

### Exit Criteria
- [ ] No increase in error rate
- [ ] Performance within acceptable bounds
- [ ] Feature works as specified
- [ ] Rollback not required

### Proofs
- Monitoring dashboard screenshot
- Error rate metrics
- Performance metrics
- User validation (if applicable)

---

## Checkpoint Failure Handling

If a checkpoint cannot be passed:

1. **Document** the blocking issue
2. **Escalate** if human decision needed
3. **Iterate** up to max allowed attempts
4. **Rollback** if iteration limit exceeded

Never skip a checkpoint. If a checkpoint seems unnecessary, escalate to discuss removing it from the workflow.

---

## Checkpoint Automation

Where possible, checkpoints should be machine-verifiable:

| Check | Automation |
|-------|------------|
| Tests pass | CI pipeline |
| Lint clean | Pre-commit hook |
| Static analysis | CI pipeline |
| Links valid | Documentation check |
| Template complete | PR validation |

Human judgment required for:
- Scope appropriateness
- Design quality
- Risk assessment accuracy
- Code clarity and maintainability

---

*Checkpoints exist to ensure quality, not to slow progress.*
