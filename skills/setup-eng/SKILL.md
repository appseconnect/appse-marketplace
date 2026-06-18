---
name: setup-eng
description: >
  Bootstrap Product Engineer environment for appse ai on Cursor. Clones
  appse-marketplace, installs appse-core and appse-engineering from the catalog
  manifest, then runs eng-init workspace setup. Trigger on "setup engineering",
  "setup-eng", "install eng plugins", or "bootstrap developer".
---

# setup-eng — Product Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-engineering`

## Workflow

1. Read `skills/references/marketplace-plugin-install.md`.
2. Execute **Phase 1–4** for role plugin **`appse-engineering`**.
3. If reload required → stop; re-run **`/setup-eng`** or **`/eng-init`** after reload.
4. Execute **Phase 5** → follow `eng-init` from **Step 1** onward.

## Definition of Done

- [ ] Marketplace + core + engineering plugins cloned.
- [ ] `eng-init` workspace bootstrap completed (or reload pending).
