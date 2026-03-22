# Quality Gates Reference

Quality gates are automated checkpoints between pipeline stages.
They prevent bad output from cascading downstream — catching problems early
saves tokens and avoids wasted agent runs.

## How Gates Work

After each agent writes its output, the orchestrator runs the corresponding
gate check before spawning the next agent. If the gate fails, the pipeline
halts with a specific error message and does not proceed.

```
Agent completes → Gate check runs → PASS: spawn next agent
                                  → FAIL: halt, report error, suggest fix
```

Gates are intentionally strict on required fields and lenient on subjective
quality. The Master Agent handles subjective review — gates handle structural
correctness.

## Gate Definitions

### Gate 1: Research/Analytics → Writer

**Triggered after:** Research Agent and Analytics Agent both complete (Phase 1).

**Checks:**
| Check | Condition | Failure action |
|---|---|---|
| File exists | `research.md` exists and is non-empty | Re-run Research Agent |
| File exists | `analytics.md` exists and is non-empty | Re-run Analytics Agent |
| Completion signal | `research.md` ends with `RESEARCH COMPLETE` | Re-run Research Agent |
| Completion signal | `analytics.md` ends with `ANALYTICS COMPLETE` or `ANALYTICS PARTIAL` | Re-run Analytics Agent |
| Minimum sources | `research.md` contains at least 3 URLs in Sources section | Re-run Research Agent with "expand sources" instruction |
| Structure present | `research.md` contains "Recommended Structure" section | Re-run Research Agent |

**Partial pass:** If analytics.md has `ANALYTICS PARTIAL` status (API failures),
the gate still passes but appends a note to the Writer Agent's context:
"Analytics data is incomplete — lean more heavily on research brief."

### Gate 2: Writer → Editor

**Triggered after:** Writer Agent completes (Phase 2).

**Checks:**
| Check | Condition | Failure action |
|---|---|---|
| File exists | `draft.md` exists and is non-empty | Re-run Writer Agent |
| Completion signal | `draft.md` ends with `DRAFT COMPLETE` | Re-run Writer Agent |
| Meta section | `draft.md` contains YAML front matter with `title:` field | Re-run Writer with "add meta section" |
| Word count | Draft is at least 300 words | Re-run Writer with "content is too thin" |
| Word count ceiling | Draft is under 10,000 words | Re-run Writer with "content is too long, tighten" |
| No placeholders | No `[TODO]` or `[TBD]` markers present | Re-run Writer with "resolve all placeholders" |

### Gate 3: Editor → SEO/GEO

**Triggered after:** Editor Agent completes (Phase 3).

**Checks:**
| Check | Condition | Failure action |
|---|---|---|
| File exists | `edited-draft.md` exists and is non-empty | Re-run Editor Agent |
| Completion signal | `edited-draft.md` ends with `EDITING COMPLETE` | Re-run Editor Agent |
| Editor report | `edited-draft.md` contains `## Editor Report` section | Re-run Editor with "include editor report" |
| Research gaps | No unresolved `[RESEARCH GAP]` markers remain | Flag to human — needs manual decision |
| Word count | Edited draft is within 20% of original draft length | Pass with warning (large edits noted) |

### Gate 4: SEO/GEO → Master

**Triggered after:** SEO/GEO Agent completes (Phase 4).

**Checks:**
| Check | Condition | Failure action |
|---|---|---|
| Files exist | Both `final-draft.md` and `seo-report.md` exist | Re-run SEO Agent |
| Completion signal | Both files end with `SEO COMPLETE` | Re-run SEO Agent |
| SEO score | Total score in `seo-report.md` is >= 50/100 | Re-run SEO Agent with "score too low, optimise further" |
| Schema present | `seo-report.md` contains a JSON-LD block | Re-run SEO Agent with "add schema markup" |
| Keywords defined | `seo-report.md` lists at least one primary keyword | Re-run SEO Agent |

### Gate 5: Master → Human

**Triggered after:** Master Agent completes (Phase 5).

**Checks:**
| Check | Condition | Failure action |
|---|---|---|
| File exists | `master-review.md` exists and is non-empty | Re-run Master Agent |
| Decision present | Contains "GO" or "NEEDS REVISION" | Re-run Master Agent |
| Pause message | Ends with PIPELINE PAUSED message | Re-run Master Agent |
| Quality table | Contains the Quality Gate Results table | Re-run Master Agent |

## Gate Failure Reporting

When a gate fails, the orchestrator outputs:

```
PIPELINE HALTED at Gate [N]: [Gate Name]

Failed check: [specific check that failed]
Reason: [why it failed]
Recommended fix: [which agent to re-run and with what instruction]

Run `/run-agent [name] "[instruction]"` to fix, or `/pipeline-reset` to start over.
```

## Overriding Gates

In rare cases, you may want to skip a gate (e.g., running without analytics).
Use `--skip-gate [N]` to bypass a specific gate. The orchestrator logs the skip
in master-review.md so the Master Agent knows which checks were skipped.

Do not skip Gate 5 (Master → Human) — human approval is always required.
