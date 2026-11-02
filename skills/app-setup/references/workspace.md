# app-setup — Initialize the App Development Environment

First skill in the App Dev plugin. Sets up the source + docs repos, brings up appse ai on
local Docker, and leaves you with a running platform to build and test apps against.

> **Standalone plugin.** appse-appdev installs directly and does **not** require
> `appse-core`. It uses **no** `arise` repos and **no** `handbook` — only
> `appse-ai-community-apps` (apps source) and `appse-ai-docs` (documentation).

---

## Constants

| Constant          | Value                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------- |
| Workspace root    | `%USERPROFILE%\repos\appseai-apps-workspace`                                              |
| Workspace file    | `%USERPROFILE%\repos\appseai-apps-workspace\appdev-workspace.code-workspace`              |
| Apps clone target | `%USERPROFILE%\repos\appseai-apps-workspace\appse-ai-community-apps`                      |
| Apps repo URL     | `https://insyncworld@dev.azure.com/insyncworld/appseai-apps/_git/appse-ai-community-apps` |
| Docs clone target | `%USERPROFILE%\repos\appseai-apps-workspace\appse-ai-docs`                                |
| Docs repo URL     | `https://github.com/appseconnect/appse-ai-docs.git`                                       |
| Compose template  | `references/docker-compose-public.yaml` in the installed `appse-appdev` plugin            |
| Compose file      | `%USERPROFILE%\repos\appseai-apps-workspace\docker-compose.yaml` (local copy — holds secrets) |
| Image registry    | `pulicacr.azurecr.io` (private — requires login)                                          |
| Workflow UI       | http://localhost:3000                                                                     |
| App-credential UI | http://localhost:8084                                                                     |
| Setup report      | `%USERPROFILE%\repos\appseai-apps-workspace\setup\appdev-env-{YYYY-MM-DD}.md`             |

---

## Workflow

### Step 1 — Setup Repos (pause before clone)

1. Create `%USERPROFILE%\repos\appseai-apps-workspace` and `setup\` if missing.
2. **Clone or pull both repos** (confirm first; stop on a dirty tree):
   - `appse-ai-community-apps` (Azure DevOps — apps source)
   - `appse-ai-docs` (GitHub — documentation)
3. **Write `appdev-workspace.code-workspace`** with both roots:

   ```json
   {
     "folders": [
       {
         "path": "<path-to-appse-ai-community-apps>",
         "name": "appse-ai-community-apps"
       },
       { "path": "<path-to-appse-ai-docs>", "name": "appse-ai-docs" }
     ],
     "settings": {}
   }
   ```

4. **Open workspace** — branch by AI tool (Cursor / Claude with VS Code / Claude Code).

### Step 2 — Check Docker (install if missing)

- Verify Docker Desktop is installed and running: `docker version` and `docker compose version`.
- If Docker is missing → install it (Windows: `winget install Docker.DockerDesktop`, or the
  Docker Desktop installer), start it, and confirm the engine is running. **Hard prerequisite** —
  stop here until Docker responds.

### Step 3 — Authenticate to the Image Registry

- The appse ai images live in the private registry `pulicacr.azurecr.io`.
- Log in before pulling: `az acr login --name pulicacr` (or `docker login pulicacr.azurecr.io`).
- Login fails → stop; asked use to contact system team to allow access. stop.

### Step 4 — Install appse ai Locally (docker compose)

The compose template ships in the `appse-appdev` plugin (`references/docker-compose-public.yaml`).
Copy it into the workspace root as `docker-compose.yaml` — **only if missing**; never overwrite
an existing copy, it holds the developer's local secrets. Then bring up the stack from the copy:

```powershell
docker compose -f "$env:USERPROFILE\repos\appseai-apps-workspace\docker-compose.yaml" pull
docker compose -f "$env:USERPROFILE\repos\appseai-apps-workspace\docker-compose.yaml" up -d
```

Before `up`, confirm the compose prerequisites:

- **Postgres + Redis** reachable at the host the compose points to (`ConnectionStrings__*` /
  `ConnectionStrings__Redis`). Adjust if your DB/Redis host differs.
- **Secrets** — the template ships secret keys empty (`SENDGRID_API_KEY=`, `Stripe__SecretKey=`,
  `AUTH_AUTH0_SECRET=`, …). The developer fills them in the local copy only, from the team's
  secret store. Never commit real values back to `appse-appdev`.
- **Placeholder tokens** (`#{...}#` — ngrok, gateway) filled in where needed. Core
  bring-up needs the registry login + DB/Redis; ngrok/onprem-gateway values are only needed
  for webhook/on-prem features.
- **Community-app dev loop:** to develop apps from your clone, mount `appse-ai-community-apps`
  into the app-management container — set the (commented) volume
  `"${USERPROFILE}/appseai/community-apps:/app/community-apps"` to your cloned path.

> "Next I download a local copy of appse ai so you can test your apps against a real
> running system.
>
> Fair warning: this is several gigabytes and will take a while on a slow connection.
> You only have to do it once.
>
> Shall I start it?"

### Step 5 — Verify the Stack

- `docker compose -f "$env:USERPROFILE\repos\appseai-apps-workspace\docker-compose.yaml" ps` — all services up/healthy.
- Open the **Workflow UI** (http://localhost:3000) and the **app-credential UI**
  (http://localhost:8084).
- Unhealthy services → check `docker compose logs` for the failing container (usually
  registry auth or DB/Redis connectivity).

### Step 6 — Validate the Apps Source

- Confirm `appse-ai-community-apps` is cloned and populated — its existing app folders and
  `{operation_code}.json` files are the source of truth for `app-create` / `app-action` /
  `app-trigger` / `app-tools` (no MCP lookup needed).

### Step 7 — Report

Save `setup/appdev-env-{YYYY-MM-DD}.md`:

> "You are set up, and appse ai is running on your machine.
>
> **Two things you can open right now:**
>
> - The workflow builder — http://localhost:3000. This is what your users see.
> - The sign-in details screen — http://localhost:8084, where connections to other
>   systems are set up.
>
> **What I did:**
>
> - Downloaded `appse-ai-community-apps`, where apps are built, and `appse-ai-docs`,
>   where they get documented.
> - Made you a workspace file and opened it. Open `appdev-workspace.code-workspace`
>   from now on.
> - Started a full local copy of appse ai, so you can test an app end to end without
>   touching anything shared.
>
> I saved a note of all this at `{report-path}`.
>
> **Where to start.** Create the app itself → `/app-create`. Then give it things it
> can do: `/app-action` for things it performs, `/app-trigger` for things it reacts
> to, `/app-tools` for what an AI agent may call. Writing the documentation in
> `appse-ai-docs` is yours to do — the skills do not write it for you."

---

## What "done" looks like

- [ ] Both repos are downloaded, and `appdev-workspace.code-workspace` is open.
- [ ] Docker is installed and running.
- [ ] You are signed in to the image store.
- [ ] A local copy of appse ai is running.
- [ ] Both screens open in your browser: the workflow builder on port 3000 and the
      sign-in details screen on port 8084.
- [ ] A note of the setup is saved.

---

## Output Rules

- Everything the user reads is plain English — `conventions/plain-english.md` (appse-core)
- Warn before the large download, and say roughly how big it is
- Safe to run again — only create what is missing. Always ask before downloading, and
  before `docker compose pull` (large images).
- Never merge App Dev roots into another role's workspace file.
- A running local platform is a hard prerequisite for `app-create` / `app-action` /
  `app-trigger` / `app-tools`.
- Skills create the app + operations in `appse-ai-community-apps`; the developer writes the
  documentation in `appse-ai-docs` (app-integrations).
