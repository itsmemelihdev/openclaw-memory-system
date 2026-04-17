# Self-Improvement — Continuous Feedback Loop

> How the memory system learns from its errors and improves automatically.

---

## Fundamental Principle

A **static** memory system degrades over time:

- Noise accumulates
- Information becomes outdated
- Retrieval failures go uncorrected
- Structure becomes unfit for new needs

A **self-improving** system:
- Identifies its own errors
- Corrects recurring problems
- Evolves with needs

---

## Loop Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    OUTER LOOP (weekly)                       │
│                                                              │
│  ┌─────────────┐    ┌──────────────┐    ┌──────────────┐    │
│  │ Self-review │───→│ Pattern      │───→│ Structural   │    │
│  │ analysis    │    │ detection    │    │ improvements │    │
│  └─────────────┘    └──────────────┘    └──────────────┘    │
│         │                                      │             │
│         └──────────────────────────────────────┘             │
│                            ↓                                  │
│             ┌──────────────────────┐                         │
│             │ Update MEMORY.md      │                         │
│             │ Update files         │                         │
│             │ (keywords, related)  │                         │
│             └──────────────────────┘                         │
└─────────────────────────────────────────────────────────────┘
                          ↑
                          │ (cron daily 21h)
                          │
┌─────────────────────────────────────────────────────────────┐
│                    INNER LOOP (daily)                       │
│                                                              │
│  ┌──────────────┐    ┌─────────────┐    ┌──────────────┐   │
│  │ .learnings/  │───→│ Cron checks │───→│ Entries      │   │
│  │ (raw logs)   │    │ pending     │    │ resolved or  │   │
│  └──────────────┘    └─────────────┘    │ promoted     │   │
│                                          └──────────────┘   │
└─────────────────────────────────────────────────────────────┘
                          ↑
                          │
              ┌───────────────────────────┐
              │ Events during session:     │
              │ - Error occurs            │
              │ - User corrects          │
              │ - Missing capability     │
              │ - Better approach found  │
              └───────────────────────────┘
```

---

## The 3 Learnings Files

### `.learnings/LEARNINGS.md`
```markdown
# Learnings

Corrections, insights, and knowledge gaps captured.

**Categories**: correction | insight | knowledge_gap | best_practice
```

**When to populate:**
- User says "No, that's wrong..."
- User corrects information
- We discover we were wrong
- We find a better approach

**Entry format:**
```markdown
## [LRN-YYYYMMDD-XXX] category

**Logged**: ISO-8601 timestamp
**Priority**: low | medium | high | critical
**Status**: pending | resolved | promoted
**Area**: frontend | backend | infra | tests | docs | config

### Summary
One-line description of what was learned

### Details
Full context: what happened, what was wrong, what's correct

### Suggested Action
Specific fix or improvement to make

### Metadata
- Source: conversation | error | user_feedback
- Related Files: path/to/file.ext
- Tags: tag1, tag2
- See Also: LRN-20260401-001 (if related)
- Recurrence-Count: 1
- First-Seen: 2026-04-01
- Last-Seen: 2026-04-01
```

### `.learnings/ERRORS.md`
```markdown
# Errors

Command failures and integration errors.
```

**When to populate:**
- Command fails (exit code != 0)
- API returns an error
- Exception or stack trace
- Timeout or connection failure

**Entry format:**
```markdown
## [ERR-YYYYMMDD-XXX] skill_or_command_name

**Logged**: ISO-8601 timestamp
**Priority**: high
**Status**: pending | resolved
**Area**: frontend | backend | infra | tests | docs | config

### Summary
Brief description of what failed

### Error
```
Actual error message or output
```

### Context
- Command/operation attempted
- Input or parameters used
- Environment details if relevant

### Suggested Fix
If identifiable, what might resolve this

### Metadata
- Reproducible: yes | no | unknown
- Related Files: path/to/file.ext
- See Also: ERR-20260401-001 (if recurring)
```

### `.learnings/FEATURE_REQUESTS.md`
```markdown
# Feature Requests

Capabilities requested by the user.
```

**When to populate:**
- User asks for something that doesn't exist
- "Can you also..."
- "I wish you could..."
- "Is there a way to..."

**Entry format:**
```markdown
## [FEAT-YYYYMMDD-XXX] capability_name

**Logged**: ISO-8601 timestamp
**Priority**: medium
**Status**: pending | in_progress | resolved
**Area**: frontend | backend | infra | tests | docs | config

### Requested Capability
What the user wanted to do

### User Context
Why they needed it, what problem they're solving

### Complexity Estimate
simple | medium | complex

### Suggested Implementation
How this could be built, what it might extend
```

---

## Self-Review Journal

**File**: `memory/summaries/self-review.md`

### Structure
```markdown
# Self-Review — Memory System Improvement Log

## 📊 Scorecard — System Health

| Component | Status | Notes |
|-----------|--------|-------|
| Daily capture | ✅ | Operational |
| Cron distillation | ✅ | Configured |
| Graph memory | ✅ | In place |
| Retrieval optimization | ✅ | Done |
| Self-improvement loop | 🔄 | Built-in now |

## 🔄 Improvement Loop

### Trigger
- **When**: at each distillation (daily 21h) + manual review
- **Source**: `.learnings/`

### Process
1. Read pending entries in `.learnings/`
2. Analyze recurring patterns
3. Identify retrieval problems
4. Propose corrective actions
5. Update relevant files
6. Mark resolved entries

## 📝 Entries — Retrieval Errors & Improvements

### [REV-20260417-001] Initial setup
**Date**: 2026-04-17
**Type**: system_setup
**Status**: completed

