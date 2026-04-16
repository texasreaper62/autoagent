# autoagent

Autonomous agent engineering. Improve the harness (`agent.py` or
`agent-claude.py`) so the agent-under-test passes more benchmark tasks.

## Directive

Build a generally capable autonomous coding and terminal agent. Do NOT change
the model from `gpt-5` unless the human explicitly lifts that constraint.

## Brain

This repo uses the portable `.agent/` brain. Read in this order:

1. `.agent/AGENTS.md` — the map.
2. `.agent/memory/personal/PREFERENCES.md` — stable user conventions.
3. `.agent/memory/semantic/LESSONS.md` — distilled patterns from prior runs.
4. `.agent/skills/_index.md` — discover skills. Load full `SKILL.md` only when triggers match.
5. `.agent/protocols/permissions.md` — check before any tool call.

## Getting started

When the human says *"kick off a new experiment"*:

1. Read `README.md`, this file, and the active harness file (`agent.py` or `agent-claude.py`).
2. Confirm the `FIXED ADAPTER BOUNDARY` is understood — do not edit below it.
3. Enter `skills/experiment-loop/SKILL.md`.
4. Every iteration MUST run inside a fresh-context subagent — see `skills/subagent-dispatcher/SKILL.md`.
5. The first run is ALWAYS the unmodified baseline.
6. Never stop the loop until the human interrupts.

## Primary metric

`passed` (number of benchmark tasks passed). Ties broken by simpler harness.
See `skills/results-logging/` for `results.tsv` schema.

## Dual-SDK

This repo ships `agent.py` (OpenAI Agents SDK) AND `agent-claude.py` (Claude
Agent SDK). Both mount the same `.agent/` brain. When improving, keep
knowledge SDK-agnostic — see `skills/sdk-portability/`.

Everything else — failure triage, tool design, overfitting checks, the
loop structure, keep/discard rules — lives in the skills.
