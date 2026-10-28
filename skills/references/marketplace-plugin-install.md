# Installing the appse ai tools in Cursor

This is the Cursor route. If you are using Claude Code or Claude with VS Code, read
`ai-tool-plugin-install.md` instead — the steps are different.

**Cursor branch only.** For IDE selection and Claude Code / Claude with VS Code paths, read `ai-tool-plugin-install.md` first.

Shared by the `{role}-setup` skills (Step 0) when **AI tool = Cursor**. **No symlinks** — git clone or pull only.

## Constants

| Constant | Value |
|----------|-------|
| Cursor plugin root | `%USERPROFILE%\.cursor\plugins\local` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Marketplace repo URL | `https://github.com/appseconnect/appse-marketplace.git` |
| Manifest file | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace\.cursor-plugin\marketplace.json` |

## Role → plugins to install

Every role needs **`appse-core`** plus one role plugin:

| Setup skill | Role plugin |
|-------------|-------------|
| `pm-setup` | `appse-product` |
| `design-setup` | `appse-product` *(appse-design reserved)* |
| `eng-setup` | `appse-engineering` |
| `arch-setup` | `appse-architecture` |
| `qa-setup` | `appse-quality` |
| `ops-setup` | `appse-devops` |

A senior developer who also reviews architecture runs both `eng-setup` and `arch-setup`.

---

## Step 1 — Get the catalogue

The catalogue is a small repo listing every appse ai plugin and where it lives.

Target: `%USERPROFILE%\.cursor\plugins\local\appse-marketplace`

| State | Action |
|-------|--------|
| Missing | Confirm, then `New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\plugins\local"` if needed; `git clone https://github.com/appseconnect/appse-marketplace.git "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace"` |
| Clean clone | Ask to pull; `git -C "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace" pull origin main` |
| Dirty working tree | Stop; surface `git status` |

Verify manifest exists: `.cursor-plugin/marketplace.json`.

---

## Step 2 — Work out which repos to download

1. Read `%USERPROFILE%\.cursor\plugins\local\appse-marketplace\.cursor-plugin\marketplace.json`.
2. For each required plugin name (`appse-core` + role plugin), find the matching entry in `plugins[]`.
3. Build clone URL from `source.repo`:

   ```
   https://github.com/{source.repo}.git
   ```

   Example: `appseconnect/appse-core` → `https://github.com/appseconnect/appse-core.git`

4. Clone target: `%USERPROFILE%\.cursor\plugins\local\{plugin.name}`

If a plugin name is missing from the manifest → hard stop; marketplace catalog is broken.

---

## Step 3 — Download them

Apply to **each** resolved plugin (`appse-core` + role plugin):

| State | Action |
|-------|--------|
| Missing | Confirm, then `git clone <url> <target>` |
| Clean clone | Ask to pull; `git -C <target> pull origin main` |
| Dirty working tree | Stop; surface `git status` — never pull on dirty |
| Non-empty dir, not a git repo | Stop; ask for a different target |

**Verify each clone:**

- `.cursor-plugin/plugin.json` present
- Role plugin: `skills/{role}-setup/SKILL.md` exists
- Core: `AGENTS.md`, `mcp.json`, `conventions/` present

---

## Step 4 — Restart Cursor

Cursor only notices new skills when it restarts. Skipping this is the single most
common reason someone thinks the install failed.

If **any** plugin was freshly cloned in this run → tell the user to run **Developer: Reload Window**, then re-run the same setup skill or the role `{role}-setup` skill to finish workspace bootstrap.

---

## Step 5 — Finish setting up your workspace

After plugins are present (and Cursor reloaded if needed), go back to your setup skill (`{role}-setup`) — it continues with the workspace steps in its `references/workspace.md`. Do not repeat the plugin install.

---

## Getting the latest version later

Use when plugins are **already cloned** and you want the latest skills from GitHub.

### When it is worth updating

- After a platform release or skill fix is pushed to GitHub
- When a skill fails with outdated paths or missing steps
- After `appse-marketplace` manifest adds or changes a plugin entry

### The easy way — run your setup skill again

Run your role setup skill in Agent chat. It pulls marketplace, core, and role plugin (Phases 1–3), then reloads skills after **Developer: Reload Window** if anything changed.

| Role | Command |
|------|---------|
| Product Manager | `/pm-setup` |
| Product Designer | `/design-setup` |
| Product Engineer | `/eng-setup` |
| Quality Engineer | `/qa-setup` |
| DevOps Engineer | `/ops-setup` |

Workspace repos (`arise-specs`, `arise`, …) are **not** updated by setup — only plugin clones under `plugins/local`.

### The manual way — pull each repo yourself

Confirm each repo has a **clean working tree** (`git status`) before pulling. Never pull on dirty.

**Product Manager example** (replace role plugin folder for your role):

```powershell
$root = "$env:USERPROFILE\.cursor\plugins\local"

git -C "$root\appse-marketplace" pull origin main
git -C "$root\appse-core" pull origin main
git -C "$root\appse-product" pull origin main
```

| Role | Third repo to pull |
|------|-------------------|
| Product Manager | `appse-product` |
| Product Designer | `appse-product` |
| Product Engineer | `appse-engineering` |
| Architect | `appse-architecture` |
| Quality Engineer | `appse-quality` |
| DevOps Engineer | `appse-devops` |

Then **Developer: Reload Window** in Cursor.

### The other way — your role's setup skill also offers to update

If plugins are installed, run **`/pm-setup`**, **`/eng-setup`**, etc. Step 0 pulls marketplace → resolves manifest → pulls core + role plugin when you confirm.

### After updating

1. Restart Cursor (`Developer: Reload Window`)
2. Confirm skills appear in the picker (`/fumadocs-formatting`, role skills)
3. Re-run `{role}-setup` only if workspace or handbook layout changed — not required for skill-only plugin updates

### Things that will bite you

- Never pull with uncommitted changes in a plugin clone
- Pull **marketplace first** — manifest may point at new plugin repos
- If a new plugin appears in the manifest but folder is missing, re-run **`/{role}-setup`** to clone it

