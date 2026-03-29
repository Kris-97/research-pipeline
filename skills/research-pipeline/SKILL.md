---
name: research-pipeline
description: "Use when the user wants to research a topic, investigate a question, or gather information from multiple sources. Triggers on: 'research X', 'look into X', 'investigate X', 'what do we know about X', 'market size for', 'competitive landscape', '/research'. NOT for quick factual questions answerable from memory, file editing, or code tasks."
---

# Research Pipeline — Multi-Source Research Workflow

Takes a research question and produces structured output by orchestrating all available sources (web, YouTube, NotebookLM, Supabase, Excel, local files, Obsidian) through 4 phases.

## Phase State Machine

```
INTAKE ──(Gate 0)──▶ GATHER ──▶ ANALYZE ──(Gate 1)──▶ REPORT
```

Gate 0 and Gate 1 only appear for **deep** research. Shallow and medium skip gates entirely.

## Research Type Detection

Classify the research question into one of four types (see `references/type-profiles.md` for full rules):

| Type | Default Depth | Key Signals |
|------|:------------:|-------------|
| **investment** | deep | Company names + financial terms, deal screening, valuation, CIM |
| **ideation** | medium | Business idea, opportunity, trend, brainstorm, market gap |
| **general** | medium | How does X work, compare, overview, landscape, explain |
| **personal** | shallow | Quick, lookup, what is, define, single-concept |

User can override depth: "do a deep research on X" forces deep regardless of type.

## Shallow Fast Path

For **personal/shallow** questions (simple lookups, definitions, quick facts):
1. Run 1-2 WebSearch queries inline
2. Optionally WebFetch the top result for more detail
3. Respond directly in chat with 3-5 bullet points and source links
4. **No files created. No agents dispatched. No gates. Done.**

This fast path avoids the overhead of the full pipeline for questions that don't need it.

---

## Phase 0: INTAKE

**Goal**: Understand the question, classify it, plan sources.

### Steps

1. **Classify the question** — determine type and depth using `references/type-profiles.md` rules.

2. **If shallow/personal**: Execute the Shallow Fast Path above. Stop here — do not proceed to further phases.

