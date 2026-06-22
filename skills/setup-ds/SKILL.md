---
name: setup-ds
description: >
  Bootstrap Product Designer environment for appse ai. Asks which AI tool
  (Cursor, Claude Code, or Claude with VS Code), installs appse-core and
  appse-design per tool, then runs ds-init workspace setup. Trigger on
  "setup design", "setup-ds", "install design plugins", or "bootstrap designer".
---

# setup-ds — Product Designer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-design`

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install plugins** for **`appse-design`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-ds`** or **`/ds-init`** after reload.
5. **Phase 5** → follow `ds-init` from **Step 1** onward (skip Step 0). Pass AI tool choice.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-core` + `appse-design` installed per chosen AI tool.
- [ ] `ds-init` workspace bootstrap completed (or reload pending).
