# Retrieval System — How to Find Information

> Strategies to maximize the probability of finding the right information at the right time.

---

## The Fundamental Problem

A memory system is only useful if we can **retrieve** information when needed.

But:

- Users don't type exact words
- Information can be worded differently
- Context changes between storage and retrieval
- Synonyms and variations exist in both languages

---

## Strategy 1 — Massive Keywords

### Principle
In each file, include **all variants** of the same concept.

### Basic Example
```markdown
### Keywords:
password, mdp, mot de passe, credentials, login, auth, access, clé, clé d'accès
```

### Multi-language (FR/EN)
```markdown
### Keywords:
notion, Notion, database, base de données, bloc notes intelligent
OS, operating system, système, mission control, workspace
crontab, cron, scheduled task, tâche planifiée, automation, job
```

### Technical Synonyms
```markdown
### Keywords:
API key, clé API, token, bearer token, authorization header
webhook, callback, notification, trigger, event
embedding, vector, embedding model, semantic vector
```

### Orthographic Variants
```markdown
### Keywords:
Behavior, behaviour (UK vs US)
Color, colour
Center, centre
Organisation, organization
```

---

## Strategy 2 — Reformulated Concepts

### Principle
In `MEMORY.md`, list the same concepts under multiple formulations.

### Example
```markdown
## Reformulated Concepts — Multi-phrase retrieval

- "mémoire long terme" / "long term memory" / "persistent memory" / "memory bank"
- "bras droit数字" / "chief of staff agent" / "agentique" / "AI chief of staff"
- "système mémoire" / "memory system" / "agent memory architecture"
- "distillation mémoire" / "memory distillation" / "cron mémoire" / "automated memory cleanup"
- "récupération mémoire" / "memory retrieval" / "semantic search" / "memory search"
```

### Why It Works
When the user searches "memory bank", the search also finds files containing "long term memory".

---

## Strategy 3 — Graph Linking

### Principle
Files point to each other, creating navigation paths.

### Structure
```markdown
### Related:
* memory/projects/example-project.md      ← related project
* memory/tools/notion-api.md              ← tool used
* memory/people/john-doe.md               ← person involved

### Context:
This file documents project X. It was created following
user request in April 2026.
```

### Advantage
When searching for "project X", we also find "tool Y" and "person Z" related to it.

---

## Strategy 4 — Contextual Headers

### Principle
Each file starts with a descriptive header.

### Bad
```markdown
# Notes

Project in progress...
```

### Good
```markdown
# Example Project — AI Content Generation System

> Atomic entity — one project per file.
> Status: Active | Created: 2026-04-01 | Updated: 2026-04-15

## Overview
This project tracks the development of...
```

### Why
Headers are the first search results and contain key information.

---

## Strategy 5 — Frequency-Based Boosting

### Principle
Information that appears often in dailies is probably important.

### Implementation (conceptual)
```
1. Count occurrences of each fact across dailies
2. If fact appears > 3 times → boost priority
3. If fact appears in recent dailies → boost recency
4. Combine frequency + recency for ranking
```

### Example
```
Fact: "User prefers short responses"
- Appears in: 5 dailies
- Last seen: 2 days ago
- Score: HIGH (frequent + recent)
```

---

## Strategy 6 — Hybrid Search (Concept)

### Principle
Combine semantic search + keyword matching.

#### Semantic Search (embeddings)
```python
# Pseudo-code
query_embedding = embed(user_query)
file_embedding = embed(file_content)
similarity = cosine_similarity(query_embedding, file_embedding)
```

#### Keyword Matching (BM25)
```python
# Pseudo-code
score = bm25_score(user_query, file_content)
```

#### Hybrid Combination
```python
final_score = 0.7 * semantic_similarity + 0.3 * keyword_score
```

### Current State
Current implementation uses `memory_search` (semantic search).
A hybrid version would be a future improvement.

---

## Strategy 7 — Chunking Strategy

### Principle
Split files into chunks with clear headers.

### Bad
```markdown
# Very Long File

[... 500 lines of unstructured content ...]
```

### Good
```markdown
# Example Tool

## Identity
[content]

## Configuration
[content]

## Usage
[content]
```

### Advantage
Retrieval can return a specific chunk without reading everything.

---

## Strategy 8 — Query Expansion

### Principle
Transform the user query into multiple variants.

### Process
```
User query: "how to create a Notion client?"

Expanded queries:
1. "how to create a Notion client?"
2. "create Notion API client"
3. "notion api client setup"
4. "initialize Notion API client"
5. "notion client python"
```

### Implementation (future)
```python
def expand_query(query):
    base = query
    fr_variants = translate_to_fr(query)
    en_variants = translate_to_en(query)
    synonyms = get_synonyms(query)
    return deduplicate([base] + fr_variants + en_variants + synonyms)
```

---

## Retrieval-Optimized File Template

```markdown
# [Entity Name] — [Type]

> Atomic entity — one [entity] per file.

## Meta
- **Created**: YYYY-MM-DD
- **Updated**: YYYY-MM-DD

## [Section 1 — Core Information]
Main content here.

## [Section 2 — Details]
More details.

### Related:
* path/to/related-file-1.md
* path/to/related-file-2.md

### Context:
Short explanation of the file: why it exists,
when it was created, what problem it solves.

### Keywords:
# Primary terms (exact match)
primary, main, key, essential, core

# French variants
primaire, principal, clé, essentiel, cœur

# English variants
primary, main, key, essential, core, fundamental

# Synonyms
synonym1, synonym2, alternative, equivalent

# Related terms
related-concept-1, related-concept-2, associated-term

# Technical terms
technical-term, jargon, specific-vocabulary

# Abbreviations
abbr, abbr2, short-form, acronym
```

---

## Step-by-Step Retrieval Process

```
1. RECEIVE QUERY from user or agent

2. GENERATE VARIANTS
   - Original query
   - FR translation
   - EN translation
   - Synonyms (via keywords in files)
   - Abbreviations expansion

3. SEARCH
   For each variant:
     Search MEMORY.md
     Search memory/**/*.md
     Search with exact phrase match
     Search with partial match

4. AGGREGATE RESULTS
   - Deduplicate identical results
   - Combine scores
   - Sort by relevance

5. RANK
   Factors:
   - Exact keyword match (high)
   - Section header match (high)
   - Body partial match (medium)
   - File name match (medium)
   - Recency (low boost)

6. RETURN TOP RESULTS
   Format:
   - File path
   - Relevant lines
   - Match context
   - Confidence score

7. IF NEEDED
   → Read full file
   → Follow Related: links
   → Return synthesized answer
```

---

## Best Practices

| Practice | Why |
|----------|-----|
| Put keywords first | Highlight for search |
| Use both languages | FR + EN = double coverage |
| Keep files short | Easier to chunk |
| Use descriptive headers | Improves matching |
| Link files together | Graph navigation |
| Update keywords | When new term discovered |
| Test retrieval regularly | Validate it works |

---

## Anti-Patterns to Avoid

| Anti-pattern | Problem | Solution |
|--------------|---------|----------|
| File too long | Chunking failure | Split into multiple files |
| Empty keywords | No match | Always include keywords |
| No Related: | Isolation | Always link related files |
| Vague headers | Poor match | "Notes" → "Q1 2026 Sales Metrics" |
| Duplicates | Inconsistency | One file per entity |
| Sensitive data | Security risk | Never in public repo |
