# Memory Pipeline — Complete Data Flow

> How information travels from user session to retrieval system.

---

## Pipeline Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 1: CAPTURE                                                    │
│  ┌──────────┐    ┌──────────────────┐    ┌────────────────────────┐  │
│  │ Session  │───→│ Every interaction │───→│ Append to daily log   │  │
│  │ (chat)   │    │ important info    │    │ memory/dailies/YYYY-   │  │
│  └──────────┘    └──────────────────┘    │  MM-DD.md              │  │
│                                          └────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 2: DISTILLATION (cron 21h)                                   │
│  ┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐  │
│  │ Read all dailies │───→│ Extract:          │───→│ Filter &       │  │
│  │ since last run   │    │ Decision          │    │ reformulate    │  │
│  │                  │    │ Information        │    │                │  │
│  │                  │    │ Insight            │    │ Keep only      │  │
│  │                  │    │ Error              │    │ stable facts  │  │
│  └──────────────────┘    └──────────────────┘    └────────────────┘  │
│                                                           │         │
│                                                           ↓         │
│  ┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐  │
│  │ Update atomic    │←───│ Distribute to     │←───│ Write to       │  │
│  │ files if needed  │    │ right files       │    │ MEMORY.md      │  │
│  └──────────────────┘    └──────────────────┘    └────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 3: STORAGE (atomic files)                                    │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐               │
│  │ projects/   │  │ tools/       │  │ people/     │               │
│  │ example.md  │  │ example.md   │  │ example.md  │               │
│  └──────────────┘  └──────────────┘  └──────────────┘               │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐                                 │
│  │ ideas/      │  │ summaries/   │                                 │
│  │ todo.md     │  │ weekly.md    │                                 │
│  └──────────────┘  └──────────────┘                                 │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 4: RETRIEVAL (on-demand)                                     │
│                                                                      │
│  ┌──────────────┐    ┌──────────────────┐    ┌────────────────────┐   │
│  │ User query   │───→│ Generate variants │───→│ Search files     │   │
│  │ "mdp"        │    │ (synonyms/FR/EN) │    │ with keywords    │   │
│  └──────────────┘    └──────────────────┘    └────────────────────┘   │
│                                                        │             │
│                                                        ↓             │
│                                              ┌────────────────────┐   │
│                                              │ Return top results │   │
│                                              │ with path + lines  │   │
│                                              └────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Phase 1 — Capture (Session)

### Trigger
Every significant user interaction.

### Rule
> "Append only — never overwrite."

### Format in `memory/dailies/YYYY-MM-DD.md`

```markdown
## [HH:MM] — [Context]

* Information: User mentioned a new project
* Decision: Using Notion as main OS
* Insight: Client prefers short responses
* Error: Notion API returned a 400 error

## [HH:MM] — [Next context]

* Information: ...
```

### What's Captured (without filtering)
- Decisions (explicit or implicit)
- Contextual information
- Insights and intuitions
- Errors and corrections
- User preferences
- Identified tasks
- Raw ideas

### What We Do NOT Do
- We don't edit existing files
- We don't delete anything
- We don't filter (everything could be useful)

---

## Phase 2 — Distillation (CRON)

### Schedule
```bash
0 21 * * *  # Every day at 21:00 Paris
```

### Detailed Process

```
1. READ .last_distill_date
   └─ If 2026-04-16 → process all files > 2026-04-16

2. FOR EACH daily:
   Extract lines containing:
   - "* Decision:"
   - "* Information:"
   - "* Insight:"
   - "* Error:"

3. FOR EACH extraction:
   - Deduplicate (if same info appears multiple times)
   - Evaluate stability (will it last > 1 month?)
   - Reformulate clearly

4. DISTRIBUTE:
   a. MEMORY.md → append section "## Distilled YYYY-MM-DD HH:MM"
   b. memory/projects/NAME.md → create or update
   c. memory/tools/NAME.md → create or update
   d. memory/people/NAME.md → create or update
   e. memory/summaries/self-review.md → if recurring pattern

5. WRITE .last_distill_date = today

6. SELF-REVIEW:
   - Read .learnings/LEARNINGS.md (pending entries)
   - Read .learnings/ERRORS.md (pending entries)
   - If > 3 pending OR recurring pattern:
     → Write to self-review.md
     → Propose corrective action
   - Mark resolved entries
```

### Distillation Criteria

| Type | Keep? | Reason |
|------|-------|--------|
| Strategic decision | ✅ Yes | Stable long-term |
| Factual information | ✅ Yes | Reference |
| User preference | ✅ Yes | Important for UX |
| Fixed error | ✅ Yes | Don't repeat |
| Contextual insight | ⚠️ Maybe | If recurring → keep |
| Technical log | ❌ No | Noise, not relevant |
| Thought process | ❌ No | Already in session |

