# Memory Pipeline — Flux complet des données

> Comment une information voyage de la session utilisateur jusqu'au système de retrieval.

---

## Vue d'ensemble du pipeline

```
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 1: CAPTURE                                                   │
│  ┌──────────┐    ┌──────────────────┐    ┌────────────────────────┐  │
│  │ Session  │───→│ Every interaction │───→│ Append to daily log   │  │
│  │ (chat)   │    │ important info    │    │ memory/dailies/YYYY-  │  │
│  └──────────┘    └──────────────────┘    │  MM-DD.md              │  │
│                                          └────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 2: DISTILLATION (cron 21h)                                   │
│  ┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐  │
│  │ Read all dailies │───→│ Extract:          │───→│ Filter &       │  │
│  │ since last run   │    │ Decision          │    │ reformulate   │  │
│  │                  │    │ Information        │    │                │  │
│  │                  │    │ Insight            │    │ Keep only      │  │
│  │                  │    │ Error              │    │ stable facts  │  │
│  └──────────────────┘    └──────────────────┘    └────────────────┘  │
│                                                           │         │
│                                                           ↓         │
│  ┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐  │
│  │ Update atomic    │←───│ Distribute to    │←───│ Write to       │  │
│  │ files if needed  │    │ right files      │    │ MEMORY.md      │  │
│  └──────────────────┘    └──────────────────┘    └────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 3: STORAGE (atomic files)                                    │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│  │ projects/   │  │ tools/       │  │ people/      │              │
│  │ example.md  │  │ example.md   │  │ example.md   │              │
│  └──────────────┘  └──────────────┘  └──────────────┘              │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐                                │
│  │ ideas/      │  │ summaries/   │                                │
│  │ todo.md     │  │ weekly.md    │                                │
│  └──────────────┘  └──────────────┘                                │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│  PHASE 4: RETRIEVAL (on-demand)                                    │
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

###触发
Toute interaction utilisateur significative.

### Règle
> "Append only — never overwrite."

### Format dans `memory/dailies/YYYY-MM-DD.md`

```markdown
## [HH:MM] — [Contexte ou titre]

* Information: L'utilisateur a mentionné un nouveau projet
* Decision: On utilise Notion comme OS principal
* Insight: Le client prefere les responses courtes
* Error: L'API Notion a retourné une erreur 400

## [HH:MM] — [Contexte suivant]

* Information: ...
```

### Ce qu'on capture (sans filtrer)
- Décisions (explicites ou implicites)
- Informations contextuelles
- Insights et intuitions
- Erreurs et corrections
- Préférences utilisateurs
- Tâches identifiées
- Idées en vrac

### Ce qu'on ne fait PAS
- On n'édite pas les fichiers existants
- On n'efface pas
- On ne filtre pas (tout peut être utile)

---

## Phase 2 — Distillation (CRON)

### Schedule
```bash
0 21 * * *  # Every day at 21:00 Paris
```

### Processus détaillé

```
1. LIRE .last_distill_date
   └─ Si 2026-04-16 → traiter tous les fichiers > 2026-04-16

2. POUR CHAQUE daily:
   Extraire les lignes contenant:
   - "* Decision:"
   - "* Information:"
   - "* Insight:"
   - "* Error:"

3. POUR CHAQUE extraction:
   - Dédupliquer (si même info plusieurs fois)
   - Évaluer la stabilité (va durer +1 mois ?)
   - Reformuler clairement

4. DISTRIBUER:
   a. MEMORY.md → append section "## Distilled YYYY-MM-DD HH:MM"
   b. memory/projects/NOM.md → créer ou mettre à jour
   c. memory/tools/NOM.md → créer ou mettre à jour
   d. memory/people/NOM.md → créer ou mettre à jour
   e. memory/summaries/self-review.md → si pattern récurrent

5. ÉCRIRE .last_distill_date = today

6. SELF-REVIEW:
   - Lire .learnings/LEARNINGS.md (pending entries)
   - Lire .learnings/ERRORS.md (pending entries)
   - Si > 3 pending OU pattern récurrent:
     → Écrire dans self-review.md
     → Proposer action corrective
   - Marquer entries résolues
