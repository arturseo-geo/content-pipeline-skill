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

## Pipeline Configuration

### Customising the pipeline

Override defaults by adding options to your brief or command invocation:

| Setting | Default | How to override |
|---|---|---|
| Content type | blog post | `--type guide`, `--type landing-page` |
| Primary keyword | inferred from topic | `--keyword "exact phrase"` |
| Target audience | inferred | `--audience "marketing managers"` |
| Word count | analytics-driven | `--words 2000` |
| Brand voice | conversational-authoritative | add `Voice:` section to brief.md |
| Skip analytics | no | `--skip-analytics` (useful when APIs are unavailable) |

### Custom agent instructions

Append per-run overrides to any agent by adding a `## Custom Instructions` section
to the brief. The orchestrator passes these to the relevant agent:

```markdown
## Custom Instructions
Writer: Use a more technical tone — audience are developers.
SEO: Target Spanish-language keywords as secondary.
Editor: Keep British English spelling throughout.
```

## Quality Gates Between Stages

Every transition between agents passes through a quality gate.
If a gate fails, the pipeline halts with a clear error instead of producing
garbage output downstream.

See `references/quality-gates.md` for the full specification.

| Gate | Between | Checks |
|---|---|---|
| Gate 1 | Research/Analytics → Writer | Both files exist, both show COMPLETE status, research has >= 3 sources |
| Gate 2 | Writer → Editor | Draft exists, word count within 50% of target, meta section present |
| Gate 3 | Editor → SEO | Edited draft exists, EDITING COMPLETE signal, no unresolved RESEARCH GAP markers |
| Gate 4 | SEO → Master | Both final-draft.md and seo-report.md exist, SEO score >= 50/100 |
| Gate 5 | Master → Human | master-review.md exists, contains GO or NEEDS REVISION decision |

If a gate fails, the orchestrator reports which gate failed, why, and which
agent to re-run. It does not silently skip stages.

## Analytics Integration

The Analytics Agent connects to real data sources to ground content decisions
in actual performance data rather than guesswork.

See `references/analytics-integration.md` for connection setup and fallback behaviour.

**Supported data sources:**
- Google Search Console (via FastAPI proxy or MCP)
- GA4 / Google Analytics (via FastAPI proxy or MCP)
- DataForSEO API (keyword research, SERP analysis)
- Ahrefs CSV import (manual — no API on free tier)

**When APIs are unavailable:**
The Analytics Agent degrades gracefully. If an API call fails, it notes the failure
in analytics.md and proceeds with available data. If ALL sources fail, it produces
a minimal analytics.md with `ANALYTICS PARTIAL` status and the Writer Agent
adjusts accordingly (relies more heavily on the Research Agent output).

## Error Handling

When an individual agent fails mid-execution:

1. **Agent timeout** — if an agent produces no output within 5 minutes, the
   orchestrator marks it as failed and reports the failure. The pipeline does not
   proceed past a failed required agent.

2. **Partial output** — if an agent writes a file but without the COMPLETE signal,
   the orchestrator treats it as incomplete. It will retry the agent once with the
   same inputs. If the second attempt also fails, the pipeline halts.

3. **API failures inside agents** — agents handle their own API errors (e.g.,
   Analytics Agent notes failed API calls). The quality gate after the agent
   checks whether the output is still usable.

4. **Revision loops** — if the Master Agent returns NEEDS REVISION more than
   3 times for the same pipeline run, the orchestrator flags it as a stuck loop
   and asks the human to intervene with specific guidance.

5. **Recovery** — at any point, `/pipeline-status` shows exactly which stages
   completed and which failed. You can re-run a single agent with
   `/run-agent [name] "[instruction]"` to recover without restarting the full pipeline.

## Token Efficiency Notes

Each agent runs in its own context window — they don't share memory.
Communication happens entirely through the `.claude/pipeline/` files.
Keep each agent's context lean: give it only the files it needs, not the full history.

See `references/agent-handoffs.md` for the exact context each agent receives.
