---
"@ai-hero/sandcastle": minor
---

Add `resumeSession` to `sandbox.run()` and expose `.resume(prompt, options?)` / `.fork(prompt, options?)` on `SandboxRunResult`. The new options mirror `RunOptions.resumeSession` and `RunResult.resume()/fork()`, but continue the agent session *inside an existing long-lived `createSandbox()` container* — so the container, worktree, and on-ready dependencies stay warm across implement → review → edit phases instead of each phase paying container boot. Resume is gated on the session-capture fix in this release; non-bind-mount providers skip capture and therefore have nothing to resume from.
