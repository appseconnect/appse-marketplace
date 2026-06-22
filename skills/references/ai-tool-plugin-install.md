# AI Tool Plugin Install

Shared by `setup-{role}` skills and role `*-init` Step 0. **No symlinks** on Cursor — git clone or pull only.

## Phase 0 — Select AI tool

Ask the engineer **one explicit question** — do not auto-detect:

| Option | When to use |
|--------|-------------|
| **Cursor** | Cursor IDE with Agent chat |
| **Claude Code** | Claude Code CLI in a terminal |
| **Claude with VS Code** | Claude extension inside VS Code |

Record the choice for the setup report. Branch to the matching install path below.

---

## Role → plugins to install

Every role needs **`appse-core`** plus one role plugin:

| Setup skill | Role plugin | Role init (after plugins) |
|-------------|-------------|---------------------------|
| `setup-pm` | `appse-product` | `pm-init` |
| `setup-ds` | `appse-design` | `ds-init` |
| `setup-eng` | `appse-engineering` | `eng-init` |
| `setup-qa` | `appse-quality` | `qa-init` |
| `setup-ops` | `appse-devops` | `ops-init` |

Replace `{role-plugin}` below with the role plugin name from the table.

---

## Branch A — Cursor (git clone to local)

Run in the **Cursor integrated terminal**. Full procedure: `marketplace-plugin-install.md` **Phases 1–4**.

### Constants

| Constant | Value |
|----------|-------|
| Cursor plugin root | `%USERPROFILE%\.cursor\plugins\local` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Marketplace repo URL | `https://github.com/appseconnect/appse-marketplace.git` |
| Manifest file | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace\.cursor-plugin\marketplace.json` |

### Summary

1. Clone or pull **`appse-marketplace`** → read `.cursor-plugin/marketplace.json`.
2. Clone or pull **`appse-core`** + **`{role-plugin}`** from manifest entries.
3. Verify each clone: `.cursor-plugin/plugin.json`; core has `AGENTS.md`, `mcp.json`, `conventions/`.
4. **Fresh clone** → tell engineer to run **Developer: Reload Window**, then re-run setup or `*-init`.

### Preflight (Cursor)

| Check | Command | Failure remediation |
|-------|---------|---------------------|
| Git present | `git --version` | Install Git |
| Cursor CLI (optional) | `cursor --version` | Install Cursor |

### Open workspace (Cursor)

```powershell
cursor "$env:USERPROFILE\repos\arise-workspace\{role}-workspace.code-workspace"
```

---

## Branch B — Claude Code (CLI)

Run **`/plugin`** commands in Claude Code chat (terminal session). Pause for confirmation before install.

### Install

```text
/plugin marketplace add appseconnect/appse-marketplace
/plugin install appse-core@appse
/plugin install {role-plugin}@appse
```

Example for Product Engineer: `/plugin install appse-engineering@appse`.

### Verify

```powershell
claude plugin list
```

Confirm **`appse-core`** and **`{role-plugin}`** are listed. Also verify in chat:

```text
/plugin list
```

### Preflight (Claude Code)

| Check | Command | Failure remediation |
|-------|---------|---------------------|
| Claude Code CLI | `claude --version` | Install Claude Code |
| Git present | `git --version` | Install Git |

### Open workspace (Claude Code)

Open the role workspace file manually or via VS Code if installed:

```powershell
code "$env:USERPROFILE\repos\arise-workspace\{role}-workspace.code-workspace"
```

If `code` is unavailable, instruct the engineer to open the `.code-workspace` file from their file manager.

### Reload

Restart the Claude Code session or IDE if skills do not appear after install.

---

## Branch C — Claude with VS Code

Same **`/plugin`** install as Branch B — run in the **Claude chat panel inside VS Code**. Pause for confirmation before install.

### Install

```text
/plugin marketplace add appseconnect/appse-marketplace
/plugin install appse-core@appse
/plugin install {role-plugin}@appse
```

### Verify

```text
/plugin list
```

Confirm **`appse-core`** and **`{role-plugin}`** are listed.

### Preflight (Claude with VS Code)

| Check | Command | Failure remediation |
|-------|---------|---------------------|
| VS Code | `code --version` | Install VS Code |
| Claude extension | Claude panel available in VS Code | Install Claude extension |
| Git present | `git --version` | Install Git |

### Open workspace (VS Code)

**File → Open Workspace from File…** → select `{role}-workspace.code-workspace` under `%USERPROFILE%\repos\arise-workspace\`.

Or from terminal:

```powershell
code "$env:USERPROFILE\repos\arise-workspace\{role}-workspace.code-workspace"
```

### Reload

Restart VS Code if skills do not refresh after plugin install.

---

## Phase 5 — Continue role init

After plugins are installed (and Cursor reloaded if needed), read and follow the role init skill — **skip plugin install**:

| Role plugin | Init skill path (Cursor clone) |
|-------------|--------------------------------|
| `appse-product` | `%USERPROFILE%\.cursor\plugins\local\appse-product\skills\pm-init\SKILL.md` |
| `appse-design` | `%USERPROFILE%\.cursor\plugins\local\appse-design\skills\ds-init\SKILL.md` |
| `appse-engineering` | `%USERPROFILE%\.cursor\plugins\local\appse-engineering\skills\eng-init\SKILL.md` |
| `appse-quality` | `%USERPROFILE%\.cursor\plugins\local\appse-quality\skills\qa-init\SKILL.md` |
| `appse-devops` | `%USERPROFILE%\.cursor\plugins\local\appse-devops\skills\ops-init\SKILL.md` |

For Claude Code / Claude with VS Code, read the same init skill from the installed plugin (plugin manager resolves paths).

| Role init | Start at |
|-----------|----------|
| `pm-init`, `ds-init`, `eng-init`, `ops-init` | **Step 1** (skip Step 0) |
| `qa-init` | **Phase 1** (skip Phase 2 plugin install) |

Pass the **AI tool choice** to init so workspace-open steps use the correct branch.

---

## Update existing install

### Cursor

Re-run **`/setup-{role}`** or pull marketplace → core → role plugin. See **Update existing install** in `marketplace-plugin-install.md`.

### Claude Code / Claude with VS Code

```text
/plugin marketplace update appse
/plugin install appse-core@appse
/plugin install {role-plugin}@appse
```

Verify with `/plugin list`. Restart IDE if skills do not refresh.

---

## Guardrails

- Never auto-detect IDE — always ask Phase 0 question.
- Never pull on a dirty git working tree (Cursor path).
- Never write PATs or secrets during install.
- Brand: **appse ai** (lowercase, with a space).
