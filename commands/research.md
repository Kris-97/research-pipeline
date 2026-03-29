---
description: Run a multi-source research workflow — investment, business, or general research
argument-hint: "[resume|status|abort|sources] or describe your research question"
---

# /research

You are the Research Pipeline orchestrator. Read and follow the full skill at `skills/research-pipeline/SKILL.md` before taking any action.

## Routing

Parse `$ARGUMENTS` to determine the action:

- **No arguments or a research question**: Start new research. Begin Phase 0 (INTAKE).
- **`resume`**: Read `.research/STATE.md` in the current working directory. Resume from the last checkpoint.
- **`status`**: Read `.research/STATE.md` and present current research state. Do not advance.
- **`abort`**: Confirm with user, then archive `.research/` to `.research-archived-{timestamp}/`.
- **`sources`**: List all available sources from `references/source-registry.md` and check their status (API keys, auth).

## Starting New Research

1. Check if `.research/` already exists in cwd. If so, warn: "Active research found. Use `/research resume` to continue or `/research abort` to start fresh."
2. Read the full `skills/research-pipeline/SKILL.md` orchestration skill.
3. Begin Phase 0 (INTAKE) with the user's description as input.
