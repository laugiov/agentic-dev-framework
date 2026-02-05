# Audit Trail

> Traceability for AI-generated code.

---

## Why Audit Trail

When AI generates code, you need to answer:
- Who requested this code?
- What prompt produced it?
- When was it generated?
- What was changed?
- Why was it approved?

This matters for:
- **Incident investigation** — Trace bugs back to their source
- **Compliance** — Demonstrate control over code changes
- **Improvement** — Learn which prompts produce better code
- **Accountability** — Clear ownership of decisions

---

## What to Track

### Per Code Generation

| Field | Description | Example |
|-------|-------------|---------|
| `generation_id` | Unique identifier | `gen_abc123` |
| `timestamp` | When generated | `2024-01-15T10:30:00Z` |
| `requester` | Who requested | `user@company.com` |
| `ticket_id` | Related issue | `PROJ-142` |
| `agent_id` | Which AI agent | `agent_claude_01` |
| `model_version` | AI model used | `claude-3-opus-20240229` |
| `prompt_hash` | Hash of prompt | `sha256:abc123...` |
| `context_hash` | Hash of code context | `sha256:def456...` |
| `files_generated` | Output files | `["src/auth.py", "tests/test_auth.py"]` |
| `lines_added` | LOC added | `142` |
| `lines_removed` | LOC removed | `23` |

### Per Review

| Field | Description | Example |
|-------|-------------|---------|
| `review_id` | Unique identifier | `rev_xyz789` |
| `generation_id` | Links to generation | `gen_abc123` |
| `reviewer` | Who reviewed | `senior@company.com` |
| `decision` | Outcome | `approved` / `rejected` / `changes_requested` |
| `security_check` | Security gate result | `passed` |
| `quality_score` | Automated score | `87` |
| `comments` | Review notes | `"Approved with minor style fix"` |

### Per Deployment

| Field | Description | Example |
|-------|-------------|---------|
| `deployment_id` | Unique identifier | `dep_mno456` |
| `generation_ids` | Included generations | `["gen_abc123", "gen_def456"]` |
| `environment` | Target env | `production` |
| `deployed_by` | Who deployed | `ci-system` |
| `deployed_at` | When | `2024-01-15T14:00:00Z` |
| `rollback_id` | If rolled back | `null` or `dep_xyz789` |

---

## Storage Format

### Structured Log Entry

```json
{
  "event_type": "code_generation",
  "generation_id": "gen_abc123",
  "timestamp": "2024-01-15T10:30:00Z",
  "requester": {
    "id": "user_123",
    "email": "dev@company.com",
    "role": "developer"
  },
  "context": {
    "ticket_id": "PROJ-142",
    "branch": "feature/auth-flow",
    "repository": "company/backend"
  },
  "agent": {
    "id": "agent_claude_01",
    "model": "claude-3-opus-20240229",
    "prompt_version": "v2.3"
  },
  "input": {
    "prompt_hash": "sha256:abc123...",
    "context_hash": "sha256:def456...",
    "context_files": ["src/models/user.py", "src/api/routes.py"],
    "escalation_triggers_checked": true,
    "task_size": "medium"
  },
  "output": {
    "files": [
      {"path": "src/auth.py", "action": "create", "lines": 89},
      {"path": "tests/test_auth.py", "action": "create", "lines": 53}
    ],
    "total_lines_added": 142,
    "total_lines_removed": 0
  },
  "security": {
    "secrets_scan": "passed",
    "sast_scan": "passed",
    "findings": []
  }
}
```

### Git Metadata

Embed in commit messages:

```
feat(PROJ-142): implement user authentication

- Add login endpoint with JWT tokens
- Add logout endpoint with token invalidation
- Add middleware for protected routes

Agent: claude-3-opus-20240229
Generation-ID: gen_abc123
Prompt-Version: v2.3
Quality-Score: 87
Security-Check: passed
Reviewed-By: senior@company.com
```

### PR Metadata

```markdown
## AI Generation Details

| Field | Value |
|-------|-------|
| Generation ID | `gen_abc123` |
| Model | claude-3-opus-20240229 |
| Prompt Version | v2.3 |
| Quality Score | 87 |
| Security Check | Passed |

### Files Generated
- `src/auth.py` (89 lines)
- `tests/test_auth.py` (53 lines)

### Checkpoints
- [x] C0: Scope understood
- [x] C1: Plan reviewed
- [x] C2: Tests passing
- [x] C3: PR complete
```

---

## Implementation

### Database Schema

