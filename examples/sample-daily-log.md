# Sample Daily Log — Exemple fictif

> Ce fichier montre à quoi ressemble un daily log après une journée de session.
> Toutes les données sont **fictives**.

---

## Fichier: `memory/dailies/2026-04-03.md`

```markdown
# 2026-04-03 — Daily Log (Example)

> This is a RAW log. No filtering at this stage.
> Will be processed during nightly distillation.

---

## [09:15] — Session start

* Information: User (Alice, fictional) connected from New York
* Information: First session of the day
* Decision: Read BOOT.md checklist before responding
* Context: New project week starting today

## [09:20] — Project review

* Information: AI Content Generator project is in Phase 2 (prototype)
* Decision: User wants to move to Phase 3 by end of week
* Insight: Need to prioritize prompt testing over documentation

## [09:45] — OpenAI API issue

* Error: API returned 429 (rate limit exceeded)
* Information: Had 50 requests queued, hit limit
* Insight: Need to implement request throttling
* Decision: Add exponential backoff to retry logic
* Suggested Fix: Use openai-rate-limiter library

## [10:30] — Client meeting prep

* Information: First client demo scheduled for tomorrow
* Decision: Prepare 3 sample outputs (blog post, email, social)
* Task: Create before/after comparison for demo
* Context: Client is "ShopXYZ" — fictional e-commerce brand

## [11:00] — Tool setup

* Information: Added DALL-E 3 integration for images
* Decision: Use Dall-E for hero images, Midjourney for variations
* Insight: Midjourney API still in beta, risky for production
* Preference: User prefers DALL-E for consistency

## [14:00] — Afternoon session

* Information: Bob (mentor) suggested targeting B2B instead of B2C
* Decision: Test both segments in Q2
* Insight: B2B has higher ACV but longer sales cycle
* Knowledge Gap: Need to research B2B AI content pricing

## [15:30] — Technical implementation

* Information: Implemented content caching to reduce API calls
* Decision: Cache prompts for 24h, results for 7 days
* Error: Cache invalidation not working correctly (fixed)
* Improvement: Use Redis instead of in-memory for scalability

## [17:00] — End of day

* Information: 47 API calls made today (vs 80+ yesterday)
* Insight: Caching reduced costs by ~40%
* Decision: Document this optimization for weekly report
* Next: Test with 3 real client scenarios tomorrow

---

## Summary (auto-generated)

**Decisions today**: 5
**Errors**: 2 (both resolved)
**Insights**: 4
**Tasks for tomorrow**: 3
**API costs**: ~$2.30 (down from $3.80 yesterday)
```

---

## Comment lire ce fichier

### Ce qui est capturé
- **Decisions**: Choix explicites ou implicites
- **Information**: Facts, context, reminders
- **Insights**: Patterns observés, intuitions
- **Errors**: Ce qui a échoué + correction appliquée
- **Tasks**: Actions identifiées
- **Preferences**: Ce que l'utilisateur préfère

### Ce qui N'est PAS ici
- Pensées privées de l'agent
- Logs techniques detalliés
- Données sensibles (clé API, passwords)
- Conversations complètes (trop long)

---

## Format standardisé

```markdown
## [HH:MM] — [Contexte]

* Information: [Fact ou contexte]
* Decision: [Choix fait]
* Insight: [Pattern ou intuition]
* Error: [Ce qui a échoué]
* Correction: [Comment corrigé] (si applicable)
* Task: [Tâche identifiée]
* Preference: [Ce que l'utilisateur préfère]
* Idea: [Idée en vrac]
```

---

## Règles de capture

| Règle | Pourquoi |
|-------|----------|
| Append only | On ne modifie jamais |
| Timestamps FR/EN | Universal |
| Une想法 par line | Facilite le parsing |
| Pas de secrets | Jamais dans daily |
| Pas de logs techniques | Bruit, pas pertinent |
| Filter later | On ne sait pas ce qui sera utile |

---

## Transformation vers distillation

Ce log sera transformé par le cron 21h en :

```
## Distilled 2026-04-03

### Decisions
- Move AI Content Generator to Phase 3 by end of week
- Test B2B and B2C segments in Q2
- Use DALL-E for hero images, Midjourney for variations
- Implement content caching (Redis)
- Document cost optimization for weekly report

### Facts
- First client demo tomorrow (ShopXYZ)
- 47 API calls today (40% reduction from caching)
- Bob suggested B2B pivot
- Rate limit issue resolved with backoff

### Insights
- Prompt testing > documentation for Phase 3
- B2B has higher ACV, longer sales cycle
- Caching significantly reduces costs

### Errors (resolved)
- API 429 rate limit → exponential backoff added
- Cache invalidation → switched to Redis

### Next Steps
- Prepare 3 sample outputs for client demo
- Test with 3 real client scenarios
- Research B2B AI content pricing
```

---

## Sécurisation des données

| Donnée fictive | Remplacé par |
|----------------|--------------|
| Alice | example user |
| Bob | fictional mentor |
| ShopXYZ | fictional client |
| API key | NOT included |
| Real pricing | Generic examples |
| Specific costs | Percentages only |
