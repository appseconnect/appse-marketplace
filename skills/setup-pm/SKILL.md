---
name: setup-pm
description: >
  Bootstrap Product Manager environment for appse ai. Asks which AI tool
  (Cursor, Claude Code, or Claude with VS Code), installs appse-core and
  appse-product per tool, then runs pm-init workspace setup. Use on a fresh
  machine before any pm- skill. Trigger on "setup product", "setup-pm",
  "install pm plugins", "bootstrap product manager", or when pm-init is not
  yet available.
---

# setup-pm — Product Manager Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace). Run **before** `/pm-init` on a fresh install.

## Required plugins

`appse-core` + `appse-product` (resolved from marketplace manifest on Cursor; `@appse` on Claude).

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. **Phase 0** — Ask AI tool: **Cursor** / **Claude Code** / **Claude with VS Code**.
3. **Install plugins** for **`appse-product`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin marketplace add` + install commands in `ai-tool-plugin-install.md`
4. If Cursor fresh clone required reload → stop; re-run **`/setup-pm`** or **`/pm-init`** after **Developer: Reload Window**.
5. **Phase 5** → follow `pm-init` from **Step 1** onward (skip Step 0 plugin install). Pass AI tool choice for workspace-open steps.

## Definition of Done

- [ ] AI tool confirmed via explicit question.
- [ ] `appse-core` + `appse-product` installed per chosen AI tool.
- [ ] `pm-init` workspace bootstrap completed (or reload pending with clear next step).
