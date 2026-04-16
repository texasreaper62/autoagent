# Skill Registry — autoagent

Read this file first. Full `SKILL.md` contents load only when a skill's
triggers match the current task. Machine-readable equivalent:
`skills/_manifest.jsonl`.

## Meta-agent loop (this repo)

### experiment-loop
The harness-iteration loop. Runs until interrupted.
Triggers: "run experiment", "kick off experiment", "autoagent loop", "benchmark run"

### harness-modify
Editing `agent.py` / `agent-claude.py` above the FIXED ADAPTER BOUNDARY.
Triggers: "modify harness", "edit agent.py", "change system prompt", "add tool"

### failure-triage
Group failed tasks by root cause; pick ONE general improvement.
Triggers: "diagnose failures", "why did tasks fail", "analyze trajectory"

### tool-design
Specialized tools beat run_shell; structured returns beat raw stdout.
Triggers: "add tool", "design tool", "new function_tool", "agent.as_tool"

### overfitting-check
The "if this task disappeared, would the change still matter?" test.
Triggers: "overfitting", "task-specific", "hardcoded", "benchmark hack"

### results-logging
Schema + rules for `results.tsv`.
Triggers: "log result", "record experiment", "update results.tsv"

### simplicity-judge
Keep/discard with `passed` primary, simpler as tie-break.
Triggers: "keep or discard", "evaluate harness change"

### subagent-dispatcher
Per-iteration fresh-context subagent. Prevents overnight context rot.
Triggers: "spawn subagent", "delegate iteration", "fresh context"

### sdk-portability
Both `agent.py` (OpenAI SDK) and `agent-claude.py` (Claude SDK) share this brain.
Triggers: "switch SDK", "OpenAI vs Claude SDK", "dual SDK", "portability"

## Universal (inherited from template)

### skillforge
Creates new skills from observed patterns.

### memory-manager
Reflection cycles. Episodic → semantic promotion.

### git-proxy
All git ops with safety constraints.

### debug-investigator
Reproduce → isolate → hypothesize → verify.

### deploy-checklist
Kept for portability; unused in autoagent.
