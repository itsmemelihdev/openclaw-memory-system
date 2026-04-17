# Sample Memory Structure

> This file shows the complete structure of a memory system.
> All data is **fictional** — inspired by the real design but without sensitive data.

---

## Global Structure

```
~/.openclaw/workspace/
├── MEMORY.md                    # Central hub
├── BOOT.md                      # Startup checklist
│
├── memory/
│   ├── dailies/                 # Raw session logs
│   │   ├── 2026-04-01.md
│   │   ├── 2026-04-02.md
│   │   └── 2026-04-03.md
│   │
│   ├── projects/                # One file per project
│   │   ├── ai-content-generator.md
│   │   └── newsletter-platform.md
│   │
│   ├── tools/                  # One file per tool
│   │   ├── notion-api.md
│   │   ├── openai-api.md
│   │   ├── github-api.md
│   │   └── openclaw.md
│   │
│   ├── people/                 # One file per person
│   │   ├── alice-demo.md
│   │   └── bob-mentor.md
│   │
│   ├── ideas/                  # Ideas and todos
│   │   └── backlog.md
│   │
│   └── summaries/             # Periodic syntheses
│       ├── weekly-2026-W14.md
│       └── self-review.md
│
├── .learnings/                 # Self-improvement loop
│   ├── LEARNINGS.md
│   ├── ERRORS.md
│   └── FEATURE_REQUESTS.md
│
└── .last_distill_date          # State: last distillation
```

---

## MEMORY.md (example)

```markdown
# MEMORY.md — Long-Term Memory

> Assistant is the chief of staff for example user Alice.
> This file contains only stable, validated, distilled information.

---

## User

- **Name**: Alice (example user)
- **Timezone**: America/New_York
- **Business**: AI content creation agency
- **Preferred language**: English (primary)

## Projects

- `memory/projects/ai-content-generator.md` — AI content tool
- `memory/projects/newsletter-platform.md` — Email automation

## Tools

- `memory/tools/notion-api.md` — Notion OS
- `memory/tools/openai-api.md` — GPT-4 API
- `memory/tools/openclaw.md` — Agent platform

## People

- `memory/people/alice-demo.md` — The user (fictional)
- `memory/people/bob-mentor.md` — Mentor (fictional)

---

## System Architecture

### Memory Files
- `memory/dailies/` — raw session logs
- `memory/projects/` — one file per project
- `memory/tools/` — one file per tool
- `memory/people/` — one file per person
- `memory/ideas/` — backlog and todos
- `memory/summaries/` — periodic syntheses

### Cron Jobs
- Memory Distillation: daily at 21h Paris (Europe/Paris)

---

## Reformulated Concepts

- "memory" / "mémoire" / "MEMORY" / "long-term memory" / "persistent memory"
- "chief of staff" / "bras droit" / "assistant" / "agent"
- "distillation" / "cleanup" / "memory cleaning" / "cron memory"

### Keywords:
memory, MEMORY, assistant, alice, chief of staff, long-term memory,
agent memory, workspace memory, files, atomic memory, architecture,
dailies, distillation, cleanup, cron, self-improvement
```

---

## Sample Atomic File — Project

```markdown
# AI Content Generator — Project

> Atomic entity — one project per file.

## Overview
Fictional project for generating AI-powered content at scale.

## Goal
Build a system to create blog posts, social media content,
and email newsletters using AI.

## Stack
- OpenAI GPT-4 for text generation
- DALL-E for images
- Notion for content management
- Make.com for automation

## Status
Phase 2: Testing prompts

## Milestones
- [x] Phase 1: Research (2026-03)
- [x] Phase 2: Prototype (2026-04)
- [ ] Phase 3: Beta launch (2026-05)
- [ ] Phase 4: Monetization (2026-06)

## Next Steps
- Test GPT-4o with 20 sample prompts
- Measure output quality vs manual writing
- Calculate cost per article

### Related:
* memory/tools/openai-api.md
* memory/tools/notion-api.md
* memory/people/bob-mentor.md

### Context:
Created as part of Alice's AI agency setup.
First project after founding the company.

### Keywords:
AI, content generation, GPT-4, DALL-E, blog, social media,
email, newsletter, automation, Make.com, content pipeline
artificial intelligence, text, image generation, content,
automatic writing, AI content generation
```

