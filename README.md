# content-pipeline-skill

> **Also available as part of [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills)** — a collection of 12 production-tested skills for Claude Code.

> Built by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)**
> [𝕏 @TheGEO_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

![Licence](https://img.shields.io/badge/licence-MIT-green)
![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)

Multi-agent content production pipeline where specialist agents work together to research, write, edit, optimize for SEO/GEO, and validate against analytics data — with a master agent reviewing all outputs before human approval.

## Install

```bash
git clone https://github.com/arturseo-geo/content-pipeline-skill.git ~/.claude/skills/content-pipeline
```

## File Structure

```
content-pipeline-skill/
├── SKILL.md                  — Core skill instructions and pipeline orchestration
├── README.md                 — This file
├── CONTRIBUTING.md           — Contribution guidelines
├── SECURITY.md               — Security policy
├── LICENSE                   — MIT licence
├── .gitignore
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
└── .github/
    ├── ISSUE_TEMPLATE/
    │   ├── bug-report.md     — Bug report template
    │   └── platform-update.md — Pipeline enhancement request template
    └── pull_request_template.md — PR template
```

## Related Repos

- [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills) — Full collection of 12 skills
- [mcp-wordpress-setup](https://github.com/arturseo-geo/mcp-wordpress-setup) — WordPress MCP server setup

## Acknowledgments

Built following the open-source best practice approach — reading community work for inspiration, writing original content, and crediting every source.

**Based on:**
- [Agent Skills specification](https://github.com/anthropics/skills) by Anthropic (Apache 2.0)

All skill content is original writing. No files were copied or adapted from any source.

## Author

Built and maintained by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)**

## License

[MIT](LICENSE)
