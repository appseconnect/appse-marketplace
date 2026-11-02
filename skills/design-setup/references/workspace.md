# design-setup — Initialize the Product Design Environment

First skill in the Design plugin. Clones the handbook repo, creates the **Design
role workspace file**, validates Figma, and scaffolds design folders.

Read `conventions/workspace.md` (appse-core) for repo URLs and workspace rules.

---

## Constants

| Constant | Value |
|----------|-------|
| Workspace root | `%USERPROFILE%\repos\arise-workspace` |
| Workspace file | `%USERPROFILE%\repos\arise-workspace\ds-workspace.code-workspace` |
| Specs clone target | `%USERPROFILE%\repos\arise-workspace\arise-specs` |
| Specs repo URL | `https://insyncworld@dev.azure.com/insyncworld/APPSeCONNECT-Reimagine/_git/arise-specs` |
| Setup report | `%USERPROFILE%\repos\arise-workspace\setup\ds-env-{YYYY-MM-DD}.md` |
| Marketplace path | `%USERPROFILE%\.cursor\plugins\local\appse-marketplace` |
| Role plugin name | `appse-product` (appse-design is reserved) |

**Workspace roots for Design:** `arise-specs` only.

---

## Workflow

### Step 1 — Load Config

- Load `AGENTS.md` and `conventions/workspace.md`.

### Step 2 — Workspace and Repository (pause before clone)

1. Create `%USERPROFILE%\repos\arise-workspace` and `setup/` if missing.

2. **Clone or pull `arise-specs`** (confirm first; stop on dirty tree).

3. **Write `ds-workspace.code-workspace`** (Design role only):

   ```json
   {
     "folders": [
       { "path": "<relative-or-absolute-path-to-arise-specs>", "name": "arise-specs" }
     ],
     "settings": {}
   }
   ```

4. **Open workspace** — branch by AI tool:

   **Cursor:** `cursor "$env:USERPROFILE\repos\arise-workspace\ds-workspace.code-workspace"`

   **Claude with VS Code:** **File → Open Workspace from File…** or `code "...ds-workspace.code-workspace"`

   **Claude Code:** open workspace file via `code` or manually.

5. **Verify** `arise-specs/handbook/guidelines/` is readable.

### Step 3 — Validate Design Tooling (MCP)

- **Figma** — confirm auth. Failure → warn, link connector settings.

### Step 4 — Confirm Brand Standards

- `handbook/guidelines/` holds appse ai brand standards.
- Absent → flag for PM/Design lead.

### Step 5 — Scaffold Design Folders

Idempotent:

```
handbook/design/
├── research/
├── flows/
├── wireframes/
├── design-system/
└── handoffs/
```

### Step 6 — Report

Save `setup/ds-env-{YYYY-MM-DD}.md`:

> "You are set up. Here is what I did:
>
> - Downloaded the handbook, `arise-specs`, where the plans and design notes live.
> - Made you a workspace file and opened it. Open `ds-workspace.code-workspace` from
>   now on.
> - Connected to Figma: {connected / not connected, and why}.
> - Found the brand guidelines: {yes, at {path} / not yet — worth adding}.
> - Created the design folders. Nothing of yours was overwritten.
>
> I saved a note of all this at `{report-path}`.
>
> **Where to start.** The design skills of your own are still being built. For now,
> the most useful thing you can do is pair with a product manager on understanding
> users → `/pm-research`. Designers usually spot things in that work that
> nobody else does."

---

## What "done" looks like

- [ ] The design tools are installed and your app can see them.
- [ ] `ds-workspace.code-workspace` exists and is open.
- [ ] The handbook is downloaded and readable.
- [ ] The design folders exist, with nothing of yours overwritten.
- [ ] A note of the setup is saved.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Safe to run again — only create what is missing, and always ask before downloading
- Never merge Design folders into another role's workspace file
- Brand standards live in `handbook/guidelines/`
