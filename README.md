# appse-marketplace

Plugin catalog for **appse ai** — the single entry point for installing all role-based plugins across Claude Code and Cursor.

## What this repo is

This repo publishes the **marketplace manifest** (`.cursor-plugin/marketplace.json`) and **role setup skills** (`setup-pm`, `setup-eng`, …) that install plugins from the catalog and chain into each role's `*-init` skill. Individual skill libraries live in the plugin repos under [appseconnect](https://github.com/appseconnect/).


| File                              | Platform    |
| --------------------------------- | ----------- |
| `.claude-plugin/marketplace.json` | Claude Code |
| `.cursor-plugin/marketplace.json` | Cursor catalog |
| `.claude-plugin/marketplace.json` | Claude Code / Claude with VS Code |
| `.cursor-plugin/plugin.json`      | Cursor bootstrap plugin |
| `skills/setup-{role}/SKILL.md`    | Role bootstrap — asks AI tool, installs plugins, chains to *-init |
| `skills/references/ai-tool-plugin-install.md` | Shared install branching (Cursor / Claude Code / Claude with VS Code) |


Both manifests list the same plugins and stay in sync.

## Plugins


| Plugin              | Repo                                                                   | Description                                              |
| ------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------- |
| `appse-core`        | [appse-core](https://github.com/appseconnect/appse-core)               | Conventions, paths, gates, AzDO, **fumadocs-formatting** |
| `appse-product`     | [appse-product](https://github.com/appseconnect/appse-product)         | PM skills — planning hub + roadmap, discovery, PRD, EPIC, Feature, launch |
| `appse-engineering` | [appse-engineering](https://github.com/appseconnect/appse-engineering) | Architecture + engineering skills                        |
| `appse-quality`     | [appse-quality](https://github.com/appseconnect/appse-quality)         | QA skills — test, deploy, E2E, review                    |
| `appse-devops`      | [appse-devops](https://github.com/appseconnect/appse-devops)           | DevOps skills — deploy spec, build/publish, K8s          |
| `appse-design`      | [appse-design](https://github.com/appseconnect/appse-design)           | Product Design — reserved                                |
| `appse-appdev`      | [appse-appdev](https://github.com/appseconnect/appse-appdev)           | App development — create apps (connectors): actions, triggers, tools |


Every role plugin requires `appse-core` — except **`appse-appdev`**, which is standalone
(direct install, no core; app development against a local Docker appse ai stack). The
marketplace ships **`setup-{role}`** skills that ask your AI tool, install core + role plugin per tool, then chain into **`*-init`** workspace bootstrap.

| Role | Setup skill (marketplace) | Role init (role plugin) |
|------|---------------------------|-------------------------|
| Product Manager | `/setup-pm` | `/pm-init` |
| Product Designer | `/setup-ds` | `/ds-init` |
| Product Engineer | `/setup-eng` | `/eng-init` |
| Quality Engineer | `/setup-qa` | `/qa-init` |
| DevOps Engineer | `/setup-ops` | `/ops-init` |
| App Developer | `/setup-appdev` | `/app-init` |

## Skill availability


| Plugin              | Skills in repo | Notes                                               |
| ------------------- | -------------- | --------------------------------------------------- |
| `appse-product`     | **12**         | Full PM workflow incl. post-ship handoff + insights; `pm-planning-hub` = tickets and requests → problems, initiatives, roadmap |
| `appse-engineering` | **11**         | Includes `eng-docker` (local dev loop)              |
| `appse-devops`      | **6**          | `ops-docker` = post–Gate 4 compose; not daily dev; `ops-cost-review` = monthly Azure FinOps |
| `appse-quality`     | **7**          | Includes `qa-deploy`                                |
| `appse-design`      | **1**          | `ds-init` only                                      |
| `appse-appdev`      | **5**          | `app-init` + `app-create`/`app-action`/`app-trigger`/`app-tools` |


**P1 handoff chain:** `pm-workitem-sync` → Engineering → `ops-build` → `qa-deploy` → QA.

**Validation (2026-06-18):** Static pipeline audit ✅ with conditions — see `appse-core/validation/2026-06-18-pipeline-audit.md`. Live pilot pending.

## Workflows

### Product Manager

Install `appse-product`, then run skills in order. Each step gates the next.
Pipeline: **PRD → EPIC → Feature → branch → build → release** — stories live inline
in the Feature doc, never as their own doc or branch.

```text
/pm-init                  bootstrap env (use /setup-pm first on fresh Cursor install)
   │
/pm-planning-hub          Zoho tickets or requests → problems, initiatives, roadmap (Now / Next / Later)
   │ pick a Now problem
/pm-user-research         (with Designer) validate → JTBD + evidence
   │ problem validated
/pm-brainstorm            explore options → recommend one direction
   │ direction chosen
/pm-prd                   define the product/solution (assigns P / SP IDs)
   │
/pm-epic                  EPIC doc — lists its features (sets EPIC slug)
   │
/pm-feature               Feature doc, stories inline (sets feature slug)
   │
/feature-worktree         branch: EPIC (T1) or {epic}/{feature} (T3)
   │
/pm-azdo          (optional) EPIC → Feature (stories inline) → Azure DevOps
/pm-workitem-sync         (optional) [pm-to-eng] move backlog → Engineering
   └──────────────────────────────────────► HANDOFF to Engineering

   pre-ship:
   /pm-launch-plan         launch plan + release record (product/releases/{version}.md)

   post-ship (continuous):
   /pm-marketing-handoff   single doc for marketing (after ship)
   /pm-user-activity       Clarity friction/drop-off analysis
```

### Product Designer

Install `appse-design`, then pair with PM during discovery. Working skills are reserved for a later release.

```text
/ds-init                  bootstrap design env (Figma, brand)
   │
   pairs on /pm-user-research   research plan + synthesis
   │
   (ds-* working skills reserved — flows, wireframes, design-system, handoff)
```

### App Developer

Install `appse-appdev` (standalone — no `appse-core`). Builds appse ai platform apps
(connectors) and their operations against a local Docker appse ai stack.

```text
/app-init                 setup repos + Docker + local appse ai (docker compose)
   │
/app-create               scaffold a new app (connector): code, auth, category
   │
/app-action               author action operations (request/response)
/app-trigger              author trigger operations (polling / webhook)
/app-tools                expose operations as agent-callable AI tools
```

### Product Engineer

Install `appse-engineering`. Receives features from PM handoff. Quality gates block the next stage until approved.

```text
/eng-init                 bootstrap dev env (.NET, Node, Docker, repos)
   │ receives feature on Eng backlog
/arch-spec                ADRs from PRD/EPIC/Feature + codebase
/arch-system              component + sequence diagrams
/arch-doc                 stack + patterns (as needed)
   │
/arch-review ═══════════ GATE 1 ✅   (blocks eng-spec)
   │
/eng-spec                 tech spec (lite or full by scope)
   │
/eng-review-spec ═══════ GATE 2 ✅   (blocks eng-dev)
   │
/eng-dev                  code + unit tests + DECLARE env vars
/eng-docker               run locally on Docker Desktop (dev loop)
   │
/eng-review-code ═══════ GATE 3 ✅   (blocks eng-pr)
   │
/eng-pr                   open Azure DevOps PR
   └──────────────────────────────────────► HANDOFF to Ops (build)
```

### DevOps Engineer — Build

Install `appse-devops`. Receives merged PR from Engineering. Produces verified image for QA.

```text
/ops-init                 bootstrap ops env (kubectl, ACR, Key Vault)
   │ receives merged PR (feature branch)
/ops-build                verify build → image → push ACR :tag
                          → write handbook/engineering/deployments/image-map.json
   └──────────────────────────────────────► HANDOFF to Quality
```

Pre-QA build uses env vars declared in `eng-dev` (`{feature}-env.md`). Full `ops-spec` (Gate 4) runs before production deploy — see Parallel Windows.

### Quality Engineer

Install `appse-quality`. Test planning runs in parallel during eng-dev; deploy and E2E run after the image is built.

```text
/qa-init                  bootstrap QA env (Playwright, Docker)
   │
   (parallel from spec, during eng-dev:)
/qa-spec                  test plan + coverage matrix
/qa-test                  test cases
/qa-azdo-tests            push to Azure DevOps Test Plans
   │ image now built
/qa-deploy {feature-id}   pull mapped image → Docker Desktop
/qa-e2e                   Playwright E2E against deployed build
   │
/qa-review ═════════════ GATE 4 ✅   (blocks production)
   └──────────────────────────────────────► HANDOFF to Ops (prod)
```

### DevOps Engineer — Production

Install `appse-devops`. Deploys the same verified image digest QA tested — no rebuild.

```text
/ops-k8s                  deploy mapped image → AKS / EKS
                          (same verified digest QA tested)
   │
   ✔️ Shipped
```

### The Handoff Chain (cross-team)

```text
PM ──[pm-workitem-sync]──► ENGINEER ──[eng-pr merged]──► OPS(ops-build)
                                                              │
                                                     [image-map.json]
                                                              ▼
OPS(prod) ◄──[Gate 4 ✅]── QA ◄──[qa-deploy]── OPS(ops-build)
```

### The Contracts That Cross Teams

```text
Feature → Image map     ops-build writes · qa-deploy reads · ops-k8s reads
                        (handbook/engineering/deployments/image-map.json — digest required)
Env registry            eng-dev declares · ops-spec reconciles · all deploys read
Requirement ids          NOT a file — P/SP in the PRD, slugs = folder/file names,
                        status in each doc, Azure DevOps ids on the board
Branch context          NOT a file — every skill derives feature id, user slug and
                        PR target from `git branch --show-current`
                        (appse-core/conventions/branches.md → resolve-branch-context)
```

**Skills are independent.** No skill requires a file another skill produced in order
to resolve branch context, so each plugin works standalone in any repo or workspace.
`handbook/engineering/feature-branch-mapping.json` still exists as an *optional*
record for the AzDO work item id and PR history — read to enrich, never to decide,
and git wins any disagreement.

### Docker (three skills, three moments)


| Skill        | Moment                                   | Source                                        |
| ------------ | ---------------------------------------- | --------------------------------------------- |
| `eng-docker` | Engineer dev loop (after `eng-dev`)      | Build from `arise` on user branch             |
| `qa-deploy`  | QA test (after `ops-build`)              | Pull mapped digest — no rebuild               |
| `ops-docker` | Prod compose (after Gate 4 + `ops-spec`) | Generate extend compose under `arise/deploy/` |


### The 4 Gates (who's blocked)


| Gate                | Owner    | Blocks            |
| ------------------- | -------- | ----------------- |
| 1 `arch-review`     | Engineer | `eng-spec`        |
| 2 `eng-review-spec` | Engineer | `eng-dev`         |
| 3 `eng-review-code` | Engineer | `eng-pr`          |
| 4 `qa-review`       | Quality  | production deploy |


### Parallel Windows (where teams overlap)

```text
Window 1   eng-dev ∥ qa-spec→qa-test→qa-azdo-tests      (both from approved spec)
Window 2   eng-review-code ∥ ops-spec prep (prod deploy spec — after Gate 4 path)
Window 3   qa-deploy→qa-e2e→qa-review ∥ ops-k8s prep     (both after image built)
```

## Install (Claude Code / Claude with VS Code)

Run **`/setup-{role}`** and choose your AI tool, or install manually:

```text
/plugin marketplace add appseconnect/appse-marketplace
/plugin install appse-core@appse
/plugin install appse-engineering@appse
```

Replace `appse-engineering` with the role plugin you need (`appse-product`, `appse-quality`, etc.).

## Install (Cursor)

**Marketplace-first** — clone the catalog, run a setup skill for your role, then workspace init.

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\plugins\local"
git clone https://github.com/appseconnect/appse-marketplace.git "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace"
```

Restart Cursor (`Developer: Reload Window`), then run your role setup skill and choose **Cursor**:

| Role | Setup skill | Installs from manifest | Then runs |
|------|-------------|------------------------|-----------|
| Product Manager | `/setup-pm` | `appse-core` + `appse-product` | `pm-init` |
| Product Designer | `/setup-ds` | `appse-core` + `appse-design` | `ds-init` |
| Product Engineer | `/setup-eng` | `appse-core` + `appse-engineering` | `eng-init` |
| Quality Engineer | `/setup-qa` | `appse-core` + `appse-quality` | `qa-init` |
| DevOps Engineer | `/setup-ops` | `appse-core` + `appse-devops` | `ops-init` |
| App Developer | `/setup-appdev` | `appse-appdev` (standalone) | `app-init` |

Setup skills read `.cursor-plugin/marketplace.json` in this repo and git-clone each plugin — **no symlinks**. After a fresh plugin clone, reload Cursor and re-run setup or `*-init`.

Full runbook: `handbook/playbooks/install-plugin.md` in `arise-specs`. Install rules: `skills/references/ai-tool-plugin-install.md` or `appse-core/conventions/plugin-install.md`.

## Update (Cursor)

When plugin skills or this catalog change on GitHub:

**Recommended:** re-run your role setup skill (`/setup-pm`, `/setup-eng`, …) — pulls marketplace, core, and role plugin.

**Manual pull** (clean working tree on each repo):

```powershell
$root = "$env:USERPROFILE\.cursor\plugins\local"
git -C "$root\appse-marketplace" pull origin main
git -C "$root\appse-core" pull origin main
git -C "$root\appse-product" pull origin main   # swap for your role plugin
```

| Role | Role plugin to pull |
|------|---------------------|
| Product Manager | `appse-product` |
| Product Designer | `appse-design` |
| Product Engineer | `appse-engineering` |
| Quality Engineer | `appse-quality` |
| DevOps Engineer | `appse-devops` |
| App Developer | `appse-appdev` |

Then **Developer: Reload Window**. See **Update existing install** in `skills/references/marketplace-plugin-install.md`.

## Update (Claude Code)

```text
/plugin marketplace update appse
/plugin install appse-core@appse
/plugin install appse-{role}@appse
```

## Path conventions

```
appse-marketplace/.cursor-plugin/marketplace.json   ← catalog (plugin URLs)
appse-marketplace/skills/setup-{role}/SKILL.md      ← role bootstrap entry
<plugin>/.cursor-plugin/plugin.json                 ← per-plugin manifest
<plugin>/skills/<skill-name>/SKILL.md               ← skill definitions
appse-core/skills/fumadocs-formatting/              ← handbook formatting (shared)
```

Handbook layout in `arise-specs`:


| Scope                   | Path                                                   |
| ----------------------- | ------------------------------------------------------ |
| PM (prd, epics, releases)           | `handbook/product/`                        |
| Engineering, QA, Ops    | `handbook/engineering/`                                |
| Shared                  | `handbook/playbooks/`, `guidelines/`, `azdo-config.md` |


Canonical table: `appse-core/conventions/paths.md`.

## Role workspaces

Each `*-init` skill creates a **role-specific** multi-root workspace file under
`%USERPROFILE%\repos\arise-workspace\`. Clones live in the same parent folder;
workspace files are not shared across roles.


| Role        | Init       | Workspace file                 | Repos                      |
| ----------- | ---------- | ------------------------------ | -------------------------- |
| PM          | `pm-init`  | `pm-workspace.code-workspace`  | `arise-specs`              |
| Design      | `ds-init`  | `ds-workspace.code-workspace`  | `arise-specs`              |
| Engineering | `eng-init` | `eng-workspace.code-workspace` | `arise-specs`, `arise`     |
| DevOps      | `ops-init` | `ops-workspace.code-workspace` | `arise-specs`, `arise`     |
| Quality     | `qa-init`  | `qa-workspace.code-workspace`  | `arise-specs`, `arise-e2e` |
| App Dev     | `app-init` | `appdev-workspace.code-workspace` | `appse-ai-community-apps`, `appse-ai-docs` |


Canonical rules: `appse-core/conventions/workspace.md`.

## Feature branches

Branch namespace prefix: `**appseai`** (not the repo name `arise`).


| Tier | Branch              | Pattern                             |
| ---- | ------------------- | ----------------------------------- |
| T1   | EPIC integration    | `appseai/feature/{epic}`            |
| T3   | Feature (under EPIC)| `appseai/feature/{epic}/{feature}`  |
| T2   | Developer           | `appseai/{user}/{feature-id}`       |

Branches are cut at EPIC or Feature level — never per story. `{feature-id}` is
`{epic}` or `{epic}/{feature}`, matching the EPIC and Feature docs.

`feature` is a reserved second segment — a user slug is never `feature`. That is what
makes any branch name parse unambiguously back into its tier.

**How many tiers is your choice, and `feature-worktree` asks.** It checks origin first.
Feature branch already there → it says who else is on the feature and asks whether you
want your own branch off it. Not there → it creates the feature branch and offers to
push it, so the rest of the team can pick it up. Two tiers (feature + user branch) is
the usual answer; one tier — the feature branch alone, worked on directly — suits solo,
short-lived, or documentation-only changes. Pass `tiers=1` or `tiers=2` to skip the
question in a scripted run.

Each push is its own separate yes, and nothing is ever pushed to `main`.

PR at `eng-pr` is always **exactly one tier up**, derived from the branch name, never
read from a file: user → feature → EPIC → `main`. That last hop is the
final merge — `eng-pr` raises it only after confirming Gate 4, and never merges it.
AzDO is optional; when used, Epic/Feature titles carry a `[{feature-id}]` prefix — see `pm-azdo`.
Full rules: `appse-core/conventions/branches.md` · `appse-core/conventions/naming.md`.

## Adding a new plugin

1. Create the plugin repo with `.claude-plugin/plugin.json` (and `.cursor-plugin/plugin.json` for Cursor).
2. Add an entry to **both** marketplace manifests in this repo.
3. Ensure the new plugin declares `requires: appse-core` in its manifest.

---

Built by Samarendra Kishor Ghosh — VP Product Engineering — APPSeCONNECT | appse ai