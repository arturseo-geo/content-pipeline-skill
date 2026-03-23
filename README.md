# content-pipeline-skill

> Built by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)**
> [𝕏 @TheGEO_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

![Version](https://img.shields.io/badge/version-1.0.0-blue)
![Licence](https://img.shields.io/badge/licence-MIT-green)
![Agents](https://img.shields.io/badge/agents-6-orange)
![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](https://github.com/arturseo-geo/content-pipeline-skill/blob/main/CONTRIBUTING.md)

Multi-agent content production pipeline for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) where 6 specialist agents work together to research, write, edit, optimise for SEO/GEO, and validate against live analytics — with a master agent reviewing all outputs before human approval.

## Who This Is For

- **Content teams** who want AI-assisted production with human quality gates
- **Solo creators** who need research → writing → editing → SEO in one workflow
- **SEO practitioners** who want content optimised for both traditional search and AI citation
- **Anyone building on Claude Code** who wants a reference architecture for multi-agent pipelines

## What Makes This Different

Most "content AI" tools generate text and call it done. This skill orchestrates a full pipeline:

- ✅ **6 specialist agents** — research, writer, editor, SEO/GEO optimizer, analytics, master QA gate
- ✅ **Agent handoff protocols** — structured context passing between agents with token budgets
- ✅ **Quality checkpoints** — gates between every pipeline stage, not just at the end
- ✅ **Analytics integration** — pulls live GSC + GA4 data to inform content decisions
- ✅ **GEO optimisation** — not just SEO keywords, but AI extractability and citation readiness
- ✅ **Human approval gate** — pipeline halts before publishing, outputs to `.claude/pipeline/`
- ✅ **Slash command** — trigger with `/content-pipeline` inside Claude Code

## Pipeline Flow

```
Research Agent → Writer Agent → Editor Agent → SEO/GEO Agent
                                                     ↓
                                              Analytics Agent
                                                     ↓
                                              Master Agent (GO / NEEDS REVISION)
                                                     ↓
                                              Human Approval → Publish
```

## Install

```bash
# Clone
git clone https://github.com/arturseo-geo/content-pipeline-skill.git ~/.claude/skills/content-pipeline

# Or install all 12 skills at once
git clone https://github.com/arturseo-geo/claude-code-skills.git
cp -r claude-code-skills/skills/content-pipeline ~/.claude/skills/
```

## File Structure

```
content-pipeline-skill/
├── SKILL.md                  — Core skill instructions and pipeline orchestration
├── agents/
│   ├── master-agent.md       — Final QA gate: reviews all outputs, GO/NEEDS REVISION
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

## Related Repos

- [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills) — Full collection of 12 skills
- [seo-geo-skill](https://github.com/arturseo-geo/seo-geo-skill) — The SEO/GEO skill used by the pipeline's optimizer agent
- [mcp-wordpress-setup](https://github.com/arturseo-geo/mcp-wordpress-setup) — WordPress MCP server for publishing pipeline output

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. PRs welcome.

---

Built and maintained by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)** · [MIT License](LICENSE)
