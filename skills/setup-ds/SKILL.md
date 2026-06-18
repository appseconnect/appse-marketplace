---
name: setup-ds
description: >
  Bootstrap Product Designer environment for appse ai on Cursor. Clones
  appse-marketplace, installs appse-core and appse-design from the catalog
  manifest, then runs ds-init workspace setup. Trigger on "setup design",
  "setup-ds", "install design plugins", or "bootstrap designer".
---

# setup-ds — Product Designer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-design`

## Workflow

1. Read `skills/references/marketplace-plugin-install.md`.
2. Execute **Phase 1–4** for role plugin **`appse-design`**.
3. If reload required → stop; re-run **`/setup-ds`** or **`/ds-init`** after reload.
4. Execute **Phase 5** → follow `ds-init` from **Step 1** onward.

## Definition of Done

- [ ] Marketplace + core + design plugins cloned.
- [ ] `ds-init` workspace bootstrap completed (or reload pending).
