# Report Templates

Output format varies by research type and depth.

## Shallow (All Types) — Inline Answer

No files created. Respond directly in chat:

```
**{Answer to the question}**

Key points:
- {point 1} ([source])
- {point 2} ([source])
- {point 3} ([source])

Sources: {URLs}
```

## Medium — `.research/REPORT.md`

```markdown
# {Research Title}
*{date} | Type: {type} | Depth: medium | Sources: {count}*

## Summary
{3-5 sentences answering the research question directly}

## Key Findings
1. **{finding}** — {detail} [Source: {attribution}]
2. **{finding}** — {detail} [Source: {attribution}]
...

## Analysis
### {Theme 1}
{analysis with source attribution}

### {Theme 2}
{analysis with source attribution}

## Risks & Gaps
- {what we don't know or couldn't verify}

## Sources
1. {title} — {URL} (accessed {date})
...
```

## Deep — Full Package

### `.research/REPORT.md`

```markdown
# {Research Title}
*{date} | Type: {type} | Depth: deep | Sources: {count}*

## Executive Summary
{3-5 sentences answering the research question directly}

## Key Findings
{numbered, with confidence tags [HIGH/MEDIUM/LOW] and source attribution}

## Detailed Analysis

### {Section per theme from ANALYSIS.md}
{in-depth analysis with evidence, cross-references, and confidence scoring}

## Data & Metrics
| Metric | Value | Source | Confidence |
|--------|-------|--------|------------|
| ... | ... | ... | ... |

## Risks & Uncertainties
{what we don't know, contradictions between sources, gaps}

## Recommendations
{actionable next steps based on findings}

## Sources
{numbered bibliography with URLs and access dates}

## Methodology
{sources used, queries executed, analysis approach}
```

### `.research/DATA-PACK.md`

```markdown
# Data Package: {title}
*Generated alongside: REPORT.md*

## Source Summaries
{link to each .research/sources/*.md with one-line summary}

## Extracted Data Tables
{all structured data found across sources, consolidated}

## Key Quotes & Excerpts
{notable quotes from web pages, transcripts, notebooks — with attribution}

## NotebookLM Responses
{full Q&A from notebook queries, if used}

## Raw Search Queries
{all queries executed per source, with result counts}
```

## Investment Type Additions

For investment-type research, add these sections to the deep report:

### Financial Summary Table
| Metric | Value | Source | Period |
|--------|-------|--------|--------|
| Revenue | ... | ... | ... |
| EBITDA | ... | ... | ... |
| Growth | ... | ... | ... |
| Margin | ... | ... | ... |

### PE Fit Assessment
- **Growth**: {assessment}
- **Margins**: {assessment}
- **Moat / Defensibility**: {assessment}
- **Market Position**: {assessment}
- **Key Risks**: {assessment}

### Comparable Transactions
| Target | Acquirer | Year | EV/EBITDA | Notes |
|--------|----------|------|-----------|-------|
| ... | ... | ... | ... | ... |

## Ideation Type Additions

For ideation-type research, add:

### Opportunity Scorecard
| Factor | Score (1-5) | Notes |
|--------|:-----------:|-------|
| Market Size | ... | ... |
| Competition | ... | ... |
| Timing | ... | ... |
| Feasibility | ... | ... |
| KM Fit | ... | ... |

### Next Steps
{concrete actions to validate or pursue the idea}

## Formatting Notes

- Use European number formatting for Evli context (1.000.000 or 1 000 000, not 1,000,000)
- Currency in EUR unless otherwise specified
- Dates in YYYY-MM-DD format
- Confidence tags: `[HIGH]` = 2+ independent sources agree, `[MEDIUM]` = 1 credible source, `[LOW]` = unverified or single weak source
