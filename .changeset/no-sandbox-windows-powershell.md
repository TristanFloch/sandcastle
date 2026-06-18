---
"@ai-hero/sandcastle": patch
---

Fix `noSandbox()` failing with `spawn sh ENOENT` in PowerShell / `cmd.exe` on Windows. The provider now routes `exec` commands through `cmd.exe /d /s /c` on Windows and spawns interactive agents with `shell: true` so npm `.cmd`/`.ps1` wrappers (e.g. `claude.cmd`) resolve via `PATHEXT`. POSIX hosts are unchanged.
