# Self-Improvement — Boucle de feedback continu

> Comment le système de mémoire apprend de ses erreurs et s'améliore automatiquement.

---

## Principe fondamental

Un système de mémoire **statique** se dégrade avec le temps :

- Accumulation de bruit
- Information outdated
- Retrieval failure non corrigé
- Structure inadaptée aux nouveaux besoins

Un système **auto-améliorant** :
- Identifie ses propres erreurs
- Corrige les problèmes récurrents
- Évolution avec les besoins

---

## Architecture de la boucle

```
┌─────────────────────────────────────────────────────────────┐
│                    OUTER LOOP (weekly)                      │
│                                                              │
│  ┌─────────────┐    ┌──────────────┐    ┌──────────────┐   │
│  │ Self-review │───→│ Pattern       │───→│ Structural    │   │
│  │ analysis    │    │ detection     │    │ improvements  │   │
│  └─────────────┘    └──────────────┘    └──────────────┘   │
│         │                                     │             │
│         └─────────────────────────────────────┘             │
│                         ↓                                    │
│              ┌──────────────────────┐                        │
│              │ Update MEMORY.md     │                        │
│              │ Update files        │                        │
│              │ (keywords, related)  │                        │
│              └──────────────────────┘                        │
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
              │ Events during session:    │
              │ - Error occurs           │
              │ - User corrects          │
              │ - Missing capability     │
              │ - Better approach found  │
              └───────────────────────────┘
```

---

## Les 3 fichiers de learnings

### `.learnings/LEARNINGS.md`
```markdown
# Learnings

Corrections, insights, and knowledge gaps captured.

**Categories**: correction | insight | knowledge_gap | best_practice
```

**Quand populer :**
- User dit "No, that's wrong..."
- User corrige une information
- On découvre qu'on avait tort
- On trouve une meilleure approche

**Format d'entrée :**
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

**Quand populer :**
- Commande échoue (exit code != 0)
- API retourne une erreur
- Exception ou stack trace
- Timeout ou connection failure

**Format d'entrée :**
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

**Quand populer :**
- User demande quelque chose qui n'existe pas
- "Can you also..."
- "I wish you could..."
- "Is there a way to..."

**Format d'entrée :**
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

**Fichier**: `memory/summaries/self-review.md`

### Structure
```markdown
# Self-Review — Memory System Improvement Log

## 📊 Scorecard — Santé du Système

| Composant | Status | Notes |
|-----------|--------|-------|
| Daily capture | ✅ | Opérationnel |
| Cron distillation | ✅ | Configuré |
| Graph mémoire | ✅ | En place |
| Retrieval optimization | ✅ | Terminé |
| Self-improvement loop | 🔄 | En construction |

## 🔄 Boucle d'Amélioration

### Trigger
- **Quand** : à chaque distillation (quotidien 21h) + review manuelle
- **Source** : `.learnings/`

### Processus
1. Lire les entrées `pending` dans `.learnings/`
2. Analyser les patterns récurrents
3. Identifier les problèmes de retrieval
4. Proposer des actions correctives
5. Mettre à jour les fichiers concernés
6. Marquer les entrées résolues

## 📝 Entries — Retrieval Errors & Improvements

### [REV-20260417-001] Setup initial
**Date**: 2026-04-17
**Type**: system_setup
**Status**: completed

**Summary**: ...
**Actions réalisées**: ...
**Prochaine review**: 2026-04-24
```

---

## Processus de Self-Review (cron daily)

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

### Types de patterns

| Pattern | Seuil | Action |
|---------|-------|--------|
| Retrieval failure | > 2/week | Add keywords, fix structure |
| Same error | > 2/week | Document workaround |
| Missing capability | > 1 occurrence | Log to FEATURE_REQUESTS |
| Knowledge gap | > 1 occurrence | Add to MEMORY.md |
| Better approach | 1 occurrence | Promote to best practice |

### Exemple de detection

```python
# Pseudo-code
def detect_retrieval_failures():
    """
    Si l'utilisateur dit quelque chose comme:
    - "Je t'avais déjà dit..."
    - "Why didn't you remember..."
    - "Tu devrais savoir que..."
    → C'est un retrieval failure
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

### Quand ?

- Même fichier modifié > 3 fois/semaine
- Même information dupliquée dans plusieurs fichiers
- Fichier trop long ( > 500 lignes)
- Retrieval qui échoue souvent pour un thème

### Types d'amélioration

| Problem | Solution |
|---------|----------|
| Fichier trop long | Split en plusieurs fichiers atomiques |
| Keywords insuffisants | Enrichir avec synonyms + FR/EN |
| Graph incomplet | Ajouter Related: links |
| Structure inadaptée | Refactorer (un fichier = une entité) |
| Information dupliquée | Consolid into single source |

### Exemple

**Avant** (problème) :
```
memory/
├── notes.md  (800 lignes, tout melangé)
```

**Après** (solution) :
```
memory/
├── projects/
│   └── example-project.md  (200 lignes)
├── tools/
│   └── example-tool.md     (150 lignes)
├── people/
│   └── john-doe.md         (100 lignes)
└── notes.md               (supprimé, distribué)
```

---

## Promotion Rules

### Quand promouvoir ?

| Condition | Destination |
|-----------|-------------|
| Best practice | SOUL.md ou AGENTS.md |
| Workflow improvement | AGENTS.md |
| Tool behavior | TOOLS.md |
| Project fact | MEMORY.md |
| Recurring pattern (3+) | SYSTEM FILE appropprié |

### Comment promouvoir

```markdown
# Avant (dans LEARNINGS.md)
**Status**: pending
**Suggested Action**: Add "Claude" as alias for "Claude.ai" in keywords

# Après (dans MEMORY.md)
## Reformulated Concepts
- "Claude" / "Claude.ai" / "claude.ai" / "Claude AI"

# Update dans LEARNINGS.md
**Status**: promoted
**Promoted**: MEMORY.md (Reformulated Concepts)
```

---

## Metrics à suivre

| Metric | Target | Alert |
|--------|--------|-------|
| Pending learnings | < 5 | > 10 |
| Retrieval failures | < 2/week | > 5 |
| Duplicate entries | 0 | > 1 |
| Files without Keywords | 0 | > 2 |
| Self-review entries | ~1/week | 0 (pas de review !) |

---

## Integration avec le cron

```bash
# Le cron 21h exécute:
1. Distillation (voir memory-pipeline.md)
2. Self-review (ce fichier)

# Message envoyé au cron:
"""
Tu es Jarvis. Exécute la distillation de mémoire + self-review :

DISTILLATION (1-5):
[... distillation steps ...]

SELF-REVIEW (6-9):
6. Lire .learnings/LEARNINGS.md et .learnings/ERRORS.md — entrées pending
7. Si entrées pending > 3 ou pattern récurrent détecté:
   - Ajoute entrée dans memory/summaries/self-review.md
   - Propose action corrective
8. Ferme les entrées résolues (status: resolved)
9. SI self-review.md a nouvelle entry significative
   → promouvoir vers MEMORY.md ou fichiers atomiques

SILENCIEUX: Écriture fichiers uniquement, pas de message.
"""
```

---

## Cycle de review

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

## Best practices

1. **Log immediately** — Contexte frais = plus précis
2. **Be specific** — Future Jarvis doit comprendre vite
3. **Suggest concrete fixes** — Pas juste "investigate"
4. **Link related entries** — Create web, not silo
5. **Promote aggressively** — Si doute, ajouter à MEMORY.md
6. **Review regularly** — Stale learnings = no value
