# GEO Quality Gate — Human Review Checklist

Apply this checklist to every AI-generated section before approving.
A section that fails any check should be sent back to the Writer Agent
with specific instructions referencing the failure.

This checklist is applied by the Master Agent during Phase 3 review,
BEFORE the human approval step. The Master Agent must run all five
questions on every H2 section in `final-draft.md` and report failures
in `master-review.md`.

## The Five Questions

### 1. Does this section open with a direct answer?

Read the first sentence. Does it state the core answer/claim without
requiring context from the paragraph before it?

If the first sentence is "In this section we explore..." or
"As we discussed..." — **REJECT**. Return to Writer Agent with:
> "Rewrite opening sentence as a direct declarative answer to
> the implied question of this section heading."

### 2. Is every pronoun resolved?

Scan for: it, this, they, them, the system, the tool, the process.
For each pronoun: can it be replaced with a specific named entity
without changing the meaning? If yes — it should be.

If a pronoun cannot be resolved without reading adjacent sections
— **REJECT**. Return with:
> "Replace all pronouns with explicit entity names. This section
> must be self-contained."

### 3. Does this section stay on one topic?

Name the primary topic of the section in one sentence.
Does every paragraph in the section serve that one topic?

If any paragraph introduces a second distinct concept — **REJECT**.
Return with:
> "Split into two sections or remove the off-topic paragraph.
> Each section must address exactly one concept."

### 4. Is there at least one verifiable data point?

Does the section contain a specific statistic, percentage, date,
count, or named study from a cited source?

If no — **ADD** before approving. Do not reject — add a data point
from the research brief or instruct the Writer Agent to add one.
Minimum: one data point per section for any factual claim.

### 5. Can the opening sentence be cited standalone?

Test: "[Opening sentence] — [Author], The GEO Lab"
Does that read as a coherent, complete claim?

If yes: the section is citation-ready.
If no: the opening sentence needs to be more specific and
claim-complete. Return with:
> "Rewrite the opening sentence so it can stand alone as a
> citation with author attribution. It must be specific,
> entity-named, and claim-complete."

## Approval Flow

- **ALL FIVE pass** → approve section
- **ONE OR MORE fail** → return to Writer Agent with specific failure description per question above
- **STATISTICS outdated** → flag to SEO/GEO Agent for replacement before final approval

## How the Master Agent Reports This

In `master-review.md`, add a new section after the existing quality gate results:

```markdown
## GEO Quality Gate (per-section)

| Section (H2) | Q1: Direct opener | Q2: Pronouns | Q3: Single topic | Q4: Data point | Q5: Citation-ready | Result |
|---|---|---|---|---|---|---|
| [heading] | PASS/FAIL | PASS/FAIL | PASS/FAIL | PASS/FAIL | PASS/FAIL | ✅/❌ |
```

Any section with one or more FAIL entries triggers a NEEDS REVISION decision
with specific rewrite instructions for the Writer Agent.
