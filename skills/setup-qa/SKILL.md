---
name: setup-qa
description: >
  Bootstrap Quality Engineer environment for appse ai. Asks which AI tool
  (Cursor, Claude Code, or Claude with VS Code), installs appse-core and
  appse-quality per tool, then runs qa-init workspace setup. Trigger on
  "setup qa", "setup-qa", "install qa plugins", or "bootstrap quality engineer".
---

# setup-qa — Quality Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-quality`

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install plugins** for **`appse-quality`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-qa`** or **`/qa-init`** after reload.
5. **Phase 5** → follow `qa-init` from **Phase 1** onward (skip Phase 2 plugin install). Pass AI tool choice.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-core` + `appse-quality` installed per chosen AI tool.
- [ ] `qa-init` environment setup completed (or reload pending).
