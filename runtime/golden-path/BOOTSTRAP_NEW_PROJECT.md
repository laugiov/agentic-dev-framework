# Bootstrap a New Project

> How to apply the Agentic Dev methodology to your own project.

This guide walks you through setting up a new project that follows the agentic-ready framework, enabling AI agents to work autonomously with human oversight.

---

## Overview

To make your project "agentic-ready", you need:

1. **Documentation structure** — Clear, navigable docs
2. **Operating model** — Roles, checkpoints, escalation rules
3. **Templates** — Issue and PR templates with proof requirements
4. **Quality gates** — CI checks that enforce standards

You can adopt the full framework or start with essentials and expand.

---

## Option A: Full Adoption (Recommended)

Copy the complete framework and customize for your project.

### Step 1: Copy Core Files

```bash
# From this repository, copy these directories to your project:
cp -r core/ /path/to/your/project/
cp -r runtime/ /path/to/your/project/
cp -r .github/ /path/to/your/project/

# Copy key root files
cp REPO_MAP.md /path/to/your/project/
cp METHODOLOGY.md /path/to/your/project/
```

### Step 2: Customize Core Documents

Edit these files for your project:

| File | Customizations Needed |
|------|----------------------|
| `REPO_MAP.md` | Update paths, add your documentation sections |
| `core/README.md` | Adjust to your tech stack |
| `runtime/prompts/CLAUDE_CODE_SYSTEM.md` | Add project-specific rules |
| `runtime/quality-gates/definition-of-done.md` | Adjust criteria for your stack |

### Step 3: Configure CI

Edit `.github/workflows/conformance.yml`:
- Update required files list for your project
- Adjust checks to match your structure

### Step 4: Create Project README

Your README should include:

```markdown
## Start Here

| Your Goal | Start With |
|-----------|------------|
| Understand the project | [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) |
| Work as an AI agent | [runtime/golden-path/QUICKSTART_AGENT.md](runtime/golden-path/QUICKSTART_AGENT.md) |
| Contribute | [CONTRIBUTING.md](CONTRIBUTING.md) |
```

---

## Option B: Minimal Setup (Quick Start)

Start with essentials, add more later.

### Essential Files (Copy These)

```
your-project/
├── .github/
│   ├── pull_request_template.md      # From this repo
│   └── ISSUE_TEMPLATE/
│       └── feature.yml               # From this repo
├── docs/
│   └── CHECKPOINTS.md                # Simplified version
└── CONTRIBUTING.md                   # With checkpoint references
```

### Minimal CHECKPOINTS.md

```markdown
# Checkpoints

## Before Starting (C0)
- [ ] I understand what needs to be done
- [ ] I know which files are affected

## Before Coding (C1)
- [ ] I have a plan
- [ ] I've identified risks

## Before PR (C2)
- [ ] Tests pass
- [ ] Lint passes
- [ ] I've tested manually

## PR Requirements (C3)
- [ ] PR description explains the change
- [ ] All checkpoints documented
- [ ] Ready for review
```

### Minimal PR Template

```markdown
## Summary
[What this PR does]

## Checkpoints
- [ ] C0: Understood the task
- [ ] C1: Planned the approach
- [ ] C2: Tests passing
- [ ] C3: Ready for review

## Testing
[What was tested]
```

---

## Option C: Agent-Only Setup

If you just want AI agents to work better on your existing project.

### Step 1: Add Agent Quickstart

Create `docs/AGENT_GUIDE.md`:

```markdown
# Agent Guide

## Reading Order
1. README.md — Project overview
2. docs/ARCHITECTURE.md — System design
3. CONTRIBUTING.md — How to contribute

## Rules
- Make small, focused changes
- Run tests before committing
- Ask if uncertain about architecture decisions

## Escalate When
- Changing database schema
- Modifying authentication
- Adding new dependencies
- Uncertain about approach
```

### Step 2: Add System Prompt

Create `docs/AGENT_SYSTEM_PROMPT.md`:

```markdown
# System Prompt for AI Agents

You are working on [PROJECT NAME].

## Key Rules
1. Read docs/AGENT_GUIDE.md first
2. Make small, atomic changes
3. Run tests: `npm test` (or your command)
4. Follow existing code patterns

## Before Any Change
- Understand existing code
- Check for similar patterns
- Consider edge cases

## Escalate If
- Multiple valid approaches exist
- Security implications
- Breaking changes needed
```

---

## Option D: Micro Setup (Absolute Minimum)

For teams that want maximum value with minimal files.

Based on real-world testing, these 2 files provide 80% of the value:

### Files (Just 2)

```
your-project/
├── docs/
│   └── CHECKPOINTS.md     # Core workflow
└── .github/
    └── pull_request_template.md   # Proof collection
```

