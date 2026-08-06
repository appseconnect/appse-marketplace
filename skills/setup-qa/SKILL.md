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
2. Ask which tool they are using. Never guess:

   > "Which app are you working in? The install steps are different for each one.
   >
   > - **Cursor**
   > - **Claude Code** (the command line)
   > - **Claude with VS Code**"

3. **Install plugins** for **`appse-quality`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. On Cursor, a first-time install needs a restart before the new skills appear. Stop and say so:

   > "The testing tools are downloaded, but Cursor has to restart before it can see
   > them. Open the command palette and run **Developer: Reload Window**. When it
   > comes back, run `/setup-qa` again and I will pick up where we left off."

5. Then follow `qa-init` from **Phase 1** onward. Skip its plugin install — that is
   already done. Pass the tool choice along.

## What "done" looks like

- [ ] You told me which app you are using — I never assume it.
- [ ] The two testing tool sets, `appse-core` and `appse-quality`, are installed.
- [ ] Your environment is set up by `qa-init` — or you have been told clearly that a
      restart is needed and what to run next.

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- This is often someone's first contact with appse ai. Assume no prior knowledge
- Say what each step is doing and roughly how long it takes
- When something fails, say what to try next — never leave them at an error message
