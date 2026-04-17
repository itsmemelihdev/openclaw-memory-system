# Retrieval System — Comment retrouver l'information

> Stratégies pour maximiser la probabilité de trouver la bonne information au bon moment.

---

## Le problème fondamental

Un système de mémoire n'est utile que si on peut **retrouver** l'information quand on en a besoin.

Mais :

- L'utilisateur ne tape pas les mots exacts
- L'information peut être formulée différemment
- Le contexte change entre storage et retrieval
- Les synonyms et variations existent dans les deux langues

---

## Stratégie 1 — Keywords massifs

### Principe
Dans chaque fichier, inclure **toutes les variantes** du même concept.

### Exemple basique
```markdown
### Keywords:
password, mdp, mot de passe, credentials, login, auth, access, clé, clé d'accès
```

### Multi-langue (FR/EN)
```markdown
### Keywords:
notion, Notion, database, base de données, bloc notes intelligent
OS, operating system, système, mission control, workspace
crontab, cron, scheduled task, tâche planifiée, automation, job
```

### Synonyms techniques
```markdown
### Keywords:
API key, clé API, token, bearer token, authorization header
webhook, callback, notification, trigger, event
embedding, vector, embedding model, semantic vector
```

### Variantes orthographiques
```markdown
### Keywords:
Behavior, behaviour (UK vs US)
Color, colour
Center, centre
Organisation, organization
```

---

## Stratégie 2 — Reformulated Concepts

### Principe
Dans `MEMORY.md`, lister les mêmes concepts sous plusieurs formulations.

### Exemple
```markdown
## Reformulated Concepts — Multi-phrase retrieval

- "mémoire long terme" / "long term memory" / "persistent memory" / "memory bank"
- "bras droit数字" / "chief of staff agent" / "agentique" / "AI chief of staff"
- "système mémoire" / "memory system" / "agent memory architecture"
- "distillation mémoire" / "memory distillation" / "cron mémoire" / "automated memory cleanup"
- "récupération mémoire" / "memory retrieval" / "semantic search" / "memory search"
```

### Pourquoi ça marche
Quand l'utilisateur cherche "memory bank", le search trouve aussi les fichiers qui contain "mémoire long terme".

---

## Stratégie 3 — Graph Linking

### Principe
Les fichiers se pointent entre eux, créant des chemins de navigation.

### Structure
```markdown
### Related:
* memory/projects/example-project.md      ← projet lié
* memory/tools/notion-api.md              ← outil utilisé
* memory/people/john-doe.md               ← personne impliquée

### Context:
Ce fichier documente le projet X. Il a été créé suite à
la demande de l'utilisateur en avril 2026.
```

### Avantage
Quand on cherche "projet X", on trouve aussi "outil Y" et "personne Z" qui y sont liés.

---

## Stratégie 4 — Contextual Headers

### Principe
Chaque fichier commence par un header descriptif.

### Mauvais
```markdown
# Notes

Projet en cours...
```

### Bon
```markdown
# Example Project — AI Content Generation System

> Atomic entity — one project per file.
> Status: Active | Created: 2026-04-01 | Updated: 2026-04-15

## Overview
This project tracks the development of...
```

### Pourquoi
Les headers sont les premiers résultats du search et contiennent l'information clé.

---

## Stratégie 5 — Frequency-based Boosting

### Principe
Les informations qui apparaissent souvent dans les dailies sont probablement importantes.

### Implémentation (conceptuelle)
```
1. Count occurrences of each fact across dailies
2. If fact appears > 3 times → boost priority
3. If fact appears in recent dailies → boost recency
4. Combine frequency + recency for ranking
```

### Exemple
```
Fact: "User prefers short responses"
- Appears in: 5 dailies
- Last seen: 2 days ago
- Score: HIGH (frequent + recent)
```

---

## Stratégie 6 — Hybrid Search (Concept)

### Principe
Combiner search sémantique + keyword matching.

#### Search sémantique (embeddings)
```python
# Pseudo-code
query_embedding = embed(user_query)
file_embedding = embed(file_content)
similarity = cosine_similarity(query_embedding, file_embedding)
```

#### Keyword matching (BM25)
```python
# Pseudo-code
score = bm25_score(user_query, file_content)
```

#### Hybrid combination
```python
final_score = 0.7 * semantic_similarity + 0.3 * keyword_score
```

### État actuel
L'implémentation actuelle utilise `memory_search` (search sémantique).
Une version hybrid serait une amélioration future.

---

## Stratégie 7 — Chunking Strategy

### Principe
Diviser les fichiers en chunks avec des headers claros.

### Mauvais
```markdown
# Very Long File

[... 500 lines de contenu sans structure ...]
```

### Bon
```markdown
# Example Tool

## Identity
[content]

## Configuration
[content]

## Usage
[content]
```

### Avantage
Le retrieval peut retourner un chunk specific sans tout lire.

---

## Stratégie 8 — Query Expansion

### Principe
Transformer la requête utilisateur en plusieurs variantes.

### Processus
```
User query: "comment créer un client Notion ?"

Expanded queries:
1. "comment créer un client Notion ?"
2. "créer client Notion API"
3. "notion api client setup"
4. "initialize Notion API client"
5. "notion client python"
```

### Implémentation (future)
```python
def expand_query(query):
    base = query
    fr_variants = translate_to_fr(query)
    en_variants = translate_to_en(query)
    synonyms = get_synonyms(query)
    return deduplicate([base] + fr_variants + en_variants + synonyms)
```

---

## Template de fichier optimisé pour retrieval

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
Explication courte du fichier: pourquoi il existe,
quand il a été créé, quel problème il résout.

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

## Processus de retrieval étape par étape

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
   - Dédoublonner les résultats identiques
   - Combiner les scores
   - Trier par relevance

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

## Meilleures pratiques

| Practice | Pourquoi |
|----------|----------|
| Mettre les mots-clés en premier | Highlight pour le search |
| Utiliser les deux langues | FR + EN = double coverage |
| Garder les fichiers courts | Plus facile à chunk |
| Utiliser des headers descriptifs | Améliore le matching |
| Lier les fichiers entre eux | Graph navigation |
| Mettre à jour les keywords | Quand nouveau terme découvert |
| Tester le retrieval régulièrement | Valider que ça marche |

---

## Anti-patterns à éviter

| Anti-pattern | Problem | Solution |
|--------------|---------|----------|
| Fichier trop long | Chunking failure | Split en plusieurs fichiers |
| Keywords vides | No match | Toujours inclure keywords |
| Pas de Related: | Isolation | Always link related files |
| Headers vagues | Poor match | "Notes" → "Q1 2026 Sales Metrics" |
| Doublons | Inconsistency | Un seul fichier par entité |
| Données sensibles | Security risk | Never in public repo |
