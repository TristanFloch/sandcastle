---
"@ai-hero/sandcastle": patch
---

Implement stream parsing for the OpenCode agent provider. `parseStreamLine` now extracts assistant text and the final result from `text` events, tool calls from `tool_use` events (`bash`, `webfetch`, `task`), and the session ID from `step_start` events — previously it returned nothing, so OpenCode runs surfaced no live output, tool calls, or session ID.
