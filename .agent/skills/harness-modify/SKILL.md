---
name: harness-modify
version: 2026-04-16
triggers: ["modify harness", "edit agent.py", "change system prompt", "add tool", "change MODEL"]
tools: [bash, git]
preconditions: ["on an experiment branch", "baseline already established in results.tsv"]
constraints: ["never edit below FIXED ADAPTER BOUNDARY", "never change MODEL from gpt-5 unless human says so"]
category: engineering
---

# Harness modification

`agent.py` (or `agent-claude.py`) is the harness under test. It has two zones
separated by the `FIXED ADAPTER BOUNDARY` comment.

## Editable zone (above the boundary)
- `SYSTEM_PROMPT`, `MODEL`, `MAX_TURNS`
- `create_tools(environment)` — add/remove/modify tools
- `create_agent(environment)` — construction, handoffs, sub-agents, `agent.as_tool()`
- `run_task(environment, instruction)` — orchestration

## Fixed zone (below the boundary)
Do not touch. Contains Harbor integration + trajectory serialization.
Changing it invalidates the benchmark harness.

## High-leverage moves (in rough order)
1. **Specialized tools** > a single `run_shell`. Named tools get better pattern
   matching; structured returns reduce errors.
2. **Verification sub-agent** via `agent.as_tool()` — a second agent that
   re-reads output before the main agent finishes.
3. **Prompt tuning** — diminishing returns after the first few iterations.
4. **Handoffs** — when the task has sharply different phases.

## Anti-patterns (overfitting rule)
If your change is task-specific, keyword-matched, or hardcodes a solution,
it fails the test: *"If this exact task disappeared, would this still be a
worthwhile harness improvement?"*

## Self-rewrite hook
Every 10 harness modifications, re-read `memory/semantic/LESSONS.md` and
prune any that no longer match the current harness shape.
