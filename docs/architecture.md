# Architecture — 5 Couches

> Comment le système de mémoire est architecturé en couches independientes mais interconnectées.

---

## Vue d'ensemble

Le système fonctionne comme un **pipeline de traitement de l'information** :

```
DonnÉes brutes → Nettoyage → Stockage structurÉ → Liens → Retrieval
   (session)     (distillation)   (atomic)     (graph)   (search)
```

Chaque couche a une responsabilité unique et peut être améliorée独立的.

---

## Couche 1 — Capture (`memory/dailies/`)

### Responsabilité
Collecter **tout** sans filtrer. La capture est un funnel large.

### Principe
> "Ne jamais filtrer à la étape de capture."

### Format
```markdown
## [HH:MM] — [Contexte]

* Information: ...
* Decision: ...
* Insight: ...
* Error: ... (optionnel)
```

### Pourquoi sans filtre ?
- On ne sait pas ce qui sera pertinent plus tard
- Les insights unexpected sont souvent les plus précieux
- Le bruit peut être supprimé lors de la distillation

### Règle absolue
- **Écrire** dans `memory/dailies/YYYY-MM-DD.md`
- **Ne jamais** écrire dans MEMORY.md pendant la session
- **Ne jamais** modifier les fichiers atomiques pendant la session

---

## Couche 2 — Distillation (CRON)

### Responsabilité
Transformer les logs bruts en **mémoire condensée**.

### Processus
```
1. Lire tous les dailies depuis .last_distill_date
2. Extraire: Decision + Information + Insight + Error
3. Filtrer: ne garder que les faits stables (> 1 mois pertinent)
4. Formuler: reformuler clairement pour retrieval
5. Distribuer: ecrire dans les bons fichiers atomiques
6. Logger: mettre à jour .last_distill_date
```

### Sorties
- `MEMORY.md` — sections `## Distilled Updates` avec date
- `memory/projects/*.md` — mis à jour si nécessaire
- `memory/tools/*.md` — nouveau tool → nouveau fichier
- `memory/people/*.md` — nouvelle personne → nouveau fichier
- `memory/summaries/self-review.md` — si pattern détecté

### Fréquence
- **Cron**: 21h Paris quotidien
- **Premier run**: traite tous les dailies
- **Runs suivants**: ne traite que les nouveaux fichiers

---

## Couche 3 — Mémoire Atomique (`memory/*/`)

### Responsabilité
Stocker **une entité par fichier** avec un seul sujet.

### Structure
```
memory/
├── projects/     # Un fichier = un projet
├── tools/        # Un fichier = un outil/config
├── people/       # Un fichier = une personne
├── ideas/        # Un fichier = une liste d'idées
└── summaries/    # Un fichier = une période
```

### Template de fichier atomique
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

### Règle
> Un fichier doit pouvoir être compréhensible sans lire les autres.

---

## Couche 4 — Graph Linking

### Responsabilité
Créer des **chemins de navigation** entre fichiers.

### Sections ajoutées

**`### Related:`**
```markdown
### Related:
* memory/projects/example-project.md
* memory/tools/notion-api.md
* memory/people/john-doe.md
```
→ Liens directs vers les fichiers liés

**`### Context:`**
```markdown
### Context:
Ce fichier document le projet X. Il a été créé suite à...
```
→ Explication courte du lien et de l'origine

**`### Keywords:`**
```markdown
### Keywords:
projet, project, example, demo, fictional
projet example, projet demo, projet fictif
projet X, projet initial
```
→ Synonyms + variantes FR/EN + termes associés

### Pourquoi ?
- `memory_search` search dans le contenu + les keywords
- Les Related: créent des chemins de navigation
- Le Context: aide à comprendre le contexte sans lire le fichier

---

## Couche 5 — Retrieval Optimization

### Responsabilité
Maximiser la **probabilité de trouver** la bonne information.

### Stratégies

#### 1. Synonyms massifs
```markdown
### Keywords:
password, mdp, mot de passe, credentials, login, auth, access, clé
```
→ Meme si l'utilisateur tape "mdp", on retrouve "password"

#### 2. Multi-formulation des concepts
```markdown
## Reformulated Concepts
- "memory" / "mémoire" / "MEMORY" / "long-term memory"
- "agent" / "agent IA" / "AI agent" / "bot"
- "cron" / "scheduled task" / "tâche planifiée" / "automation"
```

#### 3. FR/EN mixing
```markdown
### Keywords:
notion, Notion, database, base de données, bloc notes intelligent
OS, operating system, système, mission control, workspace
```

#### 4. Hybrid search (concept)
> Note: L'implémentation actuelle utilise search sémantique via `memory_search`.
> Une version hybrid (semantic + BM25) serait ideale pour le future.

### Processus de retrieval
```
1. Query utilisateur
2. Générer variants (FR/EN/synonyms)
3. Chercher dans: MEMORY.md + tous les fichiers atomiques
4. Retourner top results avec path + lines
5. Si unclear → lire le fichier complet
```

---

## Interaction entre couches

```
[Session]
    ↓ BOOT: lit SOUL.md + USER.md + dailies
[Context chargé]
    ↓ Interaction
[Append to daily] ← Couche 1
    ↓ cron 21h
[Distillation] ← Couche 2
    ↓
[Atomic files] ← Couche 3
    ↓
[Graph links] ← Couche 4
    ↓
[Mise à jour keywords + related]
    ↓
[Query] → retrieval ← Couche 5
    ↓
[Response]
```

---

## Scalabilité

| Aspect | Limite actuelle | Amélioration possible |
|--------|-----------------|----------------------|
| Fichiers atomiques | N fichiers | Partition par domaine |
| Keywords | Manuel | Génération auto via LLM |
| Graph links | Manuel | Auto-détection via embeddings |
| Retrieval | memory_search | Hybrid BM25 + semantic |

---

## Principes de design

1. **Séparation des concerns** — Chaque couche a un job clair
2. **Pas de duplication** — Un fait = un endroit
3. **Append-only pour les logs** — Jamais overwrite
4. **Distillation aggressive** — Garder MEMORY.md court
5. **Atomicité** — Une entité = un fichier
6. **Graph explicite** — Pas de liens implicites
