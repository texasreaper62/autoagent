# Personal Preferences — autoagent

## Workflow
- Hill-climb on `passed` (number of benchmark tasks passed).
- Tie-break on simpler harness.
- Every harness change gets its own commit.
- Log every run to `results.tsv` (7 columns, tab-separated, untracked by git).
- Meta-agent loop never stops until the human interrupts.

## Constraints
- Do not change `MODEL` from `gpt-5` (in `agent.py`) unless the human explicitly lifts this.
- Do not edit anything below the `FIXED ADAPTER BOUNDARY` comment in `agent.py` or `agent-claude.py`.
- Do not commit task-specific hacks (see `skills/overfitting-check`).
- Docker isolation means the agent can't damage the host — OK to experiment broadly inside the container.

## Simplicity criterion
Equal `passed` with a simpler harness is a WIN. Delete aggressively.

## Communication
- Be direct. Skip pleasantries.
- Surface tradeoffs explicitly.
- Never stop mid-loop to ask "should I continue?".