3. **For medium/deep questions**, dispatch `research-intake` agent:
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "You are a research intake analyst. [Read and include full content of agents/research-intake.md]\n\nResearch question: {user's question}\n\nAnalyze and return the structured research plan.",
     name: "intake"
   )
   ```
   The intake agent returns: TYPE, DEPTH, refined QUESTION, ENTITIES, SOURCE PLAN, CONSTRAINTS.

3. **Create `.research/` directory** and write `.research/PROJECT.md`:
   ```markdown
   # Research: {title}

   ## Question
   {refined research question}

   ## Type
   {investment | ideation | general | personal}

   ## Depth
   {shallow | medium | deep}

   ## Entities
   - {entity} ({type})

   ## Source Plan
   | Source | Status | Priority | Strategy |
   |--------|--------|----------|----------|
   | web-search | planned | required | {queries} |
   | ... | ... | ... | ... |

   ## Constraints
   - {constraints}
   ```

4. **Gate 0** (deep only):
   ```
   ╔═════���════════════════════════════════════════╗
   ║  RESEARCH GATE 0: Research Plan             ║
   ╠══════════════════════════════════════════════╣
   ║  Question: {refined question}               ║
   ║  Type: {type}    Depth: {depth}             ║
   ║  Sources: {count} planned                   ║
   ║                                             ║
   ║  Source plan:                               ║
   ║  • {source 1}: {strategy summary}           ║
   ║  • {source 2}: {strategy summary}           ║
   ╚══════════════════════════════════════════════╝
   ```
   Options: `[Start] [Adjust sources] [Change depth] [Abort]`

   For shallow/medium: skip gate, proceed immediately.

---

## Phase 1: GATHER

**Goal**: Collect data from all planned sources in parallel.

### Steps

1. **Initialize `.research/STATE.md`**:
   ```markdown
   # Research State

   ## Project
   question: {question}
   type: {type}
   depth: {depth}
   phase: gather
   started: {ISO timestamp}

   ## Sources
   | Source | Status | Findings |
   |--------|--------|----------|
   | web | pending | — |
   | video | pending | — |
   ...
   ```

2. **Create `.research/sources/` directory**.

3. **Dispatch source agents in parallel**. For each source in the plan:

   **Web** (if planned):
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/source-web.md]\n\nResearch question: {question}\nEntities: {entities}\nSearch queries: {from intake plan}\n\nWrite findings to .research/sources/web.md",
     run_in_background: true,
     name: "source-web"
   )
   ```

   **Video** (if planned):
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/source-video.md]\n\nResearch question: {question}\nSearch queries: {from intake plan}\nSpecific URLs: {if any}\n\nWrite findings to .research/sources/video.md",
     run_in_background: true,
     name: "source-video"
   )
   ```

   **NotebookLM** (if planned):
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/source-notebook.md]\n\nResearch question: {question}\nNotebooks to query: {from intake plan}\nQuestions: {from intake plan}\n\nWrite findings to .research/sources/notebooklm.md",
     run_in_background: true,
     name: "source-notebook"
   )
   ```

   **Files/Data** (if planned):
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/source-files.md]\n\nResearch question: {question}\nExcel files: {paths}\nSupabase queries: {strategy}\nLocal files: {paths}\nObsidian search: {query}\n\nWrite findings to .research/sources/files.md",
     run_in_background: true,
     name: "source-files"
   )
   ```

4. **Wait for all agents to complete**.

5. **Check results**: Read each source file. Update STATE.md with status per source.

6. **Handle failures**:
   - Required source failed → retry once with adjusted queries
   - Optional source failed → proceed without
   - ALL required sources failed → present emergency gate:
     ```
     ⚠️ No data collected from required sources.
     Options: [Retry with different queries] [Proceed with what we have] [Abort]
     ```

7. **Note**: Shallow research never reaches this phase — it is handled entirely by the Shallow Fast Path in Phase 0.

---

## Phase 2: ANALYZE

**Goal**: Synthesize findings across all sources.

### Steps

1. **Dispatch `research-analyst` agent**:
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/research-analyst.md]\n\nResearch question: {question}\nType: {type}\n\nRead all files in .research/sources/ and .research/PROJECT.md.\nWrite synthesis to .research/ANALYSIS.md\n{if investment type: Also write .research/PE-LENS.md}",
     name: "analyst"
   )
   ```

2. **Review analysis**: Read ANALYSIS.md. Check:
   - Does it answer the research question?
   - Are there critical gaps that warrant a second gather round?

3. **Optional second gather**: If the analyst identified critical gaps AND the depth is deep:
   - Dispatch targeted source agents for the specific gaps
   - Re-run analyst with updated source files
   - Max 1 second gather round

4. **Update STATE.md**: phase → analyze, record analysis status.

---

## Phase 3: REPORT

**Goal**: Generate the final structured output.

### Steps

1. **Dispatch `research-reporter` agent**:
   ```
   Agent(
     subagent_type: "general-purpose",
     prompt: "[Read and include full content of agents/research-reporter.md]\n\nRead .research/PROJECT.md, .research/ANALYSIS.md{, .research/PE-LENS.md if exists}.\nRead ~/.claude/plugins/research-pipeline/skills/research-pipeline/references/report-templates.md for the template.\nRead ~/.claude/plugins/research-pipeline/skills/research-pipeline/references/quality-checklist.md and verify before finishing.\n\nDepth: {depth}\nType: {type}\n\n{if deep: Write .research/REPORT.md AND .research/DATA-PACK.md}\n{if medium: Write .research/REPORT.md only}\n{if shallow: Return inline answer — should not reach here}",
     name: "reporter"
   )
   ```

2. **For medium research**: Present the report inline with a summary:
   ```
   Research complete. Report saved to `.research/REPORT.md`.

   **Summary**: {executive summary from report}

   Key findings:
   - {finding 1}
   - {finding 2}
   - {finding 3}
   ```

