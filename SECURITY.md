# Security Policy

## Scope

This skill is a set of Markdown instruction files for Claude Code. It does not
execute code directly, but it instructs Claude Code to interact with APIs,
file systems, and external services. Security matters.

## Supported Versions

| Version | Supported |
|---|---|
| Latest on `main` | Yes |
| Older commits | No |

## Reporting a Vulnerability

If you discover a security issue, **do not open a public issue**.

Instead, email **artur@thegeolab.net** with:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if you have one)

You will receive a response within 7 days. If the issue is confirmed, a fix
will be released and you will be credited (unless you prefer anonymity).

## Security Considerations for This Skill

### API credentials
- All API keys and tokens must be stored in `.env` files (gitignored)
- Agent instructions never reference specific credentials
- The Analytics Agent documents which environment variables it expects but
  never includes actual values

### Pipeline output files
- `.claude/pipeline/` is gitignored by default to prevent accidental commits
  of draft content or analytics data
- Analytics output (analytics.md) may contain search volume and ranking data
  that some organisations consider sensitive — review before sharing

### Agent context isolation
- Each agent runs in its own context window and only receives the files
  specified in `references/agent-handoffs.md`
- No agent receives `.env` files or credential stores
- The Master Agent sees all pipeline files but not system configuration

### External data sources
- The Analytics Agent connects to Google Search Console, GA4, and DataForSEO
- All connections should go through authenticated proxies (FastAPI or MCP)
- Raw API responses are not stored — only aggregated summaries in analytics.md

### Publishing
- The pipeline always pauses for human approval before publishing
- No agent can publish content autonomously
- The WordPress integration (if connected) requires separate authentication

## Best Practices for Users

1. **Keep `.env` out of git** — verify your `.gitignore` includes `.env`
2. **Review analytics.md** before sharing pipeline outputs externally
3. **Audit agent outputs** — the Master Agent reviews quality, but a human
   reviews for sensitive information before publishing
4. **Rotate API keys** regularly, especially if you share your environment
5. **Use read-only API access** where possible (GSC and GA4 proxies should
   not have write permissions)
