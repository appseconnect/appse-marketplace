# appse-marketplace

Plugin catalog for **appse ai** — the single entry point for installing all role-based plugins across Claude Code and Cursor.

## What this repo is

This repo publishes the **marketplace manifest** (`.cursor-plugin/marketplace.json`) and **role setup skills** (`setup-pm`, `setup-eng`, …) that install plugins from the catalog and chain into each role's `*-init` skill. Individual skill libraries live in the plugin repos under [appseconnect](https://github.com/appseconnect/).


| File                              | Platform    |
| --------------------------------- | ----------- |
| `.claude-plugin/marketplace.json` | Claude Code |
| `.cursor-plugin/marketplace.json` | Cursor catalog |
| `.cursor-plugin/plugin.json`      | Cursor bootstrap plugin |
| `skills/setup-{role}/SKILL.md`    | Cursor role setup (PM, Eng, QA, …) |


Both manifests list the same plugins and stay in sync.

## Plugins


| Plugin              | Repo                                                                   | Description                                              |
| ------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------- |
| `appse-core`        | [appse-core](https://github.com/appseconnect/appse-core)               | Conventions, paths, gates, AzDO, **fumadocs-formatting** |
| `appse-product`     | [appse-product](https://github.com/appseconnect/appse-product)         | PM skills — discovery, PRD, FRD, roadmap, stories        |
| `appse-engineering` | [appse-engineering](https://github.com/appseconnect/appse-engineering) | Architecture + engineering skills                        |
| `appse-quality`     | [appse-quality](https://github.com/appseconnect/appse-quality)         | QA skills — test, deploy, E2E, review                    |
| `appse-devops`      | [appse-devops](https://github.com/appseconnect/appse-devops)           | DevOps skills — deploy spec, build/publish, K8s          |
| `appse-design`      | [appse-design](https://github.com/appseconnect/appse-design)           | Product Design — reserved                                |


Every role plugin requires `appse-core`. The marketplace ships **`setup-{role}`** skills that clone the catalog, install core + role plugin from the manifest, then chain into **`*-init`** workspace bootstrap.

| Role | Setup skill (marketplace) | Role init (role plugin) |
|------|---------------------------|-------------------------|
| Product Manager | `/setup-pm` | `/pm-init` |
| Product Designer | `/setup-ds` | `/ds-init` |
| Product Engineer | `/setup-eng` | `/eng-init` |
| Quality Engineer | `/setup-qa` | `/qa-init` |
| DevOps Engineer | `/setup-ops` | `/ops-init` |

## Skill availability


| Plugin              | Skills in repo | Notes                                               |
| ------------------- | -------------- | --------------------------------------------------- |
| `appse-product`     | **11**         | Full PM workflow incl. post-ship handoff + insights |
| `appse-engineering` | **11**         | Includes `eng-docker` (local dev loop)              |
| `appse-devops`      | **5**          | `ops-docker` = post–Gate 4 compose; not daily dev   |
| `appse-quality`     | **7**          | Includes `qa-deploy`                                |
| `appse-design`      | **1**          | `ds-init` only                                      |


**P1 handoff chain:** `pm-workitem-sync` → Engineering → `ops-build` → `qa-deploy` → QA.

**Validation (2026-06-18):** Static pipeline audit ✅ with conditions — see `appse-core/validation/2026-06-18-pipeline-audit.md`. Live pilot pending.

## Workflows

### Product Manager

Install `appse-product`, then run skills in order. Each step gates the next.

```text
/pm-init                  bootstrap env (use /setup-pm first on fresh Cursor install)
   │
/pm-triage                Zoho Desk + AzDO → ranked problem list
   │ pick a problem
/pm-user-research         (with Designer) validate → JTBD + evidence
   │ problem validated
/pm-brainstorm            explore options → recommend one direction
   │ direction chosen
/pm-prd                   define the product/solution (assigns P / SP IDs)
   │
/pm-frd                   feature requirements + FRD (assigns F IDs)
   │
/pm-roadmap               sequence features
   │
/pm-stories-azdo          Epic→Feature→Story→Task + U/J IDs → Azure DevOps
   │
/pm-workitem-sync         [pm-to-eng] move backlog → Engineering
   └──────────────────────────────────────► HANDOFF to Engineering

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

### Product Engineer

Install `appse-engineering`. Receives features from PM handoff. Quality gates block the next stage until approved.

```text
/eng-init                 bootstrap dev env (.NET, Node, Docker, repos)
   │ receives feature on Eng backlog
/arch-spec                ADRs from PRD/FRD + codebase
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

### The Two Contracts That Cross Teams

```text
Feature → Image map     ops-build writes · qa-deploy reads · ops-k8s reads
                        (handbook/engineering/deployments/image-map.json — digest required)
Feature branch map      eng-dev writes · eng-pr / ops-build read
                        (handbook/engineering/feature-branch-mapping.json)
Env registry            eng-dev declares · ops-spec reconciles · all deploys read
Requirement ID registry pm-prd (P/SP) · pm-frd (F) · pm-stories-azdo (U/J)
                        ({BASE}/requirement-id-registry.json)
```

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

## Install (Claude Code)

```text
/plugin marketplace add appseconnect/appse-marketplace
/plugin install appse-engineering@appse
```

Replace `appse-engineering` with the role plugin you need (`appse-product`, `appse-quality`, etc.).

## Install (Cursor)

**Marketplace-first** — clone the catalog, run a setup skill for your role, then workspace init.

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\plugins\local"
git clone https://github.com/appseconnect/appse-marketplace.git "$env:USERPROFILE\.cursor\plugins\local\appse-marketplace"
```

Restart Cursor (`Developer: Reload Window`), then run your role setup skill:

| Role | Setup skill | Installs from manifest | Then runs |
|------|-------------|------------------------|-----------|
| Product Manager | `/setup-pm` | `appse-core` + `appse-product` | `pm-init` |
| Product Designer | `/setup-ds` | `appse-core` + `appse-design` | `ds-init` |
| Product Engineer | `/setup-eng` | `appse-core` + `appse-engineering` | `eng-init` |
| Quality Engineer | `/setup-qa` | `appse-core` + `appse-quality` | `qa-init` |
| DevOps Engineer | `/setup-ops` | `appse-core` + `appse-devops` | `ops-init` |

Setup skills read `.cursor-plugin/marketplace.json` in this repo and git-clone each plugin — **no symlinks**. After a fresh plugin clone, reload Cursor and re-run setup or `*-init`.

Full runbook: `handbook/playbooks/install-plugin.md` in `arise-specs`. Install rules: `skills/references/marketplace-plugin-install.md` or `appse-core/conventions/plugin-install.md`.

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
| PM (prd, frd, registry) | `handbook/product/`                                    |
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


Canonical rules: `appse-core/conventions/workspace.md`.

## Feature branches

Branch namespace prefix: `**appseai`** (not the repo name `arise`).


| Branch              | Pattern                        |
| ------------------- | ------------------------------ |
| Feature integration | `appseai/feature/{feature-id}` |
| Developer           | `appseai/{user}/{feature-id}`  |


PR at `eng-pr`: user branch → feature branch. Mapping: `handbook/engineering/feature-branch-mapping.json`.
AzDO Epic/Feature/Story titles: `[{feature-id}]` prefix — see `pm-stories-azdo`.
Full rules: `appse-core/conventions/branches.md` · `appse-core/conventions/naming.md`.

## Adding a new plugin

1. Create the plugin repo with `.claude-plugin/plugin.json` (and `.cursor-plugin/plugin.json` for Cursor).
2. Add an entry to **both** marketplace manifests in this repo.
3. Ensure the new plugin declares `requires: appse-core` in its manifest.

---

Built by Samarendra Kishor Ghosh — VP Product Engineering — APPSeCONNECT | appse ai