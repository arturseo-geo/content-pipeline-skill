# Contributing to content-pipeline-skill

Thank you for your interest in improving this skill. This document explains
how to contribute effectively.

## Before You Start

1. **Check existing issues** — your idea may already be tracked
2. **Read the existing agents** — understand the pipeline flow before changing it
3. **Read `references/agent-handoffs.md`** — understand how agents communicate

## What You Can Contribute

### Agent improvements
- Better prompts for existing agents (clearer instructions, better output format)
- New quality checks in the Master Agent
- Expanded data source support in the Analytics Agent

### New agents
- Must follow the existing agent file format (see any file in `agents/`)
- Must define: Inputs, Output, What You Do, Output Format, Rules
- Must include a completion signal (e.g., `AGENT_NAME COMPLETE`)
- Must be added to the handoff map in `references/agent-handoffs.md`
- Must be added to the Agent Roster table in `SKILL.md`

### Pipeline orchestration
- Quality gate improvements (see `references/quality-gates.md`)
- Better error recovery logic
- New pipeline commands

### Documentation
- Clearer explanations of existing behaviour
- Examples of pipeline runs
- Troubleshooting guides

## How to Submit Changes

1. **Fork the repository**
2. **Create a feature branch** from `main`:
   ```bash
   git checkout -b feature/your-change-name
   ```
3. **Make your changes** following the conventions below
4. **Test your changes** by running the pipeline end-to-end
5. **Open a pull request** using the PR template

## Conventions

### File format
- All skill files are Markdown (`.md`)
- Use ATX-style headings (`#`, `##`, `###`)
- Use fenced code blocks with language identifiers
- Tables use pipe syntax with alignment
- Keep lines under 100 characters where practical

### Agent file structure
Every agent file must follow this structure:
```markdown
# Agent Name

[One paragraph description of the agent's role]

## Your Inputs
[List of files this agent reads]

## Your Output
[File path this agent writes to]

## What You Do
[Numbered steps with sub-sections]

## Output Format
[Template showing the expected output structure]

## Rules
[Bullet list of constraints and behaviours]
```

### Commit messages
- Use imperative mood: "Add analytics fallback" not "Added analytics fallback"
- First line under 72 characters
- Explain why, not just what, in the body if the change is non-obvious

### Naming
- Agent files: `agents/[role]-agent.md` (lowercase, hyphenated)
- Reference files: `references/[topic].md` (lowercase, hyphenated)
- Command files: `commands/[command-name].md` (lowercase, hyphenated)

## What We Will Not Merge

- Changes that break the existing pipeline flow without migration path
- Agents that require paid API keys with no free-tier fallback documented
- Files that contain API credentials, tokens, or secrets
- Changes to the approval flow that bypass human review (Gate 5 is sacred)
- Purely cosmetic changes with no functional benefit

## Questions?

Open an issue with the question label, or start a discussion in the repository.

## License

By contributing, you agree that your contributions will be licensed under
the same MIT License that covers this project.