---

## Phase 3 — Storage (Atomic Files)

### Standard Template

```markdown
# [Entity Name] — [Type]

> Atomic entity — one [entity] per file.

## Meta
- **Created**: YYYY-MM-DD
- **Updated**: YYYY-MM-DD
- **Category**: ...

## Content Section 1
...

## Content Section 2
...

### Related:
* memory/projects/related-project.md
* memory/tools/related-tool.md
* memory/people/related-person.md

### Context:
Short explanation: why this file exists, how it fits
into the system.

### Keywords:
word1, word2, synonym1, synonym2, variantFR, variantEN, abbreviation
related-term1, related-term2, technical-term, category-name
```

### Example: `memory/tools/notion-api.md`

```markdown
# Notion API — Tool

> Atomic entity — one tool per file.

## Configuration
- API Key: stored in ~/.config/notion/api_key
- Base URL: https://api.notion.com/v1/

## Database IDs (example)
- Main Database: 344b3135-be59-8034-93e0-e078ca6e602c
- Tasks: 344b3135-be59-8108-b568-e5be0324e473

## Usage
```bash
curl -s -X GET "https://api.notion.com/v1/databases/{id}/query" \
  -H "Authorization: Bearer $NOTION_KEY" \
  -H "Notion-Version: 2022-06-28"
```

### Related:
* memory/projects/example-project.md
* memory/tools/github-api.md

### Context:
Tool used to manage the Notion OS. All CRM operations
go through this API.

### Keywords:
Notion, API, database, query, page, block, notion.so, notion API
base de données, requête API, Notion integration
```

---

## Phase 4 — Retrieval (On-demand)

### Trigger
When Jarvis runs `memory_search` (mandatory before answering about past facts).

### Process

```
1. QUERY: "how do I upload a file to Notion?"

2. GENERATE VARIANTS:
   - Original: "upload file Notion"
   - FR: "uploader fichier Notion", "ajouter fichier Notion"
   - EN: "upload file to Notion", "attach file Notion"
   - Synonyms: "attach", "link", "file", "media"

3. SEARCH:
   For each variant:
   - Grep in MEMORY.md
   - Grep in memory/**/*.md
   - With expanded keywords

4. RANK:
   - Exact match → high score
   - Partial match → medium score
   - Keyword match → low score

5. RETURN:
   Top 3-5 results with:
   - File path
   - Relevant lines
   - Confidence score

6. IF result unclear:
   → Read full file
   → Respond with found information
```

### Search Example

```
Query: "Notion API database query"

Search terms used:
- "notion"
- "notion api"
- "database query"
- "api notion"
- "requête base de données notion"
- "notion database"

Top result:
memory/tools/notion-api.md
Lines 15-20:
"## Database Query
curl -s -X POST "https://api.notion.com/v1/databases/query" ..."

Confidence: HIGH
```

---

## Complete Loop (visual)

```
    ┌──────────────────────────────────────────────────────────────┐
    │                                                               │
    ▼                                                               │
[Session] ──append──→ [dailies/]                                    │
                              │                                     │
                              │ cron 21h                           │
                              ▼                                     │
                      [Distillation]                               │
                              │                                     │
              ┌───────────────┼───────────────┐                     │
              ▼               ▼               ▼                     │
       [MEMORY.md]    [atomic files]   [self-review]               │
              │               │               │                     │
              └───────────────┼───────────────┘                     │
                              │                                     │
                              ▼                                     │
                      [Storage layer]                               │
                              │                                     │
                              │ memory_search                       │
                              ▼                                     │
                       [Retrieval]                                 │
                              │                                     │
                              └──────────────→ [Response]            │
                                                        ▲          │
                                                        │          │
                                            [User query] ──┘        │
```

---

## State Management

### File: `.last_distill_date`

```
2026-04-16
```

→ Cron knows exactly which dailies to process.

### File: `HEARTBEAT.md`

```
# Keep this file empty (or with only comments) to skip heartbeat API calls.
```

→ If empty, no heartbeat polling.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| Corrupted daily file | Skip, log error in `.learnings/ERRORS.md` |
| Cron fails | Retry next day, no data loss (dailies remain) |
| Missing atomic file | Create at next distillation |
| Duplicate entry | Deduplicate before writing |

---

## Security

### Data Separation

```
Public repo (this repository)
├── README.md
├── docs/
├── examples/
└── .github/

Private workspace (never pushed)
├── MEMORY.md          ← Contains real data
├── memory/dailies/   ← Contains real logs
├── memory/projects/  ← Contains real projects
└── .learnings/       ← Contains real learnings
```

### Rule
> Never push `MEMORY.md`, `memory/dailies/`, or `.learnings/` to a public repo.
