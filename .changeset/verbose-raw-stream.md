---
"@ai-hero/sandcastle": minor
---

Add `verbose` option to the `logging` configuration on `run()`, `createSandbox().run()`, and `createWorktree().run()`.

When set to `true`:

- In file mode (`{ type: "file", path, verbose: true }`), every raw stdout line the agent emits is appended verbatim to the same log file at `path` in real time, interleaved with the human-readable log output.
- In stdout/terminal mode (`{ type: "stdout", verbose: true }`), raw lines are written to `process.stdout`.

Includes lines the provider's stream parser would otherwise drop (e.g. tool-use blocks for unrecognised tools) — exactly what's needed to debug a stuck or unexpectedly silent agent.

A new `{ type: "raw"; line; iteration; timestamp }` variant is also surfaced through `onAgentStreamEvent`, so callers forwarding to external observability systems get every raw line too.
