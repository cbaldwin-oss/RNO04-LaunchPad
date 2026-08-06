# dashboard-datacenter-template

**A stencil for spinning up a new client dashboard.** This repo holds *no code*
— only the thin scaffolding a client needs: a served `html-dashboard/` folder
and the caller workflow that borrows the shared engine
([`arc-dashboard-core`](https://github.com/kspann-hub/arc-dashboard-core)) once
an hour.

## Create a new client from this template

1. **Green "Use this template" button → Create a new repository.**
   - Owner: `kspann-hub`. Name it `dashboard-<client>` (e.g. `dashboard-switch`).
   - **Private.**
2. In the new repo: **Settings → Secrets and variables → Actions → New repository secret.** Add all four:
   | Secret | Value |
   |--------|-------|
   | `CXALLOY_IDENTIFIER` | this client's CxAlloy API identifier |
   | `CXALLOY_SECRET` | this client's CxAlloy API secret |
   | `CORE_APP_ID` | the arc-dashboard-core-reader App's ID (same for every client) |
   | `CORE_APP_PRIVATE_KEY` | full contents of that App's `.pem` (same for every client) |

   > `kspann-hub` is a personal account, so there are no org-level secrets — the
   > two `CORE_APP_*` values are pasted into **each** client repo. They only grant
   > read access to the shared **code**, never to any client's data.
3. Confirm the **arc-dashboard-core-reader** GitHub App is installed and can read
   `arc-dashboard-core` (one-time org setup; see that repo / the design doc).
4. **Actions tab → "Refresh dashboard data" → Run workflow** to do the first
   build immediately (otherwise it waits for the top of the hour). It syncs the
   client, writes `html-dashboard/data/*.json`, copies in the shell, and commits.
5. Point your host / auth proxy at this repo's `html-dashboard/` folder.

That's it — no code to copy, nothing to keep in sync. Engine updates arrive
automatically via the `@v1` tag the caller tracks.

## What's here

| Path | Role |
|------|------|
| `.github/workflows/refresh.yml` | The caller. Schedules hourly + on-demand runs; delegates all work to the reusable workflow in core via `secrets: inherit`. |
| `html-dashboard/` | The served folder. Starts empty; the hourly run fills `data/` and drops in `index.html` from core. |

## Version pinning

The caller tracks `@v1` (latest v1.x.x) by default. To pin or canary a specific
engine version for this one client, set `core_ref:` under `with:` in
`.github/workflows/refresh.yml` (e.g. `core_ref: v1.1.0`).