```sql
CREATE TABLE code_generations (
    id VARCHAR(36) PRIMARY KEY,
    created_at TIMESTAMP NOT NULL,
    requester_id VARCHAR(36) NOT NULL,
    ticket_id VARCHAR(50),
    agent_id VARCHAR(50) NOT NULL,
    model_version VARCHAR(100) NOT NULL,
    prompt_hash VARCHAR(64) NOT NULL,
    context_hash VARCHAR(64),
    files_generated JSONB NOT NULL,
    lines_added INTEGER NOT NULL,
    lines_removed INTEGER NOT NULL,
    security_scan_result VARCHAR(20),
    quality_score INTEGER,

    FOREIGN KEY (requester_id) REFERENCES users(id)
);

CREATE TABLE code_reviews (
    id VARCHAR(36) PRIMARY KEY,
    generation_id VARCHAR(36) NOT NULL,
    created_at TIMESTAMP NOT NULL,
    reviewer_id VARCHAR(36) NOT NULL,
    decision VARCHAR(20) NOT NULL,
    comments TEXT,

    FOREIGN KEY (generation_id) REFERENCES code_generations(id),
    FOREIGN KEY (reviewer_id) REFERENCES users(id)
);

CREATE INDEX idx_generations_ticket ON code_generations(ticket_id);
CREATE INDEX idx_generations_created ON code_generations(created_at);
CREATE INDEX idx_reviews_generation ON code_reviews(generation_id);
```

### API for Logging

```python
from datetime import datetime
from typing import List, Dict
import hashlib
import json

class AuditLogger:
    def __init__(self, storage):
        self.storage = storage

    def log_generation(
        self,
        requester_id: str,
        ticket_id: str,
        agent_id: str,
        model_version: str,
        prompt: str,
        context: str,
        files_generated: List[Dict],
        security_result: str,
        quality_score: int
    ) -> str:
        generation_id = f"gen_{self._generate_id()}"

        record = {
            "id": generation_id,
            "created_at": datetime.utcnow().isoformat(),
            "requester_id": requester_id,
            "ticket_id": ticket_id,
            "agent_id": agent_id,
            "model_version": model_version,
            "prompt_hash": self._hash(prompt),
            "context_hash": self._hash(context),
            "files_generated": files_generated,
            "lines_added": sum(f.get("lines", 0) for f in files_generated),
            "lines_removed": 0,
            "security_scan_result": security_result,
            "quality_score": quality_score
        }

        self.storage.insert("code_generations", record)
        return generation_id

    def log_review(
        self,
        generation_id: str,
        reviewer_id: str,
        decision: str,
        comments: str = None
    ) -> str:
        review_id = f"rev_{self._generate_id()}"

        record = {
            "id": review_id,
            "generation_id": generation_id,
            "created_at": datetime.utcnow().isoformat(),
            "reviewer_id": reviewer_id,
            "decision": decision,
            "comments": comments
        }

        self.storage.insert("code_reviews", record)
        return review_id

    def _hash(self, content: str) -> str:
        return hashlib.sha256(content.encode()).hexdigest()

    def _generate_id(self) -> str:
        import uuid
        return str(uuid.uuid4())[:12]
```

---

## Querying the Audit Trail

### Common Queries

**Find all code generated for a ticket:**
```sql
SELECT * FROM code_generations
WHERE ticket_id = 'PROJ-142'
ORDER BY created_at;
```

**Find code that failed security scan:**
```sql
SELECT * FROM code_generations
WHERE security_scan_result = 'failed'
AND created_at > NOW() - INTERVAL '30 days';
```

**Find reviewer approval rate:**
```sql
SELECT
    reviewer_id,
    COUNT(*) as total_reviews,
    SUM(CASE WHEN decision = 'approved' THEN 1 ELSE 0 END) as approvals,
    ROUND(100.0 * SUM(CASE WHEN decision = 'approved' THEN 1 ELSE 0 END) / COUNT(*), 1) as approval_rate
FROM code_reviews
GROUP BY reviewer_id;
```

**Trace a bug to its generation:**
```sql
-- Given a file and line number from a bug report
SELECT g.*, r.reviewer_id, r.decision
FROM code_generations g
JOIN code_reviews r ON r.generation_id = g.id
WHERE g.files_generated @> '[{"path": "src/auth.py"}]'
ORDER BY g.created_at DESC
LIMIT 1;
```

---

## Retention Policy

| Data Type | Retention | Reason |
|-----------|-----------|--------|
| Generation records | 3 years | Compliance, investigation |
| Prompt content | 90 days | Privacy, storage |
| Review records | 3 years | Compliance |
| Security scan results | 1 year | Trend analysis |

### Archival Process

```
After 90 days:
  - Remove full prompt content
  - Keep prompt hash for verification
  - Archive to cold storage

After 3 years:
  - Aggregate statistics
  - Delete individual records
  - Keep compliance summary
```

---

## Compliance Mapping

| Requirement | How Audit Trail Helps |
|-------------|----------------------|
| SOC 2 CC6.1 | Track who generated what code |
| SOC 2 CC7.2 | Monitor AI system changes |
| ISO 27001 A.12.4 | Logging of AI activities |
| GDPR Art. 30 | Record of processing activities |

---

## Implementation Checklist

- [ ] Define audit record schema
- [ ] Set up storage (database/log system)
- [ ] Integrate logging into generation pipeline
- [ ] Add generation ID to commits/PRs
- [ ] Create query interface for investigation
- [ ] Set up retention and archival
- [ ] Document for compliance

---

*Complete audit trail enables trust in AI-generated code. Every line should be traceable.*
