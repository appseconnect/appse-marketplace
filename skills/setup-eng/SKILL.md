---
name: setup-eng
description: >
  Bootstrap Product Engineer environment for appse ai. Asks which AI tool
  (Cursor, Claude Code, or Claude with VS Code), installs appse-core and
  appse-engineering per tool, then runs eng-init workspace setup. Trigger on
  "setup engineering", "setup-eng", "install eng plugins", or "bootstrap developer".
---

# setup-eng — Product Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-engineering`

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install plugins** for **`appse-engineering`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-eng`** or **`/eng-init`** after reload.
5. **Phase 5** → follow `eng-init` from **Step 1** onward (skip Step 0). Pass AI tool choice.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-core` + `appse-engineering` installed per chosen AI tool.
- [ ] `eng-init` workspace bootstrap completed (or reload pending).
