---
name: overfitting-check
version: 2026-04-16
triggers: ["overfitting", "task-specific", "hardcoded", "benchmark hack"]
tools: []
preconditions: []
constraints: []
category: engineering
---

# Overfitting check

Before committing any harness change, apply this test:

> *"If this exact task disappeared from the benchmark, would this still be
> a worthwhile harness improvement?"*

If the answer is no, it's overfitting. Discard.

## Red flags
- Keyword-matching rules tied to task titles.
- `if "<task-id>" in instruction:` branches.
- Hardcoded file paths or schema assumptions.
- Prompt additions that only make sense for one task's input shape.

## Green flags
- Generalizes across at least 2 unrelated task categories.
- Reduces a failure class, not a single failure.
- Survives a hypothetical benchmark swap.

## Simplicity tie-breaker
If the change passes and does not improve `passed`, keep it only if the
harness is genuinely simpler after the change. Equal performance with
less code is a real improvement.
