# arch-setup — Workspace Setup

Clones the code and handbook repos, creates the **Architect workspace file**, checks
write access and ownership, and scaffolds the architecture folders.

Read `conventions/workspace.md` (appse-core) for repo URLs and workspace rules.

---

## Constants

| Constant | Value |
|----------|-------|
| Workspace root | `%USERPROFILE%\repos\arise-workspace` |
| Workspace file | `%USERPROFILE%\repos\arise-workspace\arch-workspace.code-workspace` |
| Specs clone target | `%USERPROFILE%\repos\arise-workspace\arise-specs` |
| Code clone target | `%USERPROFILE%\repos\arise-workspace\arise` |
| Specs repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise-specs` |
| Code repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise` |
| Setup report | `%USERPROFILE%\repos\arise-workspace\setup\arch-env-{YYYY-MM-DD}.md` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Role plugin name | `appse-architecture` |

**Workspace roots for Architecture:** `arise-specs`, `arise`.

---

## Workflow

### Step 1 — Load Config

- Load `AGENTS.md` and `conventions/workspace.md`.

### Step 2 — Workspace and Repositories (pause before clone)

1. Create `%USERPROFILE%\repos\arise-workspace` and `setup/` if missing.

2. For each repo (`arise-specs`, `arise`):

   | State | Action |
   |-------|--------|
   | Empty or missing | Confirm, then `git clone <url> <target>` |
   | Clean clone | Ask to pull |
   | Dirty working tree | Stop; surface `git status` |

   External clones are OK — reference their paths in the workspace file.

3. **Write `arch-workspace.code-workspace`** (Architect role only):

   ```json
   {
     "folders": [
       { "path": "<path-to-arise-specs>", "name": "arise-specs" },
       { "path": "<path-to-arise>", "name": "arise" }
     ],
     "settings": {}
   }
   ```

4. **Open workspace** — branch by AI tool:

   **Cursor:** `cursor "$env:USERPROFILE\repos\arise-workspace\arch-workspace.code-workspace"`

   **Claude with VS Code:** **File → Open Workspace from File…** or `code "...arch-workspace.code-workspace"`

   **Claude Code:** open workspace file via `code` or manually.

5. **Verify** `arise-specs/handbook/product/` and `handbook/engineering/` paths readable.

### Step 3 — Check Write Access and Ownership

```
git -C <arise-specs> push --dry-run   → can you push to arise-specs?
git -C <arise-specs> config user.email
```

Read `architecture.owners` in `arise-specs/.appse/skills-config.yaml`:

| Result | Tell them |
|---|---|
| Email listed | "You are an architecture owner — the arch skills will save." |
| Not listed | "You can run every arch skill, but only owners save. Ask your lead to add you if you should own architecture." |
| List empty | "No owners are set yet, so anyone can save. Ask your lead to add the owners." |

### Step 4 — Scaffold Architecture Folders

Idempotent:

```
handbook/engineering/architecture/
├── decisions/
├── reviews/
└── system-design/components/
```

The baseline docs themselves are written by `/arch-design`, never scaffolded empty.

### Step 5 — Report

Save `setup/arch-env-{YYYY-MM-DD}.md`:

> "You are set up. Here is what I did:
>
> - Downloaded `arise` (the product code — read only for you) and `arise-specs` (the handbook).
> - Made `arch-workspace.code-workspace` and opened it.
> - Push to `arise-specs`: {works / does not work — {why and how to fix}}.
> - Architecture owner: {yes / no / no owners set yet}.
>
> I saved a note of all this at `{report-path}`.
>
> **Where to start.** `/arch-design` shows which baseline docs are missing. For an
> approved EPIC, run `/arch-decision`."

---

## What "done" looks like

- [ ] The architecture tools are installed and your app can see them.
- [ ] `arch-workspace.code-workspace` exists and is open, with `arise` and `arise-specs`.
- [ ] Push access to `arise-specs` and owner status have been checked and reported.
- [ ] A note of the setup is saved.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Safe to run again — only create what is missing, and always ask before downloading
- Never merge Architect folders into another role's workspace file
- If Docker is not running, say so plainly and say what it is needed for — it is not
  needed today, but it will be
