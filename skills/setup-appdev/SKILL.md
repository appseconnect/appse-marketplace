---
name: setup-appdev
description: >
  Bootstrap App Developer environment for appse ai. Asks which AI tool (Cursor,
  Claude Code, or Claude with VS Code), installs the standalone appse-appdev plugin
  per tool, then runs app-init (repos + Docker + local appse ai). Trigger on "setup
  appdev", "setup-appdev", "install appdev plugin", or "bootstrap app developer".
---

# setup-appdev — App Developer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugin

`appse-appdev` — **standalone**. Does not require `appse-core`.

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install `appse-appdev`** for the chosen tool:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-appdev`** or **`/app-init`** after reload.
5. **Phase 5** → follow `app-init` from **Step 1** onward. Pass AI tool choice.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-appdev` installed per chosen AI tool (standalone — no `appse-core`).
- [ ] `app-init` completed (repos cloned, Docker up, local appse ai running) — or reload pending.
