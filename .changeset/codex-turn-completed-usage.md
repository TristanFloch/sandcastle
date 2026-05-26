---
"@ai-hero/sandcastle": patch
---

The `codex()` agent provider now surfaces per-iteration token usage from its `turn.completed` stream events, so the `Context window: NNNk` line is reported for Codex runs (previously only Claude Code). Codex's `{ input_tokens, cached_input_tokens, output_tokens }` shape is mapped onto Sandcastle's usage model with the cached portion counted as cache-read tokens, avoiding double-counting in the display. Usage flows directly from the stream, so it works even when session capture is disabled or there is no bind-mount.
