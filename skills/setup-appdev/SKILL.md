---
name: setup-appdev
description: >
  Bootstrap App Developer environment for appse ai. Asks which AI tool (Cursor,
  Claude Code, or Claude with VS Code), installs the standalone appse-appdev plugin
  per tool, then runs app-init (repos + Docker + local appse ai). Trigger on "setup
  appdev", "setup-appdev", "install appdev plugin", or "bootstrap app developer".
---

# setup-appdev — App Developer Bootstrap

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugin

`appse-appdev` — **standalone**. Does not require `appse-core`.

## Workflow

1. Read `skills/references/ai-tool-plugin-install.md`.
2. Ask which tool they are using. Never guess:

   > "Which app are you working in? The install steps are different for each one.
   >
   > - **Cursor**
   > - **Claude Code** (the command line)
   > - **Claude with VS Code**"

3. **Install `appse-appdev`** for the chosen tool:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. On Cursor, a first-time install needs a restart before the new skills appear. Stop and say so:

   > "The app developer tools are downloaded, but Cursor has to restart before it can
   > see them. Open the command palette and run **Developer: Reload Window**. When it
   > comes back, run `/setup-appdev` again and I will pick up where we left off."

5. Then follow `app-init` from **Step 1** onward. Pass the tool choice along.

## What "done" looks like

- [ ] You told me which app you are using — I never assume it.
- [ ] `appse-appdev` is installed. It works on its own and needs nothing else.
- [ ] `app-init` has finished: the repos are downloaded, Docker is running, and a
      local copy of appse ai is up — or you have been told a restart is needed first.

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- This is often someone's first contact with appse ai. Assume no prior knowledge
- Say what each step is doing and roughly how long it takes
- When something fails, say what to try next — never leave them at an error message
