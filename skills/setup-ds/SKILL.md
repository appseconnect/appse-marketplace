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
2. Ask which tool they are using. Never guess:

   > "Which app are you working in? The install steps are different for each one.
   >
   > - **Cursor**
   > - **Claude Code** (the command line)
   > - **Claude with VS Code**"

3. **Install plugins** for **`appse-design`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. On Cursor, a first-time install needs a restart before the new skills appear. Stop and say so:

   > "The design tools are downloaded, but Cursor has to restart before it can see
   > them. Open the command palette and run **Developer: Reload Window**. When it
   > comes back, run `/setup-ds` again and I will pick up where we left off."

5. Then follow `ds-init` from **Step 1** onward. Skip its Step 0 — the plugins are
   already installed. Pass the tool choice along.

## What "done" looks like

- [ ] You told me which app you are using — I never assume it.
- [ ] The two design tool sets, `appse-core` and `appse-design`, are installed.
- [ ] Your workspace is set up by `ds-init` — or you have been told clearly that a
      restart is needed and what to run next.

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- This is often someone's first contact with appse ai. Assume no prior knowledge
- Say what each step is doing and roughly how long it takes
- When something fails, say what to try next — never leave them at an error message
