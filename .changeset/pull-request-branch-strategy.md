---
"@ai-hero/sandcastle": minor
---

Add a `pull-request` branch strategy. It behaves like `branch` (commits land on a named branch in a worktree, never merged back to HEAD), but Sandcastle additionally provisions HTTPS push credentials into the sandbox — `gh auth setup-git`, normalizing an SSH `origin` to HTTPS, and disabling commit signing — so the agent can push the branch and open a pull request itself (linked to the task it worked on) as the final step of its task.

Auth is HTTPS-only via `GH_TOKEN`/`GITHUB_TOKEN` (no SSH forwarding, so unattended runs never hit a passphrase/biometric prompt). The strategy fails fast before the agent runs if `gh` is missing, no token is set, or `origin` is not a GitHub remote. `branch` is optional and auto-generated (`sandcastle/…`) when omitted, making concurrent `fork()` fan-out collision-safe.

Supported on bind-mount and no-sandbox providers only — isolated providers are excluded (their in-sandbox `origin` is a git bundle, not the GitHub remote). See ADR 0021.