**Summary**: ...
**Actions taken**: ...
**Next review**: 2026-04-24
```

---

## Self-Review Process (cron daily)

```python
def self_review():
    """
    Called during cron distillation at 21h.
    """
    # 1. Read pending entries
    learnings = read_pending('.learnings/LEARNINGS.md')
    errors = read_pending('.learnings/ERRORS.md')
    features = read_pending('.learnings/FEATURE_REQUESTS.md')

    all_pending = learnings + errors + features

    # 2. If too many pending → alert
    if len(all_pending) > 3:
        add_entry('self-review.md', {
            'alert': f'{len(all_pending)} pending entries',
            'action': 'Review needed'
        })

    # 3. Detect patterns
    patterns = detect_recurring_patterns(all_pending)

    # 4. For each pattern
    for pattern in patterns:
        if pattern.recurrence_count >= 3:
            # Systemic problem → fix structure
            propose_structural_fix(pattern)

        elif pattern.recurrence_count >= 1:
            # One-off → just log
            add_learning_to_file(pattern)

    # 5. Promote if needed
    if significant_learning_detected():
        promote_to_MEMORY_or_atomic_file()

    # 6. Close resolved entries
    for entry in all_pending:
        if is_resolved(entry):
            mark_resolved(entry)
```

---

## Pattern Detection

### Pattern Types

| Pattern | Threshold | Action |
|---------|-----------|--------|
| Retrieval failure | > 2/week | Add keywords, fix structure |
| Same error | > 2/week | Document workaround |
| Missing capability | > 1 occurrence | Log to FEATURE_REQUESTS |
| Knowledge gap | > 1 occurrence | Add to MEMORY.md |
| Better approach | 1 occurrence | Promote to best practice |

### Detection Example

```python
# Pseudo-code
def detect_retrieval_failures():
    """
    If user says something like:
    - "I already told you..."
    - "Why didn't you remember..."
    - "You should know that..."
    → This is a retrieval failure
    """
    recent_queries = get_recent_user_queries()

    for query in recent_queries:
        results = memory_search(query)
        if not results or confidence < THRESHOLD:
            if "should know" in user_followup:
                log_learning({
                    'type': 'retrieval_failure',
                    'query': query,
                    'problem': 'User expected system to remember',
                    'solution': 'Add keywords or related link'
                })
```

---

## Structural Improvements

### When?

- Same file modified > 3 times/week
- Same information duplicated across files
- File too long (> 500 lines)
- Retrieval often failing for a topic

### Improvement Types

| Problem | Solution |
|---------|----------|
| File too long | Split into multiple atomic files |
| Insufficient keywords | Enrich with synonyms + FR/EN |
| Incomplete graph | Add Related: links |
| Unfit structure | Refactor (one file = one entity) |
| Duplicated information | Consolidate into single source |

### Example

**Before** (problem):
```
memory/
├── notes.md  (800 lines, everything mixed)
```

**After** (solution):
```
memory/
├── projects/
│   └── example-project.md  (200 lines)
├── tools/
│   └── example-tool.md     (150 lines)
├── people/
│   └── john-doe.md         (100 lines)
└── notes.md               (removed, distributed)
```

---

## Promotion Rules

### When to Promote?

| Condition | Destination |
|-----------|-------------|
| Best practice | SOUL.md or AGENTS.md |
| Workflow improvement | AGENTS.md |
| Tool behavior | TOOLS.md |
| Project fact | MEMORY.md |
| Recurring pattern (3+) | Appropriate SYSTEM FILE |

### How to Promote

```markdown
# Before (in LEARNINGS.md)
**Status**: pending
**Suggested Action**: Add "Claude" as alias for "Claude.ai" in keywords

# After (in MEMORY.md)
## Reformulated Concepts
- "Claude" / "Claude.ai" / "claude.ai" / "Claude AI"

# Update in LEARNINGS.md
**Status**: promoted
**Promoted**: MEMORY.md (Reformulated Concepts)
```

---

## Metrics to Track

| Metric | Target | Alert |
|--------|--------|-------|
| Pending learnings | < 5 | > 10 |
| Retrieval failures | < 2/week | > 5 |
| Duplicate entries | 0 | > 1 |
| Files without Keywords | 0 | > 2 |
| Self-review entries | ~1/week | 0 (no review!) |

---

## Cron Integration

```bash
# The 21h cron runs:
1. Distillation (see memory-pipeline.md)
2. Self-review (this file)

# Message sent to cron:
"""
Run memory distillation + self-review:

DISTILLATION (1-5):
[... distillation steps ...]

SELF-REVIEW (6-9):
6. Read .learnings/LEARNINGS.md and .learnings/ERRORS.md — pending entries
7. If pending > 3 or recurring pattern detected:
   - Add entry to memory/summaries/self-review.md
   - Propose corrective action
8. Close resolved entries (status: resolved)
9. IF self-review.md has significant new entry
   → promote to MEMORY.md or atomic files

SILENT: Files write only, no chat message.
"""
```

---

## Review Cycle

```
Daily (cron 21h)
├── Read .learnings/
├── Detect patterns
└── Update self-review.md

Weekly (cron + manual)
├── Review self-review.md entries
├── Identify systemic issues
├── Apply structural fixes
└── Reset weekly metrics

Monthly (manual)
├── Full system audit
├── Refactor if needed
├── Update documentation
└── Archive old entries
```

---

## Best Practices

1. **Log immediately** — Fresh context = more precise
2. **Be specific** — Future Jarvis must understand quickly
3. **Suggest concrete fixes** — Not just "investigate"
4. **Link related entries** — Create web, not silo
5. **Promote aggressively** — When in doubt, add to MEMORY.md
6. **Review regularly** — Stale learnings = no value
