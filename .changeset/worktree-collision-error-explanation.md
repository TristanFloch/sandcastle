---
"@ai-hero/sandcastle": patch
---

Improve the `WorktreeManager` error raised when the requested branch is already checked out in the host's main working tree (or any other unmanaged worktree). The message now explains why this happens — sandcastle's branch and merge-to-head strategies run the agent in a git worktree under `.sandcastle/worktrees/`, and git refuses to check out the same branch in two worktrees at once — and tells the caller to pick a different branch or switch the main working tree first. No behaviour change: sandcastle still does not attempt smart recovery here.
