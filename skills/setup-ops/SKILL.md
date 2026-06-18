---
name: setup-ops
description: >
  Bootstrap DevOps Engineer environment for appse ai on Cursor. Clones
  appse-marketplace, installs appse-core and appse-devops from the catalog
  manifest, then runs ops-init workspace setup. Trigger on "setup devops",
  "setup-ops", "install ops plugins", or "bootstrap devops".
---

# setup-ops — DevOps Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-devops`

## Workflow

1. Read `skills/references/marketplace-plugin-install.md`.
2. Execute **Phase 1–4** for role plugin **`appse-devops`**.
3. If reload required → stop; re-run **`/setup-ops`** or **`/ops-init`** after reload.
4. Execute **Phase 5** → follow `ops-init` from **Step 1** onward.

## Definition of Done

- [ ] Marketplace + core + devops plugins cloned.
- [ ] `ops-init` workspace bootstrap completed (or reload pending).
