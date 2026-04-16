---
name: simplicity-judge
version: 2026-04-16
triggers: ["keep or discard", "evaluate harness change", "is this worth keeping"]
tools: []
preconditions: []
constraints: []
category: engineering
---

# Simplicity judge — autoagent

Primary metric: `passed`. Simpler wins ties.

## Keep rules
- `passed` increased → **keep**.
- `passed` unchanged AND harness is simpler (fewer components, cleaner prompts,
  fewer special cases) → **keep**. Equal performance with less code is a real win.

## Discard rules
- `passed` dropped → **discard**.
- `passed` unchanged AND the change adds complexity → **discard**.
- The change fails the overfitting check → **discard**.

## Simplification wins
- Deleting a tool that was never used by the top-k tasks.
- Collapsing two near-duplicate prompts into one.
- Replacing a multi-step handoff with a single agent when the handoff wasn't helping.

## Learning from discards
A discarded run still teaches:
- Which tasks *became* newly solved by the change (maybe worth a smaller
  targeted change).
- Which tasks regressed (maybe the change broke a capability).
- Failure classes that appeared for the first time.

Log these observations to `memory/episodic/AGENT_LEARNINGS.jsonl` before the
next iteration.
