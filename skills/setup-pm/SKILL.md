---
name: setup-pm
description: >
  Bootstrap Product Manager environment for appse ai on Cursor. Clones
  appse-marketplace, installs appse-core and appse-product from the catalog
  manifest, then runs pm-init workspace setup. Use on a fresh machine before any
  pm- skill. Trigger on "setup product", "setup-pm", "install pm plugins",
  "bootstrap product manager", or when pm-init is not yet available.
---

# setup-pm — Product Manager Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace). Run **before** `/pm-init` on a fresh Cursor install.

## Required plugins

`appse-core` + `appse-product` (resolved from marketplace manifest).

## Workflow

1. Read `skills/references/marketplace-plugin-install.md` in this marketplace clone (or `%USERPROFILE%\.cursor\plugins\local\appse-marketplace\skills\references\marketplace-plugin-install.md`).
2. Execute **Phase 1–4** for role plugin **`appse-product`**.
3. If Phase 4 required a reload → stop and tell user to re-run **`/setup-pm`** or **`/pm-init`** after reload.
4. Execute **Phase 5** → follow `pm-init` from **Step 1** onward (skip Step 0 plugin install).

## Definition of Done

- [ ] `appse-marketplace`, `appse-core`, `appse-product` cloned under `%USERPROFILE%\.cursor\plugins\local\`.
- [ ] `pm-init` workspace bootstrap completed (or reload pending with clear next step).
