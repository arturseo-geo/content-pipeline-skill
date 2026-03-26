# Content Freshness — Statistical Currency for the SEO/GEO Agent

## Why the SEO/GEO Agent Needs This

When reviewing content in `final-draft.md`, the SEO/GEO agent must check whether statistical claims are current. Outdated statistics create consistency conflicts in AI retrieval that suppress citation rate — even if the content structure is perfect.

## What to Check

### Every statistic needs three things:
1. **A named source** — not "studies show" but "SE Ranking's 2025 research shows"
2. **A date** — the year the data was published or collected
3. **Currency** — is this the most recent data available from this domain?

### Red flags:
- Statistics older than 12 months in time-sensitive sections
- Generic attribution ("research shows", "studies indicate", "experts say")
- Data points from pre-2024 in GEO/AI search content (the field moves too fast)
- Multiple statistics from different years cited together without acknowledging the time gap

## The 4-Priority Freshness Framework

When auditing content, prioritise statistical updates in this order:

| Priority | Condition | Action |
|----------|-----------|--------|
| **P1** | High citation rate + statistics >12 months old | Update immediately — decay imminent |
| **P2** | Declining citation rate + time-sensitive claims | Decay already started — cross-reference stats against current consensus |
| **P3** | Time-sensitive query + no statistics at all | Add verifiable current data to raise retrieval probability |
| **P4** | Ranked but never cited + current statistics | Problem is structure not freshness — apply LLM readability test |

## What the SEO/GEO Agent Should Do

In the `seo-report.md` output, add a section:

```markdown
## Statistical Currency Check

| Statistic | Source | Date | Current? | Action |
|-----------|--------|------|----------|--------|
| "AI search captures 30% of queries" | SE Ranking | 2025 | Yes | None |
| "22-40% visibility improvement" | Princeton GEO study | 2024 | Yes (foundational) | None |
| "CTR drops 58% with AI Overviews" | Ahrefs | Dec 2025 | Yes | None |

**Freshness verdict:** [ALL CURRENT / X STATISTICS NEED UPDATE / CRITICAL: outdated data in key sections]
```

## What NOT to Do

- Do not bulk-refresh published dates without updating claims
- Do not remove statistics to avoid the freshness check
- Do not cite a newer source that makes the same claim without verifying it actually does
- Do not assume statistics from major publications are automatically current — verify the specific data point

## After Statistics Updates

After replacing any statistic in content:
1. Confirm the opening sentence of the affected section is still declarative
2. Confirm the source attribution is specific (author/org + year + link)
3. Flag for re-test: the 30-check protocol should be re-run 7-14 days after re-indexing
