---
"@ai-hero/sandcastle": patch
---

Add a "Custom" issue tracker option to `sandcastle init`. Selecting it scaffolds the project in a deliberately broken-until-configured state plus a `.sandcastle/SETUP_ISSUE_TRACKER.md` prompt you feed to your coding agent, which wires up your own issue tracker by editing the scaffolded files in place. Init skips the image build for this option (the Dockerfile is intentionally unfinished) and prints a per-agent setup command in the next steps.