```

### Critères de distillation

| Type | Garder ? | Raison |
|------|----------|--------|
| Décision stratégique | ✅ Oui | Stable long terme |
| Information factuelle | ✅ Oui | Reference |
| Preference utilisateur | ✅ Oui | Important pour UX |
| Erreur corrigée | ✅ Oui | Ne pas répéter |
| Insight contextuel | ⚠️ Selon | Si recurring → garder |
| Log technique | ❌ Non | Bruit, pas pertinent |
| Thought process | ❌ Non | Déjà dans session |

---

## Phase 3 — Stockage (Atomic Files)

### Template standard

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
Explication courte: pourquoi ce fichier existe, comment il s'insère
dans le système.

### Keywords:
word1, word2, synonym1, synonym2, variantFR, variantEN, abbreviation
related-term1, related-term2, technical-term, category-name
```

### Exemple: `memory/tools/notion-api.md`

```markdown
# Notion API — Tool

> Atomic entity — one tool per file.

## Configuration
- API Key: stored in ~/.config/notion/api_key
- Base URL: https://api.notion.com/v1/

## Database IDs (exemple)
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
Outil utilisé pour piloter le Notion OS. Toutes les operations
CRM passent par cette API.

### Keywords:
Notion, API, database, query, page, block, notion.so, notion API
base de données, requête API, integration Notion
```

---

## Phase 4 — Retrieval (On-demand)

###触发
Quand Jarvis fait `memory_search` (appel obligatoire avant de répondre sur des faits passés).

### Processus

```
1. QUERY: "comment on upload un fichier sur Notion ?"

2. GENERER VARIANTS:
   - Original: "upload fichier Notion"
   - FR: "uploader fichier Notion", "ajouter fichier Notion"
   - EN: "upload file to Notion", "attach file Notion"
   - Synonyms: "attach", "link", "fichier", "file", "media"

3. SEARCH:
   Pour chaque variant:
   - Grep dans MEMORY.md
   - Grep dans memory/**/*.md
   - Avec keywords expandus

4. RANK:
   - Exact match → score élevé
   - Partial match → score moyen
   - Keyword match → score faible

5. RETURN:
   Top 3-5 résultats avec:
   - Path du fichier
   - Lines pertinentes
   - Score de confiance

6. SI résultat unclear:
   → Lire le fichier complet
   → Répondre avec l'information trouvée
```

### Exemple de search

```
Query: " Notion API database query"

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

## Boucle complète (visual)

```
    ┌──────────────────────────────────────────────────────────────┐
    │                                                               │
    ▼                                                               │
[Session] ──append──→ [dailies/]                                    │
                              │                                     │
                              │ cron 21h                           │
                              ↓                                     │
                      [Distillation]                               │
                              │                                     │
              ┌───────────────┼───────────────┐                     │
              ↓               ↓               ↓                     │
       [MEMORY.md]    [atomic files]   [self-review]              │
              │               │               │                     │
              └───────────────┼───────────────┘                     │
                              │                                     │
                              ↓                                     │
                      [Storage layer]                               │
                              │                                     │
                              │ memory_search                       │
                              ↓                                     │
                       [Retrieval]                                 │
                              │                                     │
                              └──────────────→ [Response]            │
                                                        ▲          │
                                                        │          │
                                            [User query] ──┘          │
```

---

## State Management

### Fichier: `.last_distill_date`

```
2026-04-16
```

→ Le cron sait exactement quels dailies traiter.

### Fichier: `HEARTBEAT.md`

```
# Keep this file empty (or with only comments) to skip heartbeat API calls.
```

→ Si vide, pas de heartbeat polling.

---

## Error Handling

| Situation | Action |
|-----------|--------|
| Daily file corrompu | Skip, log error in `.learnings/ERRORS.md` |
| Cron fails | Retry next day, pas de perte (les dailies restent) |
| Atomic file manquant | Le créer à la prochaine distillation |
| Duplicate entry | Dédoublonner avant écriture |

---

## Sécurité

### Données séparées

```
Repo public (ce repository)
├── README.md
├── docs/
├── examples/
└── .github/

Workspace privé (jamais pushé)
├── MEMORY.md          ← Contains real data
├── memory/dailies/   ← Contains real logs
├── memory/projects/  ← Contains real projects
└── .learnings/       ← Contains real learnings
```

### Règle
> Jamais push `MEMORY.md`, `memory/dailies/`, ou `.learnings/` dans un repo public.
