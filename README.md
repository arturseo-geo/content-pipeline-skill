# content-pipeline-skill

> Built by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **The GEO Lab** · [𝕏 @TheGEO\_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

![Version](https://img.shields.io/badge/version-1.1.0-blue)
![Licence](https://img.shields.io/badge/licence-MIT-green)
![Agents](https://img.shields.io/badge/agents-7-orange)
![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](https://github.com/arturseo-geo/content-pipeline-skill/blob/main/CONTRIBUTING.md)

Multi-agent content production pipeline for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) where 7 specialist agents work together to research, write, edit, optimise for SEO/GEO, and validate against live analytics — with a master agent reviewing all outputs before human approval.

## Who This Is For

- **Content teams** who want AI-assisted production with human quality gates
- **Solo creators** who need research → writing → editing → SEO in one workflow
- **SEO practitioners** who want content optimised for both traditional search and AI citation
- **Anyone building on Claude Code** who wants a reference architecture for multi-agent pipelines

## What Makes This Different

Most "content AI" tools generate text and call it done. This skill orchestrates a full pipeline:

- ✅ **7 specialist agents** — research, writer, editor, SEO/GEO optimizer, analytics, GEO quality gate, master QA gate
- ✅ **GEO Quality Gate** — 5-question per-section checklist ensuring every H2 is citation-ready, pronoun-resolved, single-topic, data-backed, and opens with a direct answer
- ✅ **Agent handoff protocols** — structured context passing between agents with token budgets
- ✅ **Quality checkpoints** — gates between every pipeline stage, not just at the end
- ✅ **Analytics integration** — pulls live GSC + GA4 data to inform content decisions
- ✅ **GEO optimisation** — not just SEO keywords, but AI extractability and citation readiness
- ✅ **Human approval gate** — pipeline halts before publishing, outputs to `.claude/pipeline/`
- ✅ **Slash command** — trigger with `/content-pipeline` inside Claude Code
- ✅ **Production-tested** — part of the [**The GEO Lab**](https://thegeolab.net) toolkit for AI-assisted content operations

## Pipeline Flow

```
Research Agent → Writer Agent → Editor Agent → SEO/GEO Agent
                                                     ↓
                                              Analytics Agent
                                                     ↓
                                              Master Agent (GEO Quality Gate → GO / NEEDS REVISION)
                                                     ↓
                                              Human Approval → Publish
```

## Install

```bash
# Clone
git clone https://github.com/arturseo-geo/content-pipeline-skill.git ~/.claude/skills/content-pipeline

# Or install all skills at once
git clone https://github.com/arturseo-geo/claude-code-skills.git
cp -r claude-code-skills/skills/content-pipeline ~/.claude/skills/
```

## File Structure

```
content-pipeline-skill/
├── SKILL.md                  — Core skill instructions and pipeline orchestration
├── agents/
│   ├── master-agent.md       — Final QA gate: runs GEO Quality Gate, reviews all outputs, GO/NEEDS REVISION
│   ├── geo-quality-gate.md   — 5-question per-section GEO compliance checklist (run by Master Agent as Step 0)
│   ├── research-agent.md     — Web search, topic analysis, competitor content
│   ├── writer-agent.md       — Draft creation from research + analytics briefs
│   ├── editor-agent.md       — Quality, accuracy, brand voice, humanisation
│   ├── seo-geo-agent.md      — Keyword optimisation, schema, GEO readiness
│   └── analytics-agent.md    — GSC + GA4 + DataForSEO data pull
├── commands/
│   └── content-pipeline.md   — Slash command: /content-pipeline
├── references/
│   ├── agent-handoffs.md     — Agent-to-agent context passing and token budgets
│   ├── quality-gates.md      — Quality checkpoints between pipeline stages
│   └── analytics-integration.md — Data source setup and fallback behaviour
└── .github/                  — Issue templates and PR template
```

## GEO Quality Gate (new in v1.1.0)

The Master Agent now runs a mandatory 5-question GEO quality gate on every H2 section before any other review checks:

1. **Direct opener** — Does the section open with a declarative answer, not context-dependent preamble?
2. **Pronoun resolution** — Are all pronouns replaced with explicit named entities?
3. **Single topic** — Does every paragraph serve one concept only?
4. **Verifiable data** — Is there at least one cited statistic or named study?
5. **Citation-ready** — Can the opening sentence stand alone as a quote with author attribution?

Any section failure triggers NEEDS REVISION with specific rewrite instructions for the Writer Agent.

## Related Repos

- [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills) — Full collection of skills
- [seo-geo-skill](https://github.com/arturseo-geo/seo-geo-skill) — The SEO/GEO skill used by the pipeline's optimizer agent
- [mcp-wordpress-setup](https://github.com/arturseo-geo/mcp-wordpress-setup) — WordPress MCP server for publishing pipeline output

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. PRs welcome.

---

Built and maintained by **[Artur Ferreira](https://github.com/arturseo-geo)** · Part of the **[The GEO Lab](https://thegeolab.net)** toolkit · [MIT License](LICENSE)
