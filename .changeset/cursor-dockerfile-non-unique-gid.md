---
"@ai-hero/sandcastle": patch
---

Fix Cursor Dockerfile failing on macOS hosts where the user's GID is `20` (already used by the `dialout` group in `node:22-bookworm`). `groupmod`/`usermod` in the Cursor template now use `-o` (`--non-unique`), matching the other agent templates.
