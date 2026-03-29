# Research Pipeline

Universal multi-source research workflow plugin for Claude Code.

## What It Does

Takes any research question and produces structured reports by orchestrating all available sources through 4 phases:

```
INTAKE → GATHER → ANALYZE → REPORT
```

## Research Types

| Type | Example | Output |
|------|---------|--------|
| **Investment** | "Nordic SaaS PE acquisition targets" | Full report + data pack + PE lens |
| **Ideation** | "AI-powered due diligence tool" | Report with opportunity scorecard |
| **General** | "State of European infrastructure investing" | Structured report |
| **Personal** | "What is EBITDA?" | Inline answer |

## Sources

Orchestrates all available data sources in parallel:
- Web search + deep page reading
- YouTube transcripts (MCP + Apify fallback)
- Google NotebookLM (source-grounded answers from uploaded docs)
- Supabase database (historical deal data)
- Excel files (financial models, data)
- Local files (PDFs, documents)
- Obsidian vault (prior research, notes)
- TikTok (consumer trends, ideation only)

## Usage

```
/research "your research question"
/research resume          # resume interrupted research
/research status          # check current state
/research abort           # archive and start fresh
/research sources         # check source availability
```

## Architecture

- **7 agents**: intake, source-web, source-video, source-notebook, source-files, analyst, reporter
- **3 depth levels**: shallow (inline), medium (report), deep (full package with gates)
- **2 gates**: Gate 0 (plan approval) + Gate 1 (report review) — deep only
- **`.research/` artifacts**: all state as human-readable markdown
- **Pure markdown plugin** — no code dependencies

## Installation

Copy to `~/.claude/plugins/research-pipeline/` and enable in settings:

```json
{
  "enabledPlugins": {
    "research-pipeline@local": true
  }
}
```

## Integration

- **Spire**: delegates research phases to this plugin
- **NotebookLM**: queries uploaded documents for source-grounded answers
- **Evli Design System**: optional branded output for investment reports
- **YouTube/TikTok**: Apify API for transcript fetching

## License

MIT
