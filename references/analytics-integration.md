# Analytics Integration Reference

How the Analytics Agent connects to data sources and what to do when
connections fail.

## Architecture

The Analytics Agent does not call APIs directly. It reads data through
intermediary services (FastAPI proxies or MCP servers) that handle authentication
and rate limiting.

```
Analytics Agent
    │
    ├── Google Search Console ← FastAPI proxy or MCP server
    ├── GA4                   ← FastAPI proxy or MCP server
    ├── DataForSEO            ← Direct API (key in .env)
    └── Ahrefs                ← CSV import (manual)
```

## Setting Up Each Source

### Google Search Console

**Option A: FastAPI proxy (recommended)**
If you have a FastAPI proxy running that wraps the GSC API:
- Endpoint: configured in your project's `.env` or MCP config
- The Analytics Agent queries it for ranking data, impressions, CTR

**Option B: MCP server**
If you use an MCP server that exposes GSC data:
- Configure the MCP connection in `.mcp.json`
- The Analytics Agent uses the MCP tools to query GSC

**What it provides:**
- Current ranking positions for target keywords
- Pages already ranking for related queries (cannibalisation detection)
- Click-through rates and impression data
- Coverage errors for related URLs

### GA4 / Google Analytics

**Option A: FastAPI proxy**
- Endpoint: configured in `.env`
- Queries traffic, engagement, and conversion data

**Option B: MCP server**
- Configure in `.mcp.json`

**What it provides:**
- Traffic to existing content on the topic
- Engagement metrics (time on page, scroll depth, bounce rate)
- Conversion events from content pages
- Top landing pages in the topic cluster

### DataForSEO

**Setup:**
- API credentials in `.env`: `DATAFORSEO_LOGIN` and `DATAFORSEO_PASSWORD`
- Direct API calls (no proxy needed)

**What it provides:**
- Search volume for primary keyword and variants
- Keyword difficulty scores
- SERP features present (featured snippet, AI Overview, PAA)
- Top 10 results overview (domain authority, content type, age)
- Related keywords with lower difficulty

### Ahrefs

**Setup:**
- No API integration (free tier limitation)
- User provides CSV exports manually

**What it provides (when CSV available):**
- Backlink data for competing pages
- Domain Rating comparisons
- Referring domains data

## Fallback Behaviour

The Analytics Agent is designed to degrade gracefully. Not every project
has all data sources connected.

| Scenario | Behaviour |
|---|---|
| All sources available | Full analytics.md with `ANALYTICS COMPLETE` |
| GSC unavailable | Skip rankings section, note in output, proceed |
| GA4 unavailable | Skip performance section, note in output, proceed |
| DataForSEO unavailable | Skip keyword data, note in output — this is the biggest gap |
| All sources unavailable | Minimal analytics.md with `ANALYTICS PARTIAL`, Writer leans on research |
| API returns error | Log the error, note it in output, proceed with remaining sources |
| API returns empty data | Note "no data found for [query]" — don't fabricate numbers |

### ANALYTICS PARTIAL vs ANALYTICS COMPLETE

- `ANALYTICS COMPLETE` — at least one data source returned useful data
- `ANALYTICS PARTIAL` — all sources failed or returned nothing useful

The quality gate (Gate 1) accepts both signals. The Writer Agent checks
which signal was used and adjusts its reliance on analytics recommendations.

## Data Freshness

- **GSC data:** typically 2–3 days behind (Google's processing delay)
- **GA4 data:** up to 24 hours behind for standard reports
- **DataForSEO:** real-time SERP data, monthly-updated volume estimates
- **Ahrefs CSV:** as fresh as when the user exported it

The Analytics Agent should note the data freshness in its output when relevant
(e.g., "GSC data covers the last 90 days, ending [date]").

## Validating Content Against Analytics Post-Publish

After content is published and has been live for 2+ weeks, you can re-run
the Analytics Agent on the same brief to compare:

1. **Pre-publish predictions** — what keywords were targeted, what position expected
2. **Post-publish reality** — actual rankings, traffic, engagement

This feedback loop helps calibrate the pipeline over time. Store comparison
data in `.claude/pipeline/approved/[slug]-analytics-review.md`.

## Security Notes

- API credentials live in `.env` (gitignored, never committed)
- FastAPI proxies should require authentication
- MCP servers handle their own auth via the MCP config
- Never log raw API responses that contain account-level data
- The Analytics Agent's output (analytics.md) contains aggregated data only,
  not raw API credentials or account identifiers
