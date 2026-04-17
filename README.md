# OpenClaw Memory System

> Un système de mémoire atomique,蒸馏é, graphée et auto-améliorant pour agents IA.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🎯 Pourquoi ce système ?

Les agents IA souffre de trois problèmes fondamentaux :

| Problème | Solution apportée |
|----------|-------------------|
| **Perte de contexte** entre sessions | Fichiers `dailies/` + append systématique |
| **Mémoire saturée** de logs bruts | Distillation automatique quotidienne |
| **Retrieval failure** quand on a besoin | Graph + keywords + synonyms |

Ce système transforme une mémoire brute en **système de connaissance exploitable**.

---

## 🏗️ Architecture en 5 couches

```
┌─────────────────────────────────────────────────────────────┐
│                    1. CAPTURE (dailies)                     │
│   Every interaction → append to memory/dailies/YYYY-MM-DD   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   2. DISTILLATION (cron)                    │
│   Daily at 21h: extract decisions, insights, errors         │
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
│   Related: paths to related files                          │
│   Keywords: FR/EN synonyms + variants                      │
│   Context: why this file exists                            │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              5. RETRIEVAL OPTIMIZATION                      │
│   Semantic search via memory_search                         │
│   Keywords + synonyms + reformulations                    │
│   Hybrid: semantic + keyword matching                       │
└─────────────────────────────────────────────────────────────┘
```

---

## 📂 Structure du système de fichiers

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
│   ├── LEARNINGS.md            # Corrections, best practices
│   ├── ERRORS.md               # Command failures
│   └── FEATURE_REQUESTS.md     # Missing capabilities
│
└── .last_distill_date          # State file for cron
```

---

## ⚡ Fonctionnement

### Session start (BOOT.md)
1. Lire `SOUL.md` + `USER.md`
2. Lire daily du jour + veille
3. Charger MEMORY.md si session principale
4. Ready to respond

### During session
- Chaque interaction importante → append à `memory/dailies/YYYY-MM-DD.md`
- Format: `* Decision:*` / `* Information:*` / `* Insight:*`

### End of day (CRON 21h Paris)
1. Lit tous les dailies depuis dernière distillation
2. Extrait décisions + insights + errors
3. Append à MEMORY.md (section `## Distilled Updates`)
4. Met à jour les fichiers atomiques si changement
5. Self-review: analyse `.learnings/` pending
6. Log date dans `.last_distill_date`

---

## 🔄 Pipeline complet

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

## 🛡️ Sécurité des données

| Règle | Application |
|-------|------------|
| Aucune donnée réelle dans le repo | Toutes les données sont fictives |
| Credentials jamais committed | `.gitignore` + pas de tokens |
| Logs bruts isolés | `memory/dailies/` n'est pas pushé |
| Promo only after distillation | MEMORY.md = only clean facts |

---

## 📚 Documentation

- [Architecture](./docs/architecture.md) — Détail des 5 couches
- [Memory Pipeline](./docs/memory-pipeline.md) — Flux de données complet
- [Retrieval System](./docs/retrieval-system.md) — Synonyms, keywords, hybrid search
- [Self-Improvement](./docs/self-improvement.md) — Boucle de feedback

## 📖 Exemples

- [Sample Memory Structure](./examples/sample-memory-structure.md)
- [Sample Daily Log](./examples/sample-daily-log.md)
- [Sample Distillation Output](./examples/sample-distillation-output.md)

---

## 🚀 Installation

Ce système est conçu pour OpenClaw. Pour l'installer :

```bash
# Clone this repo
git clone https://github.com/your-handle/openclaw-memory-system.git
mkdir -p ~/.openclaw/workspace/memory/
cp -r openclaw-memory-system/* ~/.openclaw/workspace/
```

Ou lancez simplement les commands dans votre workspace OpenClaw existant.

---

## 📝 License

MIT — Utilisez librement, contribuez avec plaisir.