3. **Gate 1** (deep only):
   ```
   ╔══════════════════════════════════════════════╗
   ║  RESEARCH GATE 1: Report Review             ║
   ╠══════════════════════════════════════════════╣
   ║  Report: .research/REPORT.md                ║
   ║  Data Pack: .research/DATA-PACK.md          ║
   ║  Findings: {count}  Gaps: {count}           ║
   ║  Confidence: {HIGH/MEDIUM/LOW counts}       ║
   ║                                             ║
   ║  Sources: {list with status}                ║
   ╚══════════════════════════════════════════════╝
   ```
   Options:
   - `[Accept]` — research complete, mark done
   - `[Deepen]` — investigate identified gaps, loop back to GATHER
   - `[Brand]` — format through Evli design system (investment type)
   - `[Pivot]` — change research direction, loop back to INTAKE
   - `[Save & pause]` — STATE.md saved, resume later with `/research resume`

4. **If `[Brand]` selected**: Hand off report content to Evli design system:
   ```
   Skill(skill: "evli-design-system-v2")
   ```
   Pass the report content with type tags for branded rendering.

5. **Update STATE.md**: phase → complete.

---

## Delegation Table

Before doing something yourself, check if an existing skill handles it:

| Situation | Delegate To |
|-----------|------------|
| NotebookLM queries | NotebookLM skill at `~/.claude/skills/notebooklm/` via agent |
| YouTube transcripts (fast) | `mcp__youtube-transcript__get-transcript` tool |
| YouTube transcripts (batch/search) | Apify flow from youtube-research skill |
| TikTok content | TikTok scraper plugin via Apify |
| Excel data | `mcp__excel__*` MCP tools |
| Supabase data | `mcp__supabase__*` MCP tools |
| Obsidian vault | MCP obsidian tools or `/brain read` |
| Evli-branded output | `evli-design-system-v2` skill |
| PE frameworks / deal analysis | `private-equity:*` skills |
| Brainstorming (unclear question) | `superpowers:brainstorming` skill |
| Context7 library docs | `mcp__plugin_context7_context7__resolve-library-id` + `query-docs` |

**Research Pipeline's unique contributions** (not delegated):
- Research type detection and depth calibration
- Multi-source parallel orchestration
- Cross-source synthesis and deduplication
- Confidence scoring and gap analysis
- The `.research/` artifact system
- Gate protocol for deep research
- Resume/abort lifecycle

---

## Resume Protocol

When user invokes `/research resume`:

1. Read `.research/STATE.md`
2. Determine current phase and last completed action
3. Present status summary:
   ```
   Resuming research: "{question}"
   Type: {type} | Depth: {depth}
   Phase: {current phase}
   Sources: {completed}/{total}
   ```
4. Resume from the next incomplete step:
   - If mid-gather: re-dispatch incomplete source agents
   - If between phases: start next phase
   - If at a gate: re-present the gate
   - Source files that completed successfully are NOT re-fetched

---

## Abort Protocol

When user invokes `/research abort`:

1. Confirm: "This will archive the current research. Are you sure?"
2. If confirmed:
   - Rename `.research/` to `.research-archived-{YYYY-MM-DD-HHmmss}/`
3. Report: "Research archived. Start new research with `/research {question}`."

---

## Sources Command

When user invokes `/research sources`:

1. Read `references/source-registry.md`
2. Check availability of each source:
   - Web: always available
   - YouTube MCP: always available
   - Apify (YouTube/TikTok): check APIFY_API_TOKEN in environment
   - NotebookLM: run auth check
   - Excel: check if file exists (if path specified)
   - Supabase: check MCP connection
   - Obsidian: check MCP connection
3. Present status table:
   ```
   Research Sources Status:
   ✅ web-search      — ready
   ✅ youtube-mcp     — ready
   ✅ youtube-apify   — ready (APIFY_API_TOKEN set)
   ⚠️ notebooklm     — auth expired (run reauth)
   ✅ supabase        — connected
   ✅ obsidian        — connected
   ✅ excel           — available
   ✅ local-files     — available
   ```

---

## Integration with Spire

When Spire needs research during a build:

1. Spire dispatches a builder agent with instructions to invoke the research pipeline
2. The research pipeline runs its full workflow, writing to `.research/` alongside `.spire/`
3. Spire's subsequent agents read `.research/REPORT.md` as input
4. Both artifact directories coexist without conflict

To invoke from Spire or any other context:
```
Read and follow the research-pipeline skill at
~/.claude/plugins/research-pipeline/skills/research-pipeline/SKILL.md
```
