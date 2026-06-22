---
name: setup-ops
description: >
  Bootstrap DevOps Engineer environment for appse ai. Asks which AI tool
  (Cursor, Claude Code, or Claude with VS Code), installs appse-core and
  appse-devops per tool, then runs ops-init workspace setup. Trigger on
  "setup devops", "setup-ops", "install ops plugins", or "bootstrap devops".
---

# setup-ops — DevOps Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-devops`

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install plugins** for **`appse-devops`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-ops`** or **`/ops-init`** after reload.
5. **Phase 5** → follow `ops-init` from **Step 1** onward (skip Step 0). Pass AI tool choice.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-core` + `appse-devops` installed per chosen AI tool.
- [ ] `ops-init` workspace bootstrap completed (or reload pending).
