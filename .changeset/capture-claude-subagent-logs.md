---
"@ai-hero/sandcastle": minor
---

Capture Claude Code subagent / workflow session transcripts to the host alongside the main session. Previously only the main `<sessionId>.jsonl` was copied off the sandbox; transcripts written by the `Agent` tool and the `Workflow` tool under `<sessionId>/subagents/agent-*.jsonl` were lost on teardown. They are now captured with the same sandbox→host `cwd` rewrite. Failure to capture an individual subagent transcript is best-effort and logs a warning; the main session capture remains fatal on failure.
