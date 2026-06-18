---
"@ai-hero/sandcastle": patch
---

Fix Docker bind-mount sandbox failing on Windows hosts with `too many colons` when launched via `interactive()` (non-head strategy), `worktree.interactive()`, or `worktree.run()`. These three entry points called `resolveGitMounts` but skipped the `patchGitMountsForWindows` step, so the parent `.git` mount kept its `C:\...` sandbox path and Docker rejected the resulting volume string. They now mirror the existing wiring in `SandboxFactory` and `createSandbox`.
