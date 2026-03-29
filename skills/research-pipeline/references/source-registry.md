# Source Registry

All available research sources, their tools, requirements, and characteristics.

## Sources

| Source ID | Tool / Skill | Auth Required | Latency | Batch | Notes |
|-----------|-------------|---------------|---------|-------|-------|
| `web-search` | WebSearch (built-in) | No | ~2s | No | Primary for current facts, news, market data |
| `web-fetch` | WebFetch (built-in) | No | ~3s | No | Deep-read specific URLs found via search |
| `youtube-mcp` | `mcp__youtube-transcript__get-transcript` | No | ~5s | No | Fast, single video. Try first. |
| `youtube-apify` | Apify actor `h7sDV53CddomktSi5` | APIFY_API_TOKEN | ~30s | Yes | Fallback. Batch capable. Search queries supported. |
| `tiktok` | TikTok scraper plugin | APIFY_API_TOKEN | ~30s | Yes | Trend research, consumer sentiment |
| `notebooklm` | `python scripts/run.py ask_question.py` at `~/.claude/skills/notebooklm/` | Google auth | ~15s | No | Source-grounded answers from user's uploaded docs |
| `excel` | `mcp__excel__excel_read_sheet` | No | ~2s | No | Local Excel files — financial models, data |
| `supabase` | `mcp__supabase__execute_sql` | Token in .mcp.json | ~3s | Yes | Stored deal data, historical research |
| `local-files` | Read tool | No | ~1s | No | Any file on disk (PDFs, docs, data) |
| `obsidian` | MCP obsidian tools or `/brain read` | No | ~2s | No | KM's knowledge vault — prior research, notes, contacts |

## YouTube Fallback Chain

1. Try `mcp__youtube-transcript__get-transcript` (fast, free, no auth)
2. If fails (private video, region lock, no transcript) → use Apify actor from youtube-research skill
3. If both fail → mark source as FAILED, proceed without

## NotebookLM Protocol

1. Check auth: `python scripts/run.py auth_manager.py status`
2. If expired: tell user to reauth, skip source
3. Search library: `python scripts/run.py notebook_manager.py search --query "{topic}"`
4. If relevant notebook found: `python scripts/run.py ask_question.py --question "{q}" --notebook-url "{url}"`
5. Follow-up: analyze answer, ask follow-ups until information is complete
6. Synthesize all answers into findings

## Source Health Check

To verify source availability, run `/research sources`:

```
For each source:
  - web-search: always available
  - youtube-mcp: always available
  - youtube-apify: check APIFY_API_TOKEN in env
  - tiktok: check APIFY_API_TOKEN in env
  - notebooklm: run auth_manager.py status
  - excel: check if target file exists
  - supabase: check mcp__supabase connection
  - obsidian: check MCP obsidian connection
  - local-files: always available
```