### Why These Two?

| File | Value |
|------|-------|
| CHECKPOINTS.md | Forces structured thinking, includes escalation triggers |
| PR template | Ensures documentation, creates audit trail |

### Micro CHECKPOINTS.md

```markdown
# Checkpoints

## ⚠️ FIRST: Check Escalation Triggers

**STOP and escalate if ANY apply:**

- [ ] Architecture decision (new service, schema, dependency)
- [ ] Security concern (auth, crypto, access control)
- [ ] Data change (PII, migration, retention)
- [ ] Breaking change (API, deprecation)
- [ ] Uncertainty (multiple approaches, unclear requirements)

If any checked → STOP → Ask human → Wait for response.

## Task Size → Workflow

| Size | Lines | Workflow |
|------|-------|----------|
| Trivial | < 10 | Fast Path (this checklist + PR) |
| Small | 10-50 | Lite (add brief plan) |
| Medium+ | > 50 | Full (detailed plan + risks) |

## Checklist

- [ ] **C0**: I understand the task and scope
- [ ] **C1**: I have a plan (for non-trivial tasks)
- [ ] **C2**: Tests pass, lint passes
- [ ] **C3**: PR documents the change
```

### Micro PR Template

```markdown
## Summary
[One sentence: what and why]

## Checkpoints
- [ ] No escalation triggers apply
- [ ] C0: Understood task
- [ ] C2: Tests pass
- [ ] C3: Ready for review

## Changes
[List files changed]

## Testing
[What was tested]
```

### When to Upgrade from Micro

Consider full setup when:
- Team grows beyond 2-3 people
- Project complexity increases
- More agents working concurrently
- Need formal escalation tracking

---

## Customization Guide

### Adapting Checkpoints

Modify checkpoint criteria for your stack:

| Stack | C2 Additions |
|-------|--------------|
| Python | `pytest`, `mypy`, `black` |
| Node.js | `npm test`, `eslint`, `tsc` |
| Go | `go test`, `golint`, `go vet` |
| Rust | `cargo test`, `cargo clippy` |

### Adapting Escalation Rules

Common project-specific escalations:

```markdown
## Always Escalate
- Database migrations
- API contract changes
- Security-related code
- [Your specific rules]

## Usually Safe
- Bug fixes with tests
- Documentation updates
- Dependency patches
```

### Adapting Prompts

In `CLAUDE_CODE_SYSTEM.md`, add:

```markdown
## Project-Specific Rules

### Tech Stack
- Language: [Your language]
- Framework: [Your framework]
- Database: [Your database]

### Commands
- Run tests: `[your test command]`
- Lint: `[your lint command]`
- Build: `[your build command]`

### Patterns
- [Describe your architectural patterns]
- [Describe your coding conventions]
```

---

## Verification

After setup, verify your project is agentic-ready:

### Checklist

- [ ] Agent can find documentation easily (clear README, navigation)
- [ ] Checkpoints are defined and referenced in PR template
- [ ] Escalation triggers are documented
- [ ] CI validates basic quality (tests, lint)
- [ ] System prompt exists with project-specific rules

### Test with an Agent

1. Start a Claude Code session
2. Ask: "Read the project documentation and summarize how to contribute"
3. Verify the agent finds the right files
4. Try a simple task (fix a typo, add a comment)
5. Check if the agent follows checkpoints

---

## Examples

### Example: Node.js API Project

```
my-api/
├── .github/
│   ├── pull_request_template.md
│   ├── ISSUE_TEMPLATE/
│   │   └── feature.yml
│   └── workflows/
│       └── ci.yml
├── docs/
│   ├── ARCHITECTURE.md
│   ├── CHECKPOINTS.md
│   ├── AGENT_GUIDE.md
│   └── API.md
├── src/
├── tests/
├── README.md
├── CONTRIBUTING.md
└── package.json
```

### Example: Python ML Project

```
ml-project/
├── .github/
│   └── pull_request_template.md
├── docs/
│   ├── CHECKPOINTS.md
│   └── AGENT_SYSTEM_PROMPT.md
├── src/
├── tests/
├── notebooks/
├── README.md
└── pyproject.toml
```

---

## Next Steps

1. Choose your adoption level:
   - **Full** — Complete framework (recommended for complex projects)
   - **Minimal** — Essential files (6 files)
   - **Agent-Only** — Just agent guidance (existing projects)
   - **Micro** — Absolute minimum (2 files, 80% value)
2. Copy the relevant files
3. Customize for your project
4. Test with an AI agent
5. Iterate based on experience

---

*The goal is not perfect documentation, but documentation good enough for agents to work effectively.*
