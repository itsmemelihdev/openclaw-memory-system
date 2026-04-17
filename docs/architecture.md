# Architecture — 5 Layers

> How the memory system is organized into independent but interconnected layers.

---

## Overview

The system works as an **information processing pipeline**:

```
Raw data → Cleaning → Structured storage → Links → Retrieval
(session)   (distillation)   (atomic)     (graph)  (search)
```

Each layer has a single responsibility and can be improved independently.

---

## Layer 1 — Capture (`memory/dailies/`)

### Responsibility
Collect **everything** without filtering. Capture is a wide funnel.

### Principle
> "Never filter at the capture stage."

### Format
```markdown
## [HH:MM] — [Context]

* Information: ...
* Decision: ...
* Insight: ...
* Error: ... (optional)
```

### Why no filter?
- We don't know what will be relevant later
- Unexpected insights are often the most valuable
- Noise can be removed during distillation

### Hard Rule
- **Write** to `memory/dailies/YYYY-MM-DD.md`
- **Never** write to MEMORY.md during session
- **Never** modify atomic files during session

---

## Layer 2 — Distillation (CRON)

### Responsibility
Transform raw logs into **condensed memory**.

### Process
```
1. Read all dailies since .last_distill_date
2. Extract: Decision + Information + Insight + Error
3. Filter: keep only stable facts (> 1 month relevant)
4. Reformulate: write clearly for retrieval
5. Distribute: write to correct atomic files
6. Log: update .last_distill_date
```

### Outputs
- `MEMORY.md` — sections `## Distilled Updates` with date
- `memory/projects/*.md` — updated if needed
- `memory/tools/*.md` — new tool → new file
- `memory/people/*.md` — new person → new file
- `memory/summaries/self-review.md` — if pattern detected

### Frequency
- **Cron**: 21h Paris daily
- **First run**: processes all dailies
- **Subsequent runs**: only new files

---

## Layer 3 — Atomic Memory (`memory/*/`)

### Responsibility
Store **one entity per file** with a single topic.

### Structure
```
memory/
├── projects/     # One file = one project
├── tools/        # One file = one tool/config
├── people/       # One file = one person
├── ideas/        # One file = ideas backlog
└── summaries/    # One file = one period
```

### Atomic File Template
```markdown
# [Entity Name]

> Atomic entity — one [entity] per file.

## [Section 1]
content

## [Section 2]
content

### Related:
* path/to/related/file.md
* path/to/another.md

### Context:
Why this file exists and how it connects to the system.

### Keywords:
synonym, variant, equivalent term, french, english, abbreviation
```

### Rule
> A file should be understandable without reading others.

---

## Layer 4 — Graph Linking

### Responsibility
Create **navigation paths** between files.

### Sections Added

**`### Related:`**
```markdown
### Related:
* memory/projects/example-project.md
* memory/tools/notion-api.md
* memory/people/john-doe.md
```
→ Direct links to related files

**`### Context:`**
```markdown
### Context:
This file documents project X. It was created following...
```
→ Short explanation of the link and origin

**`### Keywords:`**
```markdown
### Keywords:
projet, project, example, demo, fictional
projet example, projet demo, projet fictif
projet X, projet initial
```
→ Synonyms + FR/EN variants + related terms

### Why?
- `memory_search` searches content + keywords
- Related: creates navigation paths
- Context: helps understand without reading the file

---

## Layer 5 — Retrieval Optimization

### Responsibility
Maximize the **probability of finding** the right information.

### Strategies

#### 1. Massive Synonyms
```markdown
### Keywords:
password, mdp, mot de passe, credentials, login, auth, access, clé, clé d'accès
```
→ Even if user types "mdp", we find "password"

#### 2. Multi-Formulation of Concepts
```markdown
## Reformulated Concepts
- "memory" / "mémoire" / "MEMORY" / "long-term memory"
- "agent" / "agent IA" / "AI agent" / "bot"
- "cron" / "scheduled task" / "tâche planifiée" / "automation"
```

#### 3. FR/EN Mixing
```markdown
### Keywords:
notion, Notion, database, base de données, bloc notes intelligent
OS, operating system, système, mission control, workspace
```

#### 4. Hybrid Search (concept)
> Note: Current implementation uses semantic search via `memory_search`.
> A hybrid version (semantic + BM25) would be ideal for the future.

### Retrieval Process
```
1. User query
2. Generate variants (FR/EN/synonyms)
3. Search: MEMORY.md + all atomic files
4. Return top results with path + lines
5. If unclear → read full file
```

---

## Interaction Between Layers

```
[Session]
    ↓ BOOT: reads SOUL.md + USER.md + dailies
[Context loaded]
    ↓ Interaction
[Append to daily] ← Layer 1
    ↓ cron 21h
[Distillation] ← Layer 2
    ↓
[Atomic files] ← Layer 3
    ↓
[Graph links] ← Layer 4
    ↓
[Update keywords + related]
    ↓
[Query] → retrieval ← Layer 5
    ↓
[Response]
```

---

## Scalability

| Aspect | Current Limit | Possible Improvement |
|--------|---------------|---------------------|
| Atomic files | N files | Partition by domain |
| Keywords | Manual | Auto-generation via LLM |
| Graph links | Manual | Auto-detection via embeddings |
| Retrieval | memory_search | Hybrid BM25 + semantic |

---

## Design Principles

1. **Separation of concerns** — Each layer has a clear job
2. **No duplication** — One fact = one location
3. **Append-only for logs** — Never overwrite
4. **Aggressive distillation** — Keep MEMORY.md short
5. **Atomicity** — One entity = one file
6. **Explicit graph** — No implicit links
