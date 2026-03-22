# Agent Handoffs Reference

## Why Handoffs Matter

Each agent runs in its own context window — they share no memory.
All communication happens through files in `.claude/pipeline/`.

This is intentional:
- Prevents context contamination between agents
- Each agent starts fresh with only what it needs
- Reduces token usage (each agent only loads its inputs)
- Outputs are auditable — you can read exactly what each agent produced

## Handoff Map

```
brief.md ──────────────────────────────────────────────┐
    │                                                   │
    ├──→ Research Agent ──→ research.md                 │
    │                           │                       │
    ├──→ Analytics Agent ──→ analytics.md               │
    │                           │                       │
    │         ┌─────────────────┘                       │
    │         │                                         │
    └─────────┼──→ Writer Agent ──→ draft.md            │
              │                        │                │
              │         ┌──────────────┘                │
              │         │                               │
              └─────────┼──→ Editor Agent ──→ edited-draft.md
                        │                        │
                        │         ┌──────────────┘
                        │         │
                        └─────────┼──→ SEO Agent ──→ final-draft.md
                                  │               └──→ seo-report.md
                                  │                        │
                                  │    ┌───────────────────┘
                                  │    │
                                  └────┼──→ Master Agent ──→ master-review.md
                                       │
                                    (all files)
```

## What Each Agent Receives

| Agent | Reads | Does NOT see |
|---|---|---|
| Research | brief.md | everything else |
| Analytics | brief.md | everything else |
| Writer | brief.md + research.md + analytics.md | draft history |
| Editor | brief.md + research.md + draft.md | analytics.md |
| SEO/GEO | brief.md + analytics.md + edited-draft.md | draft.md, research.md |
| Master | ALL files | nothing hidden |

## Completion Signals

Each agent writes a COMPLETE status line as its final output line.
The orchestrator checks for these before spawning the next phase:

| Agent | Signal |
|---|---|
| Research | `RESEARCH COMPLETE` |
| Analytics | `ANALYTICS COMPLETE` |
| Writer | `DRAFT COMPLETE` |
| Editor | `EDITING COMPLETE` |
| SEO/GEO | `SEO COMPLETE` (in both output files) |
| Master | Ends with PIPELINE PAUSED message |

## Re-running Individual Agents

When re-running after feedback, append a revision context section to the agent's input:

```markdown
## Revision Context
Previous run output: [path to existing output file]
Feedback from human: [exact feedback]
Feedback from Master Agent: [relevant section from master-review.md]
Instruction: Revise the previous output addressing the feedback above.
             Write the revised version to the same output file (overwrite).
```

## Token Budget Per Agent

Guidelines to keep each agent's context lean:

| Agent | Approx input tokens | Approx output tokens |
|---|---|---|
| Research | ~500 (brief) | ~2,000 (research brief) |
| Analytics | ~500 (brief) | ~1,500 (analytics brief) |
| Writer | ~4,000 (brief+research+analytics) | ~3,000–6,000 (draft) |
| Editor | ~8,000 (brief+research+draft) | ~3,000–6,000 (edited draft) |
| SEO/GEO | ~6,000 (brief+analytics+edited) | ~4,000 (final + report) |
| Master | ~15,000 (all files) | ~2,000 (review) |

Total pipeline cost: ~30,000–40,000 tokens for a standard blog post.
Using Sonnet for all agents keeps this affordable.
Use Haiku for Research and Analytics (data gathering, not creative).
Use Sonnet for Writer, Editor, SEO, Master.
