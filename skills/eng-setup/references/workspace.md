# eng-setup — Initialize the Engineering Environment

First skill in the Engineering plugin. Clones code + handbook repos, creates the
**Engineering role workspace file**, scaffolds folders, and validates toolchain.

Read `conventions/workspace.md` (appse-core) for repo URLs and workspace rules.

---

## Constants

| Constant | Value |
|----------|-------|
| Workspace root | `%USERPROFILE%\repos\arise-workspace` |
| Workspace file | `%USERPROFILE%\repos\arise-workspace\eng-workspace.code-workspace` |
| Specs clone target | `%USERPROFILE%\repos\arise-workspace\arise-specs` |
| Code clone target | `%USERPROFILE%\repos\arise-workspace\arise` |
| Specs repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise-specs` |
| Code repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise` |
| Setup report | `%USERPROFILE%\repos\arise-workspace\setup\eng-env-{YYYY-MM-DD}.md` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Role plugin name | `appse-engineering` |

**Workspace roots for Engineering:** `arise-specs`, `arise`.

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

3. **Write `eng-workspace.code-workspace`** (Engineering role only):

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

   **Cursor:** `cursor "$env:USERPROFILE\repos\arise-workspace\eng-workspace.code-workspace"`

   **Claude with VS Code:** **File → Open Workspace from File…** or `code "...eng-workspace.code-workspace"`

   **Claude Code:** open workspace file via `code` or manually.

5. **Verify** `arise-specs/handbook/product/` and `handbook/engineering/` paths readable.

### Step 3 — Validate Toolchain

```
dotnet --version      → expect .NET 8.x
node --version        → supported LTS
pnpm --version        → present
docker info           → Docker Desktop running
git --version         → present
```

Missing → warn with fix; continue.

### Step 4 — Validate MCP

- **Azure DevOps** — read project (used by `eng-pr`). Optional — warn if failing, never block.

### Step 5 — Scaffold Engineering Folders

Idempotent:

```
handbook/engineering/
├── architecture/   (overview, stack, patterns, decisions/, reviews/, system-design/)
├── specs/          ({epic}/{feature}/ — spec.md, reviews/, tests/; created per feature)
└── deployments/env/
plan/               (eng-dev plans — at arise-specs repo root)
```

`feature-branch-mapping.json` is **not** scaffolded. It is an optional record that
`worktree-start` / `eng-dev` create on first use if the engineer wants an AzDO and
PR-history trail. Branch context itself comes from git — see `resolve-branch-context`
in `conventions/branches.md` — so no skill needs this file to exist.

### Step 6 — Report

Save `setup/eng-env-{YYYY-MM-DD}.md`:

> "You are set up. Here is what I did:
>
> - Downloaded both repos: `arise` — the product code — and `arise-specs`, the
>   handbook where the plans and specs live.
> - Made you a workspace file and opened it. From now on, open
>   `eng-workspace.code-workspace` and both repos will be there together.
> - Checked your tools: .NET, Node, pnpm, git and Docker are {all there / mostly
>   there — {what is missing and how to get it}}.
> - Connected to Azure DevOps: {connected / not connected, and why}.
> - Created the folders for architecture notes and specs. Nothing of yours was
>   overwritten.
>
> I saved a note of all this at `{report-path}`.
>
> **Where to start.** If the architecture for your feature has not been decided yet,
> ask an architect to run `/arch-decision`. If it has been signed off, write the technical spec →
> `/eng-spec`.
>
> One thing to know for later: after you have built something, `/eng-run` runs it
> on your own machine so you can click through it before anyone reviews it."

---

## What "done" looks like

- [ ] The engineering tools are installed and your app can see them.
- [ ] `eng-workspace.code-workspace` exists and is open.
- [ ] Both `arise` and `arise-specs` are downloaded and in the workspace.
- [ ] Your tools have been checked, and you have been told about anything missing.
- [ ] A note of the setup is saved.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Safe to run again — only create what is missing, and always ask before downloading
- Never merge Engineering folders into another role's workspace file
- If Docker is not running, say so plainly and say what it is needed for — it is not
  needed today, but it will be
