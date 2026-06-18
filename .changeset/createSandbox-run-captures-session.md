---
"@ai-hero/sandcastle": patch
---

Fix `createSandbox().run()` and `createWorktree().run()` not capturing the agent session on bind-mount providers — `iterations[].usage` stayed `undefined`, and the resulting `"Context window: NNNk"` line never printed. The `reuseFactoryLayer` that both entry points install was dropping `bindMountHandle` from the `SandboxInfo` it passed to the orchestrator, so the session-capture gate (`provider.captureSessions && provider.sessionStorage && sessionId && bindMountHandle`) silently no-op'd. The handle is now plumbed through, gated on `sandbox.tag === "bind-mount"` so isolated and no-sandbox providers still bypass capture cleanly.
