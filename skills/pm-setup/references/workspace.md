# pm-setup — Initialize the Product Environment

First skill to run in the Product plugin. Verifies prerequisites, clones the
handbook repo, creates the **PM role workspace file**, scaffolds folders, and
validates PM tooling.

Read `conventions/workspace.md` (appse-core) for repo URLs and workspace rules.

---

## Constants

| Constant | Value |
|----------|-------|
| Workspace root | `%USERPROFILE%\repos\arise-workspace` |
| Workspace file | `%USERPROFILE%\repos\arise-workspace\pm-workspace.code-workspace` |
| Specs clone target | `%USERPROFILE%\repos\arise-workspace\arise-specs` |
| Specs repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise-specs` |
| Setup report | `%USERPROFILE%\repos\arise-workspace\setup\pm-env-{YYYY-MM-DD}.md` |
| Cursor plugin root | `%USERPROFILE%\.cursor\plugins\local` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Marketplace repo URL | `https://github.com/appseconnect/appse-marketplace.git` |
| Role plugin name | `appse-product` |

**Workspace roots for PM:** `arise-specs` only.

---

## Workflow

Follow these steps in order. Do not skip or reorder.

### Step 1 — Load Config

- Load `AGENTS.md` → brand, paths, AzDO config.
- Load `conventions/workspace.md` → PM workspace file and repo URLs.

### Step 2 — Workspace and Repository (pause before clone)

1. Create workspace root and setup folder (confirm if new):

   ```powershell
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\repos\arise-workspace"
   New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\repos\arise-workspace\setup"
   ```

2. **Clone or pull `arise-specs`** (confirm first):

   | State | Action |
   |-------|--------|
   | Empty or missing | `git clone <specs-url> <target>` |
   | Clean clone | Ask to pull; `git -C <target> pull origin main` |
   | Dirty working tree | Stop; surface `git status` |

   Default target: `%USERPROFILE%\repos\arise-workspace\arise-specs`. If a clone exists elsewhere, use that path in the workspace file instead.

3. **Write `pm-workspace.code-workspace`** (PM role only — do not merge other roles):

   ```json
   {
     "folders": [
       { "path": "<relative-or-absolute-path-to-arise-specs>", "name": "arise-specs" }
     ],
     "settings": {}
   }
   ```

4. **Open workspace** (confirm) — branch by AI tool from Step 0:

   **Cursor:**

   ```powershell
   cursor "$env:USERPROFILE\repos\arise-workspace\pm-workspace.code-workspace"
   ```

   **Claude with VS Code** — **File → Open Workspace from File…** or:

   ```powershell
   code "$env:USERPROFILE\repos\arise-workspace\pm-workspace.code-workspace"
   ```

   **Claude Code** — open the workspace file via `code` if available, or instruct the engineer to open `pm-workspace.code-workspace` manually.

5. **Verify** agent can read `arise-specs/handbook/azdo-config.md`.

### Step 3 — Validate MCP Tooling

- **Zoho Desk** — list 1 ticket to confirm auth.
- **Azure DevOps** (optional — only for teams that sync work items) — read project/team to confirm auth.
- Failures → warn with connector fix; continue.

### Step 4 — Create the folders

Only create what is missing. Never overwrite anything already there:

```
handbook/product/
├── prd.md
├── epics/index.md
├── releases/index.md
├── handoffs/
└── insights/

.appse/skills-config.yaml   ← criteria skills read (outside handbook/, never published)
```

`.appse/skills-config.yaml` starts from
`pm-intake/references/skills-config.template.yaml` — it holds no secrets.

`epics/index.md` and `releases/index.md` start as a title, a one-line description
and an empty table header — `| EPIC | Slug | What it delivers | Status |` and
`| Release | Date | Status | EPICs |`. `pm-epic` and `pm-launch-plan` add rows.

### Step 5 — Verify Sprint Config

Only if the team uses Azure DevOps:

- `handbook/azdo-config.md` present with `current_sprint`.
- Missing → prompt PM for sprint, create file. Not using the tracker → skip.

### Step 6 — Report

Save setup report to `setup/pm-env-{YYYY-MM-DD}.md` and print summary:

> "You are set up. Here is what I did:
>
> - Downloaded the handbook — the shared folder where all product documents live.
>   It is at `{path}`, on the `{branch}` branch.
> - Made you a workspace file and opened it. From now on, open
>   `pm-workspace.code-workspace` and everything will be where you expect it.
> - Created the folders for the product plan, EPICs, releases, the roadmap and so on.
>   Anything you already had is untouched.
> - Connected to Zoho Desk and Azure DevOps: {connected / not connected, and why}.
> - Your current sprint is **{current_sprint}** (only if you use Azure DevOps).
>
> I saved a note of all this at `{report-path}`, in case you need it later.
>
> **Where to start.** If you are not sure what to build yet, go through what
> customers have been telling us, or plan a request someone brought you → `/pm-intake`. If you already know the problem,
> write the product plan → `/pm-prd`."

---

## What "done" looks like

- [ ] The Product Manager tools are installed and your app can see them.
- [ ] `pm-workspace.code-workspace` exists and is open.
- [ ] The handbook is downloaded and readable.
- [ ] The product folders exist, with nothing of yours overwritten.
- [ ] A note of the setup is saved at `setup/pm-env-{date}.md`.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Safe to run again — only create what is missing, and always ask before downloading
- Never merge PM folders into another role's workspace file
- A connection that fails is a warning, not a dead end. Say what still works, what
  does not, and how to fix it later
- Never write passwords or access tokens to disk
