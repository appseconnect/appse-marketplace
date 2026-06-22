# Marketplace-First Plugin Install (Cursor)

**Cursor branch only.** For IDE selection and Claude Code / Claude with VS Code paths, read `ai-tool-plugin-install.md` first.

Shared by `setup-{role}` skills and role `*-init` Step 0 when **AI tool = Cursor**. **No symlinks** — git clone or pull only.

## Constants

| Constant | Value |
|----------|-------|
| Cursor plugin root | `%USERPROFILE%\.cursor\plugins\local` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Marketplace repo URL | `https://github.com/appseconnect/appse-marketplace.git` |
| Manifest file | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace\.cursor-plugin\marketplace.json` |

## Role → plugins to install

Every role needs **`appse-core`** plus one role plugin:

| Setup skill | Role plugin | Role init (after plugins) |
|-------------|-------------|---------------------------|
| `setup-pm` | `appse-product` | `pm-init` |
| `setup-ds` | `appse-design` | `ds-init` |
| `setup-eng` | `appse-engineering` | `eng-init` |
| `setup-qa` | `appse-quality` | `qa-init` |
| `setup-ops` | `appse-devops` | `ops-init` |

---

## Phase 1 — Clone or update marketplace catalog

Target: `%USERPROFILE%\.cursor\plugins\local\appse-marketplace`

| State | Action |
|-------|--------|
| Missing | Confirm, then `New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\plugins\local"` if needed; `git clone https://github.com/appseconnect/appse-marketplace.git "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace"` |
| Clean clone | Ask to pull; `git -C "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace" pull origin main` |
| Dirty working tree | Stop; surface `git status` |

Verify manifest exists: `.cursor-plugin/marketplace.json`.

---

## Phase 2 — Resolve plugin repos from manifest

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

## Phase 3 — Clone or update each plugin

Apply to **each** resolved plugin (`appse-core` + role plugin):

| State | Action |
|-------|--------|
| Missing | Confirm, then `git clone <url> <target>` |
| Clean clone | Ask to pull; `git -C <target> pull origin main` |
| Dirty working tree | Stop; surface `git status` — never pull on dirty |
| Non-empty dir, not a git repo | Stop; ask for a different target |

**Verify each clone:**

- `.cursor-plugin/plugin.json` present
- Role plugin: `skills/{role}-init/SKILL.md` exists
- Core: `AGENTS.md`, `mcp.json`, `conventions/` present

---

## Phase 4 — Reload Cursor

If **any** plugin was freshly cloned in this run → tell the user to run **Developer: Reload Window**, then re-run the same setup skill or the role `*-init` skill to finish workspace bootstrap.

---

## Phase 5 — Continue role init

After plugins are present (and Cursor reloaded if needed), read and follow the role init skill from the **role plugin clone**, starting at workspace bootstrap (skip plugin install):

| Role plugin | Init skill path |
|-------------|-----------------|
| `appse-product` | `%USERPROFILE%\.cursor\plugins\local\appse-product\skills\pm-init\SKILL.md` |
| `appse-design` | `%USERPROFILE%\.cursor\plugins\local\appse-design\skills\ds-init\SKILL.md` |
| `appse-engineering` | `%USERPROFILE%\.cursor\plugins\local\appse-engineering\skills\eng-init\SKILL.md` |
| `appse-quality` | `%USERPROFILE%\.cursor\plugins\local\appse-quality\skills\qa-init\SKILL.md` |
| `appse-devops` | `%USERPROFILE%\.cursor\plugins\local\appse-devops\skills\ops-init\SKILL.md` |

For role inits: execute from **Step 1 / Phase 1** onward — **do not repeat** plugin install (Step 0 / Phase 2 plugin branch).

---

## Update existing install

Use when plugins are **already cloned** and you want the latest skills from GitHub.

### When to update

- After a platform release or skill fix is pushed to GitHub
- When a skill fails with outdated paths or missing steps
- After `appse-marketplace` manifest adds or changes a plugin entry

### Option A — re-run setup skill (recommended)

Run your role setup skill in Agent chat. It pulls marketplace, core, and role plugin (Phases 1–3), then reloads skills after **Developer: Reload Window** if anything changed.

| Role | Command |
|------|---------|
| Product Manager | `/setup-pm` |
| Product Designer | `/setup-ds` |
| Product Engineer | `/setup-eng` |
| Quality Engineer | `/setup-qa` |
| DevOps Engineer | `/setup-ops` |

Workspace repos (`arise-specs`, `arise`, …) are **not** updated by setup — only plugin clones under `plugins/local`.

### Option B — manual git pull

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
| Product Designer | `appse-design` |
| Product Engineer | `appse-engineering` |
| Quality Engineer | `appse-quality` |
| DevOps Engineer | `appse-devops` |

Then **Developer: Reload Window** in Cursor.

### Option C — role init Step 0

If plugins are installed, run **`/pm-init`**, **`/eng-init`**, etc. Step 0 pulls marketplace → resolves manifest → pulls core + role plugin when you confirm.

### After update

1. Restart Cursor (`Developer: Reload Window`)
2. Confirm skills appear in the picker (`/fumadocs-formatting`, role skills)
3. Re-run `{role}-init` only if workspace or handbook layout changed — not required for skill-only plugin updates

### Guardrails

- Never pull with uncommitted changes in a plugin clone
- Pull **marketplace first** — manifest may point at new plugin repos
- If a new plugin appears in the manifest but folder is missing, re-run **`/setup-{role}`** to clone it

