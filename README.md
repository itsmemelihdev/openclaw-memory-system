# OpenClaw Memory System

> An atomic, distilled, graph-linked, and self-improving memory system for AI agents.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🎯 Why This System?

AI agents suffer from three fundamental problems:

| Problem | Solution |
|---------|----------|
| **Context loss** between sessions | `dailies/` files + systematic append |
| **Memory overload** with raw logs | Automatic daily distillation |
| **Retrieval failure** when needed | Graph + keywords + synonyms |

This system transforms raw memory into an **exploitable knowledge system**.

---

## 🏗️ 5-Layer Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    1. CAPTURE (dailies)                     │
│   Every interaction → append to memory/dailies/YYYY-MM-DD    │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   2. DISTILLATION (cron)                    │
│   Daily at 21h Paris: extract decisions, insights, errors     │
│   → MEMORY.md (long-term) + atomic files                    │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                  3. ATOMIC MEMORY (files)                   │
│   projects/ · tools/ · people/ · ideas/ · summaries/        │
│   One entity = one file = one source of truth               │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│               4. GRAPH LINKING (relationships)              │
│   Related: paths to related files                            │
│   Keywords: FR/EN synonyms + variants                        │
│   Context: why this file exists                              │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              5. RETRIEVAL OPTIMIZATION                      │
│   Semantic search via memory_search                          │
│   Keywords + synonyms + reformulations                      │
│   Hybrid: semantic + keyword matching                        │
└─────────────────────────────────────────────────────────────┘
```

---

## 📂 File Structure

```
workspace/
├── MEMORY.md                    # Hub — long-term memory only
├── BOOT.md                      # Startup checklist
│
├── memory/
│   ├── dailies/                 # Raw session logs (never filter here)
│   │   └── YYYY-MM-DD.md
│   │
│   ├── projects/                # One file = one project
│   │   └── example-project.md
│   │
│   ├── tools/                   # One file = one tool
│   │   ├── notion-api.md
│   │   ├── openai-api.md
│   │   └── openclaw.md
│   │
│   ├── people/                  # One file = one person
│   │   ├── example-user.md
│   │   └── contact.md
│   │
│   ├── ideas/                   # Backlog, todos
│   │   └── todo.md
│   │
│   └── summaries/               # Periodic syntheses
│       ├── weekly.md
│       └── self-review.md
│
├── .learnings/                  # Self-improvement loop
│   ├── LEARNINGS.md             # Corrections, best practices
│   ├── ERRORS.md                # Command failures
│   └── FEATURE_REQUESTS.md      # Missing capabilities
│
└── .last_distill_date           # State file for cron
```

---

## ⚡ How It Works

### Session Start (BOOT.md)
1. Read `SOUL.md` + `USER.md`
2. Read today's daily + yesterday's daily
3. Load MEMORY.md if main session
4. Ready to respond

### During Session
- Every important interaction → append to `memory/dailies/YYYY-MM-DD.md`
- Format: `* Decision:*` / `* Information:*` / `* Insight:*`

### End of Day (CRON 21h Paris)
1. Read all dailies since last distillation
2. Extract decisions + insights + errors
3. Append to MEMORY.md (section `## Distilled Updates`)
4. Update atomic files if changed
5. Self-review: analyze `.learnings/` pending entries
6. Log date in `.last_distill_date`

---

## 🔄 Complete Pipeline

```
[Session]
   ↓ append every interaction
memory/dailies/YYYY-MM-DD.md
   ↓ cron 21h (distillation)
[Extraction: Decision + Information + Insight + Error]
   ↓
   ├── MEMORY.md (distilled long-term facts)
   ├── memory/projects/*.md (if project changed)
   ├── memory/tools/*.md (if tool added/updated)
   ├── memory/people/*.md (if new person)
   ├── memory/ideas/todo.md (if new idea)
   └── memory/summaries/self-review.md (if pattern detected)
   ↓
[memory_search] ← semantic retrieval at query time
   ↓
[Response]
```

---

## 🛡️ Data Security

| Rule | Application |
|------|-------------|
| No real data in repo | All data is fictional |
| Credentials never committed | `.gitignore` + no tokens |
| Raw logs isolated | `memory/dailies/` not pushed |
| Promo only after distillation | MEMORY.md = clean facts only |

---

## 📚 Documentation

- [Architecture](./docs/architecture.md) — 5 layers detailed
- [Memory Pipeline](./docs/memory-pipeline.md) — Complete data flow
- [Retrieval System](./docs/retrieval-system.md) — Synonyms, keywords, hybrid search
- [Self-Improvement](./docs/self-improvement.md) — Feedback loop

## 📖 Examples

- [Sample Memory Structure](./examples/sample-memory-structure.md)
- [Sample Daily Log](./examples/sample-daily-log.md)
- [Sample Distillation Output](./examples/sample-distillation-output.md)

---

## 🚀 Installation

This system is designed for OpenClaw. To install:

```bash
# Clone this repo
git clone https://github.com/your-handle/openclaw-memory-system.git
mkdir -p ~/.openclaw/workspace/memory/
cp -r openclaw-memory-system/* ~/.openclaw/workspace/
```

Or run the commands in your existing OpenClaw workspace.

---

## 📝 License

MIT — Use freely, contribute gladly.
