---
name: arch-setup
description: >
  One-step setup for the Architect (and senior developers who review architecture) on
  appse ai. Installs appse-core and appse-architecture for your AI tool (Cursor, Claude
  Code, or Claude with VS Code), then sets up the workspace — clones arise and arise-specs,
  creates arch-workspace.code-workspace, checks you can push to arise-specs and whether you
  are an architecture owner. Run once per machine; run again to re-check. Trigger on
  "setup architecture", "init architecture", "arch-setup", "install arch plugins", or
  "get my architect environment ready".
---

# arch-setup — Architect Setup

Entry skill from [appse-marketplace](https://github.com/appseconnect/appse-marketplace).

## Required plugins

`appse-core` + `appse-architecture`

## Workflow

0. **Already installed?** If `appse-architecture` skills are available, skip to step 5 — this run re-checks the workspace.
1. Read `skills/references/ai-tool-plugin-install.md`.
2. Ask which tool they are using. Never guess:

   > "Which app are you working in? The install steps are different for each one.
   >
   > - **Cursor**
   > - **Claude Code** (the command line)
   > - **Claude with VS Code**"

3. **Install plugins** for **`appse-architecture`**:
   - **Cursor** → `marketplace-plugin-install.md` Phases 1–4
   - **Claude Code** or **Claude with VS Code** → `/plugin` commands in `ai-tool-plugin-install.md`
4. On Cursor, a first-time install needs a restart before the new skills appear. Stop and say so:

   > "The architecture tools are downloaded, but Cursor has to restart before it can
   > see them. Open the command palette and run **Developer: Reload Window**. When it
   > comes back, run `/arch-setup` again and I will pick up where we left off."

5. Set up the workspace — follow `references/workspace.md` from its first step. Pass the tool choice along.

## What "done" looks like

- [ ] You told me which app you are using — I never assume it.
- [ ] The two engineering tool sets, `appse-core` and `appse-architecture`, are installed.
- [ ] Your workspace is set up (`references/workspace.md`) — or you have been told clearly that a
      restart is needed and what to run next.

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- This is often someone's first contact with appse ai. Assume no prior knowledge
- Say what each step is doing and roughly how long it takes
- When something fails, say what to try next — never leave them at an error message
