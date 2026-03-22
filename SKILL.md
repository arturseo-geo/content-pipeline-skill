---
name: content-pipeline
description: >
  Run a multi-agent content production pipeline where specialist agents work
  together to research, write, edit, optimise for SEO/GEO, and validate against
  analytics data — with a master agent reviewing all outputs before human
  approval. Use this skill whenever the user wants to produce content using
  multiple agents, asks about agent pipelines, content automation, multi-agent
  content workflow, orchestrated content creation, or wants one agent to search
  the web while others write and optimise. Also trigger when the user says
  "run the content pipeline", "create content for [topic]", or wants to produce
  a piece of content that goes through research, writing, editing, SEO, and
  analytics review stages before approval. The pipeline produces content into
  .claude/pipeline/ and halts for human approval before publishing.
---

# Content Pipeline Skill

A multi-agent content production workflow. Six specialist agents collaborate,
each writing outputs to `.claude/pipeline/`. The Master Agent reviews all outputs
and halts for your approval before anything is published.

## Pipeline Overview

```
User request
    ↓
Orchestrator (you — or auto via /content-pipeline command)
    ↓
[Phase 1 — parallel]
  Research Agent     → .claude/pipeline/research.md
  Analytics Agent    → .claude/pipeline/analytics.md
    ↓
[Phase 2 — sequential, depends on Phase 1]
  Writer Agent       → .claude/pipeline/draft.md
    ↓
  Editor Agent       → .claude/pipeline/edited-draft.md
    ↓
  SEO/GEO Agent      → .claude/pipeline/seo-report.md
                     → .claude/pipeline/final-draft.md
    ↓
[Phase 3]
  Master Agent       → .claude/pipeline/master-review.md
    ↓
  ⏸ HUMAN APPROVAL — review master-review.md, then say "approved" or give feedback
    ↓
  Publish / revise
```

## Agent Roster

Load agent files from `.claude/agents/` as needed:

| Agent | File | Role |
|---|---|---|
| Research | `research-agent.md` | Web search, topic analysis, competitor content |
| Analytics | `analytics-agent.md` | GSC + GA4 + DataForSEO data pull |
| Writer | `writer-agent.md` | Draft creation from research brief |
| Editor | `editor-agent.md` | Quality, accuracy, brand voice, humanisation |
| SEO/GEO | `seo-geo-agent.md` | Keyword optimisation, schema, GEO readiness |
| Master | `master-agent.md` | Final QA gate, human-ready summary |

## Running the Pipeline

### Full pipeline (recommended)
```
/content-pipeline "topic or brief here"
```

### Single agent (re-run one stage)
```
/run-agent writer "rewrite the draft with a more conversational tone"
/run-agent seo "re-optimise for keyword: [new keyword]"
```

### Review current pipeline state
```
/pipeline-status
```

## Pipeline Output Files

All files written to `.claude/pipeline/` (gitignored by default):

```
.claude/pipeline/
├── brief.md           ← input: topic + requirements
├── research.md        ← Research Agent output
├── analytics.md       ← Analytics Agent output
├── draft.md           ← Writer Agent output
├── edited-draft.md    ← Editor Agent output
├── seo-report.md      ← SEO Agent analysis
├── final-draft.md     ← SEO Agent optimised draft
├── master-review.md   ← Master Agent final review
└── approved/          ← content that passed approval
```

## Approval Flow

The pipeline always pauses before publishing. The Master Agent produces
`master-review.md` with:
- Summary of what each agent did
- Quality scores per dimension
- Any concerns or flags
- The final draft ready to publish

Your responses:
- **"approved"** → pipeline publishes (or hands off to WordPress skill)
- **"revise: [feedback]"** → re-runs specific agents with your feedback
- **"redo research"** → restarts from Phase 1
- **"fix SEO only"** → re-runs SEO agent on existing draft

## Token Efficiency Notes

Each agent runs in its own context window — they don't share memory.
Communication happens entirely through the `.claude/pipeline/` files.
Keep each agent's context lean: give it only the files it needs, not the full history.

See `references/agent-handoffs.md` for the exact context each agent receives.
