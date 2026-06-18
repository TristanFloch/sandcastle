---
"@ai-hero/sandcastle": patch
---

Fix `createWorktree({ branchStrategy: { type: "merge-to-head" } })` not merging the agent's commits back to the host's current branch. `wt.run()`, `wt.interactive()`, and `wt.createSandbox()` previously forwarded the worktree's temp branch as an explicit branch, which routed `SandboxLifecycle` through its "explicit branch" path and skipped the merge step entirely — commits landed on the temp branch but never on HEAD. They now pass `branch: undefined` (so the lifecycle records the host's current branch and merges back to it) while keeping the worktree's source branch alive for subsequent calls.