---

## Sample Atomic File — Tool

```markdown
# OpenAI API — Tool

> Atomic entity — one tool per file.

## Configuration
- API Endpoint: https://api.openai.com/v1/
- Model default: gpt-4o
- Max tokens: 4096
- Temperature: 0.7

## Credentials
- Key stored in: ~/.env (OPENAI_API_KEY)
- Never commit .env to git

## Usage Example
```bash
curl -s -X POST "https://api.openai.com/v1/chat/completions" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

## Models Available
- gpt-4o (latest, recommended)
- gpt-4-turbo
- gpt-3.5-turbo (cheaper)
- dall-e-3 (images)

## Rate Limits
- 500 requests/minute (Tier 1)
- $100/month free credits (new accounts)

### Related:
* memory/projects/ai-content-generator.md
* memory/tools/openclaw.md

### Context:
Primary AI model used for content generation.
All AI features depend on this API.

### Keywords:
OpenAI, API, GPT, GPT-4, GPT-4o, chat, completions,
tokens, prompt, model,人工智能, OpenAI API
dall-e, dall-e-3, image generation, vision
```

---

## Sample Atomic File — Person

```markdown
# Bob Mentor — Person

> Atomic entity — one person per file.

## Identity
- **Name**: Bob (fictional mentor)
- **Role**: Business mentor
- **Relation**: Mentor for Alice's agency
- **Timezone**: Europe/London

## Background
- 15 years in tech
- Founded 3 startups
- Expert in AI/ML
- Angel investor

## Interactions
- Weekly call: Tuesday 10am London
- Topics: Business strategy, AI trends, fundraising

## Notes
- Very direct feedback style
- Prefers data-driven decisions
- Good at finding flaws in plans

### Related:
* memory/projects/ai-content-generator.md
* memory/people/alice-demo.md

### Context:
Bob has been mentoring Alice since March 2026.
Helped define the initial positioning.

### Keywords:
Bob, mentor, business mentor, tech entrepreneur, startup,
investor, advisor, AI expert, expert IA, mentorship
```

---

## Sample Atomic File — Ideas

```markdown
# Backlog — Ideas

> Atomic entity — ideas and tasks not yet processed.

## Priority Queue

### High
- [ ] Add image generation to content pipeline
- [ ] Set up analytics dashboard
- [ ] Write 3 sample client proposals

### Medium
- [ ] Explore Midjourney API integration
- [ ] Research competitors' pricing
- [ ] Create onboarding documentation

### Low
- [ ] Design logo
- [ ] Set up company email
- [ ] Explore Shopify integration

## Random Ideas
- Could use AI for video scripts too
- Maybe partner with a design agency
- Consider B2B2C model eventually

### Related:
* memory/projects/ai-content-generator.md
* memory/summaries/weekly-2026-W14.md

### Context:
Central backlog for all unprocessed ideas.
Reviewed weekly during distillation.

### Keywords:
ideas, backlog, todo, tasks, pending, à faire,
features, requests, suggestions, improvements
```

---

## Sample Atomic File — Summary

```markdown
# Weekly Summary — W14 2026

> Week of March 31 to April 6 (fictional)

## Accomplishments
- Completed GPT-4o prompt testing (20 samples)
- First client meeting (positive feedback)
- Defined pricing structure

## Decisions
- Use Notion as primary OS
- Target mid-market e-commerce first
- Focus on blog + email (skip social initially)

## Challenges
- Prompt quality inconsistent for long-form
- Need better image generation workflow

## Next Week
- Finalize 3 client proposals
- Set up analytics
- Book more discovery calls

### Related:
* memory/projects/ai-content-generator.md
* memory/people/alice-demo.md

### Context:
Week 14 summary for Alice's AI agency.
First week with real client conversations.

### Keywords:
weekly, summary, recap, W14, 2026, accomplishments,
decisions, challenges, next steps, progress
```
