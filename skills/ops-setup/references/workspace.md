# ops-setup — Initialize the DevOps Environment

First skill in the DevOps plugin. Clones repos, creates the **DevOps role workspace
file**, maps deploy structure, scaffolds deployment folders, validates cloud toolchain.

Read `conventions/workspace.md` (appse-core) for repo URLs and workspace rules.

---

## Constants

| Constant | Value |
|----------|-------|
| Workspace root | `%USERPROFILE%\repos\arise-workspace` |
| Workspace file | `%USERPROFILE%\repos\arise-workspace\ops-workspace.code-workspace` |
| Specs clone target | `%USERPROFILE%\repos\arise-workspace\arise-specs` |
| Code clone target | `%USERPROFILE%\repos\arise-workspace\arise` |
| Specs repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise-specs` |
| Code repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise` |
| Setup report | `%USERPROFILE%\repos\arise-workspace\setup\ops-env-{YYYY-MM-DD}.md` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Role plugin name | `appse-devops` |

**Workspace roots for DevOps:** `arise-specs`, `arise`.

---

## Workflow

### Step 1 — Load Config

- Load `AGENTS.md` and `conventions/workspace.md`.

### Step 2 — Workspace and Repositories (pause before clone)

1. Create `%USERPROFILE%\repos\arise-workspace` and `setup/` if missing.

2. Clone or pull `arise-specs` and `arise` (confirm first; stop on dirty tree).

3. Scan `arise/deploy/` and map layout after clone.

4. **Write `ops-workspace.code-workspace`** (DevOps role only):

   ```json
   {
     "folders": [
       { "path": "<path-to-arise-specs>", "name": "arise-specs" },
       { "path": "<path-to-arise>", "name": "arise" }
     ],
     "settings": {}
   }
   ```

5. **Open workspace** — branch by AI tool:

   **Cursor:** `cursor "$env:USERPROFILE\repos\arise-workspace\ops-workspace.code-workspace"`

   **Claude with VS Code:** **File → Open Workspace from File…** or `code "...ops-workspace.code-workspace"`

   **Claude Code:** open workspace file via `code` or manually.

6. **Verify** `arise/deploy/` and `arise-specs/handbook/engineering/deployments/` paths.

### Step 3 — Validate Toolchain

```
docker info
kubectl version
kubectl config current-context
az --version
az acr check-health
```

Report each; unauthenticated → warn with login hint.

### Step 4 — Validate Secret Store Access

- Key Vault reachable (`az keyvault list` or scoped check). **Names only — never values.**

### Step 5 — Validate MCP

- **Azure DevOps** — confirm auth (for `ops-build`).

### Step 6 — Scaffold DevOps Folders

Idempotent:

```
handbook/engineering/deployments/
├── index.md
├── image-map.json
├── env/index.md, secrets.md
├── local/docker-desktop.md
└── kubernetes/local, azure, amazon
```

### Step 7 — Report

Save `setup/ops-env-{YYYY-MM-DD}.md`:

> "You are set up. Here is what I did:
>
> - Downloaded both repos: `arise`, the product code, and `arise-specs`, the handbook.
> - Made you a workspace file and opened it. Open `ops-workspace.code-workspace` from
>   now on and both will be there.
> - Mapped what is already in `arise/deploy/`, so anything I write later follows the
>   layout that is there rather than inventing a new one.
> - Checked your tools: Docker, kubectl and the Azure CLI are {all there / mostly
>   there — {what is missing and how to get it}}.
> - Checked access to the image store and the vault: {reachable / not, and why}.
> - Created the deployment folders. Nothing of yours was overwritten.
>
> I saved a note of all this at `{report-path}`.
>
> **Where to start.** Once a feature's work has been merged, build it →
> `/ops-build`. When QA have signed a feature off and it is going to production,
> plan the deployment → `/ops-plan`."

---

## What "done" looks like

- [ ] The deployment tools are installed and your app can see them.
- [ ] `ops-workspace.code-workspace` exists and is open.
- [ ] Both `arise` and `arise-specs` are downloaded and in the workspace.
- [ ] The existing `arise/deploy/` layout is written down in the setup note.
- [ ] Your tools have been checked, and you have been told about anything missing.
- [ ] A note of the setup is saved.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Safe to run again — only create what is missing, and always ask before downloading
- Never put real build records into the image map during setup
- Never read out or print a secret's value — names only
- Never merge DevOps folders into another role's workspace file
