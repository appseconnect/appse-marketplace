---
name: app-setup
description: >
  One-step setup for the App Developer on appse ai. Installs appse-appdev (no appse-core) for your AI tool
  (Cursor, Claude Code, or Claude with VS Code), then sets up the workspace — clones
  appse-ai-community-apps and appse-ai-docs, creates appdev-workspace.code-workspace and checks your tools (Docker and a local appse ai stack).
  Run once per machine; run again any time to re-check a broken setup — it skips what is
  already done. Trigger on "setup appdev", "init appdev", "run appse ai locally", "start building an app", "app-setup", "install app plugins", or
  "get my environment ready".
---

# app-setup — App Developer Setup

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugin

`appse-appdev` — **standalone**. Does not require `appse-core`.

## Workflow

0. **Already installed?** If `appse-appdev` skills are available, skip to step 5 — this run re-checks the workspace.
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
   > comes back, run `/app-setup` again and I will pick up where we left off."

5. Set up the workspace — follow `references/workspace.md` from its first step. Pass the tool choice along.

## What "done" looks like

- [ ] You told me which app you are using — I never assume it.
- [ ] `appse-appdev` is installed. It works on its own and needs nothing else.
- [ ] `app-setup` has finished: the repos are downloaded, Docker is running, and a
      local copy of appse ai is up — or you have been told a restart is needed first.

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- This is often someone's first contact with appse ai. Assume no prior knowledge
- Say what each step is doing and roughly how long it takes
- When something fails, say what to try next — never leave them at an error message
