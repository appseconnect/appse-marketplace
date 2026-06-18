---
name: setup-qa
description: >
  Bootstrap Quality Engineer environment for appse ai on Cursor. Clones
  appse-marketplace, installs appse-core and appse-quality from the catalog
  manifest, then runs qa-init workspace setup. Trigger on "setup qa", "setup-qa",
  "install qa plugins", or "bootstrap quality engineer".
---

# setup-qa — Quality Engineer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-quality`

## Workflow

1. Read `skills/references/marketplace-plugin-install.md`.
2. Execute **Phase 1–4** for role plugin **`appse-quality`**.
3. If reload required → stop; re-run **`/setup-qa`** or **`/qa-init`** after reload.
4. Execute **Phase 5** → follow `qa-init` from **Phase 1** onward (skip Phase 2 plugin install).

## Definition of Done

- [ ] Marketplace + core + quality plugins cloned.
- [ ] `qa-init` environment setup completed (or reload pending).
