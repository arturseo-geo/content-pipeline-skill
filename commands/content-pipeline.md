# /content-pipeline

Run the full multi-agent content production pipeline.

## Usage
```
/content-pipeline "topic or brief"
/content-pipeline "topic or brief" --type [blog|guide|landing-page]
/content-pipeline "topic or brief" --keyword "primary keyword" --audience "target audience"
```

## What This Command Does

1. **Creates the brief** — writes `.claude/pipeline/brief.md` from your input
2. **Phase 1 (parallel)** — spawns Research Agent and Analytics Agent simultaneously
3. **Waits** for both to complete (checks for COMPLETE status lines)
4. **Phase 2** — spawns Writer Agent (depends on Phase 1)
5. **Phase 3** — spawns Editor Agent (depends on Writer)
6. **Phase 4** — spawns SEO/GEO Agent (depends on Editor)
7. **Phase 5** — spawns Master Agent (reviews all outputs)
8. **Pauses** — presents master-review.md for your approval

## Step-by-Step Execution

### Step 1: Create pipeline directory and brief
```bash
mkdir -p .claude/pipeline
```

Write `.claude/pipeline/brief.md`:
```markdown
# Content Brief
Topic: [extracted from user input]
Content type: [blog post / guide / landing page — inferred or from --type flag]
Primary keyword: [from --keyword flag or infer from topic]
Target audience: [from --audience flag or infer]
Additional requirements: [any other specifics from user input]
Created: [timestamp]
```

### Step 2: Spawn Phase 1 agents in parallel
Dispatch two Task tool calls simultaneously:

**Task 1 — Research Agent:**
```
Read .claude/agents/research-agent.md for your instructions.
Input file: .claude/pipeline/brief.md
Output file: .claude/pipeline/research.md
```

**Task 2 — Analytics Agent:**
```
Read .claude/agents/analytics-agent.md for your instructions.
Input file: .claude/pipeline/brief.md
Output file: .claude/pipeline/analytics.md
```

Wait until both files contain their COMPLETE status line before proceeding.

### Step 3: Spawn Writer Agent
```
Read .claude/agents/writer-agent.md for your instructions.
Input files: .claude/pipeline/brief.md, .claude/pipeline/research.md, .claude/pipeline/analytics.md
Output file: .claude/pipeline/draft.md
```

Wait for DRAFT COMPLETE.

### Step 4: Spawn Editor Agent
```
Read .claude/agents/editor-agent.md for your instructions.
Input files: .claude/pipeline/brief.md, .claude/pipeline/research.md, .claude/pipeline/draft.md
Output file: .claude/pipeline/edited-draft.md
```

Wait for EDITING COMPLETE.

### Step 5: Spawn SEO/GEO Agent
```
Read .claude/agents/seo-geo-agent.md for your instructions.
Input files: .claude/pipeline/brief.md, .claude/pipeline/analytics.md, .claude/pipeline/edited-draft.md
Output files: .claude/pipeline/final-draft.md, .claude/pipeline/seo-report.md
```

Wait for SEO COMPLETE.

### Step 6: Spawn Master Agent
```
Read .claude/agents/master-agent.md for your instructions.
Input files: all .claude/pipeline/*.md files
Output file: .claude/pipeline/master-review.md
```

### Step 7: Present to human
Display the contents of `.claude/pipeline/master-review.md` directly in the conversation.
The pipeline is now paused. Do not take any further action until the user responds.

## After Approval

If user says **"approved"**:
- Move final-draft.md to `.claude/pipeline/approved/[slug].md`
- Offer to publish via WordPress skill if connected
- Suggest next steps from master-review.md

If user says **"revise: [feedback]"**:
- Identify which agent(s) need to re-run based on feedback
- Re-run only those agents with the feedback appended to their input context
- Re-run Master Agent after revisions
- Present updated master-review.md

If user says **"redo research"**:
- Re-run from Phase 1 (both Research + Analytics)
- Then run all subsequent phases

## Other Pipeline Commands

**`/pipeline-status`** — show which files exist and their completion status
**`/run-agent [name] "[instruction]"`** — re-run a single agent with custom instruction
**`/pipeline-reset`** — clear `.claude/pipeline/` and start fresh
