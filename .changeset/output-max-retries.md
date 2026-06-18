---
"@ai-hero/sandcastle": minor
---

Add `maxRetries` to `Output.object` and `Output.string` for built-in retry of structured-output runs. When extraction or validation fails, `run()` resumes the failed agent session and feeds back a token-efficient description of the error so the agent can re-emit a corrected tag, up to `maxRetries` extra attempts (default: `0`). Retries require an agent provider that supports session resumption (`claudeCode`, `codex`, `pi`); calling `run()` with `maxRetries > 0` against a non-resumable provider (`cursor`, `opencode`, `copilot`) fails at entry with a clear error.
