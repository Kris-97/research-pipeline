# Research Type Profiles

## Type Detection

Classify the user's research question into one of four types based on signal words and context.

| Type | Signal Words / Patterns | Default Depth |
|------|------------------------|---------------|
| **investment** | Company names, "deal", "screen", "market size", "EBITDA", "valuation", "CIM", "acquisition", "target", "portfolio", fund names, "due diligence", "comparable", "multiple" | deep |
| **ideation** | "business idea", "opportunity", "trend", "what if", "brainstorm", "market gap", "startup", "concept", "explore", "potential" | medium |
| **general** | Topic queries, "how does X work", "compare", "explain", "research X", "overview of", "state of", "landscape" | medium |
| **personal** | "quick", "lookup", "remind me", "what is", "define", single-concept questions, simple factual | shallow |

### Disambiguation Rules

- If the question mentions a company AND financial terms → **investment** (not general)
- If the question says "brainstorm" but includes a specific company → **ideation** (not investment)
- If the question is a single sentence with no entities → **personal** (not general)
- User can always override: "do a deep research on X" forces deep regardless of type

## Source Selection Matrix

Which sources to use per research type. **Required** = must attempt. **Optional** = use if relevant. **Skip** = don't bother.

| Source | Investment | Ideation | General | Personal |
|--------|:---------:|:--------:|:-------:|:--------:|
| web-search | **required** | **required** | **required** | **required** |
| web-fetch | required | optional | optional | skip |
| youtube-mcp | optional | **required** | optional | skip |
| youtube-apify | fallback | fallback | fallback | skip |
| tiktok | skip | optional | skip | skip |
| notebooklm | **required** (if relevant notebook) | optional | optional | skip |
| excel | if referenced | skip | if referenced | skip |
| supabase | **required** (deal history) | skip | skip | skip |
| local-files | if provided | if provided | if provided | if provided |
| obsidian | optional | optional | optional | skip |

## Depth Levels

| Depth | Sources | Files Created | Gates | Time Estimate |
|-------|---------|---------------|-------|---------------|
| **shallow** | 1-2 | None (inline answer) | 0 | ~10s |
| **medium** | 3-4 | `.research/REPORT.md` | 0 | ~2min |
| **deep** | 4+ | Full `.research/` package | 2 (Gate 0 + Gate 1) | ~5min |

## Type-Specific Analysis Focus

| Type | Analysis Priorities |
|------|-------------------|
| **investment** | Financial metrics, competitive position, risks, market dynamics, PE fit (growth, margins, moat), valuation context |
| **ideation** | Trends, opportunities, competitive landscape, feasibility, timing, market size estimate |
| **general** | Comprehensiveness, multiple perspectives, accuracy, balanced view, further reading |
| **personal** | Clarity, directness, key takeaway, source link |
