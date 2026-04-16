# Claude Code pointer — autoagent

This repo's agent context lives in `.agent/`. It's the portable brain shared
by both `agent.py` (OpenAI SDK) and `agent-claude.py` (Claude SDK).

## On session start

1. Read `.agent/AGENTS.md`.
2. Read `.agent/memory/personal/PREFERENCES.md` + `.agent/memory/semantic/LESSONS.md`.
3. Read `.agent/skills/_index.md`. Load individual `SKILL.md` files on demand.
4. Check `.agent/protocols/permissions.md` before any write or shell call.

## Session management (context hygiene)

- Every harness-iteration goes in a **fresh-context subagent** (the benchmark
  produces huge trajectory JSON the parent doesn't need). See
  `.agent/skills/subagent-dispatcher/SKILL.md`.
- Failure triage on 50+ trajectories is a classic delegation candidate —
  return only `{class: count, top_examples: [...]}`.
- Prefer `/rewind` + summarize over "that didn't work, try X" in the same context.
- Proactively `/compact` around 60% of the window with a focus instruction.

## Directive

See `program.md` at the repo root.
