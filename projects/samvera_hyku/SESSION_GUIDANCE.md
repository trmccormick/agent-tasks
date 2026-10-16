# Samvera Hyku — Session Guidance

**Last Updated**: 2026-10-14
**Purpose**: Project-specific paths, rules and startup checks for agents working on the upstream `samvera/hyku` project.
**Evidence labels**: **VERIFIED** = read in the repo or confirmed by a command. **REPORTED** = stated by Tracy, not re-checked. **[CONFIRM]** = needs an agent with repo access to check. **[FILL IN]** = value not known to the author.

Universal rules live in `rules/GUARDRAILS.md` and `SESSION_CLOSEOUT.md`; this file does not repeat them. Read `projects/samvera_hyku/README.md` too: `status.md` says the project's agent workflow and commit protocols are documented there (not read when this file was written).

---

## 1. Startup (read-only; run before starting work)

```bash
ls projects/samvera_hyku/tasks/active/
ls projects/samvera_hyku/tasks/backlog/
head -40 projects/samvera_hyku/status.md
tail -30 projects/samvera_hyku/status.md
```

**Why**: the `status.md` header and "Current Status" were last curated 2026-08-11 and are stale. The newest facts are in the "Pending Items (added 2026-10-14)" section at the end of the file. Treat claims in the header as unverified until re-checked.

---

## 2. Paths (relative to the agent-tasks repo root)

| Item | Path |
|---|---|
| Project folder | `projects/samvera_hyku/` |
| Active tasks | `projects/samvera_hyku/tasks/active/` |
| Backlog tasks | `projects/samvera_hyku/tasks/backlog/` |
| Completed tasks | `projects/samvera_hyku/tasks/completed/` |
| Handoffs | `projects/samvera_hyku/handoffs/` |
| Summaries | `projects/samvera_hyku/summaries/` |
| Status | `projects/samvera_hyku/status.md` |
| Notes | `projects/samvera_hyku/notes.md` |
| Source repo (local clone) | [FILL IN] — upstream: https://github.com/samvera/hyku |

The `tasks/current/` folder is not part of the standard layout (see section 6).

---

## 3. Project state (REPORTED unless a hash is shown)

- **What this project is:** work on `samvera/hyku`, which is multitenant Hyrax. Hyrax, Hyku and the knapsack are three layers that flow together. This project has **no git submodule**; `wvulibraries_knapsack` embeds Hyku as one. Fixes generally start in the knapsack and are then contributed here (rule 3 below).
- **GA multi-tenant fix:** Phase 1 is on branch `fix/ga-tenant-property-scoping` (Tracy's work for Samvera). Phase 2 is blocked until a VM has Google Analytics on both tenants, to be set up by the current Hyku product owner.
- **Facet-limiting backport:** `tasks/backlog/2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md` is **on hold** (carried in from the stray `projects/hyku/` folder in `3a7804c`). It waits on WVU production validation and on a comparison with a Notch8 colleague's upstream-compatible solution. **Do not dispatch.**

---

## 4. Rules specific to this project

1. **One branch per upstream PR.** `status.md` (2026-08-11 entry) says branch separation is needed for the PRs. Do not combine unrelated fixes on one branch.
2. **Hyrax, Hyku and the knapsack flow together** (Tracy, 2026-10-14). Hyku is multitenant Hyrax, so making it work means creating overrides or changing Hyrax code to fix Hyku. Changing Hyku code is the point of this project, on a feature branch (one per PR). In the knapsack, `hyrax-webapp/` is the `samvera/hyku` repo as a submodule (VERIFIED: knapsack README), so a knapsack override that is a backport candidate is really a change to core Hyku (or Hyrax) code, made with the Samvera community by pull request, not by editing the knapsack's checkout. **Fixes can also start in Hyrax:** sometimes a Hyku fix only works after Hyrax core is changed first, then correctly overridden in Hyku (Tracy, REPORTED, from past work). Before writing an override, check whether the root cause is in Hyrax. **"Do NOT modify core Hyku code directly" is a `wvulibraries_knapsack` rule** (it keeps the submodule clean while the change goes upstream) and does not apply here. The same sentence appears under "Special Warnings & Conventions" in this project's `status.md`; it is a stale carry-over, so ignore it.
3. **Fix-in-knapsack-first pattern** (from the knapsack guidance): a fix is made and verified in the knapsack first, then contributed upstream as a pull request to `samvera/hyku` (or `samvera/hyrax`). See `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md` and `UPSTREAM_CONTRIBUTION_AUDIT.md`.
4. **Task file naming:** `YYYY-MM-DD-PRIORITY-TYPE-DESCRIPTIVE-NAME.md` (see `TASK_TEMPLATE.md`).

---

## 5. Local development (Stack Car) — **[CONFIRM]** all of this section

`samvera/hyku` uses Stack Car, and the Docker and Stack Car problems seen in `wvu_knapsack` also occur here (REPORTED by Tracy, 2026-10-14).

Upstream docs (`docs/getting-started.md`, REPORTED, read through a summarising tool) use Stack Car only for the proxy and certificate, and Docker Compose for the stack itself. Not yet confirmed on the laptop:

```bash
sc proxy cert            # once per machine (needs two passwords)
sc proxy up              # start the shared proxy
docker compose build
docker compose up web
docker compose stop
docker compose down
```

- Agents never run `docker compose down -v` or the cleanup script (they destroy the local database). Tracy runs them herself (REPORTED, 2026-10-14).
- Local URL: `https://admin-hyku.localhost.direct`. If the `APP_NAME` variable is changed it becomes `https://admin-APP_NAME.localhost.direct` (default `APP_NAME` is `hyku`). **[CONFIRM]**
- Tracy uses her own copies of `up.sc.local.sh`, `down.sc.local.sh` and `scripts/cleanup-dev.sh` in Hyku, kept on her laptop and gitignored (REPORTED, 2026-10-14). They are not in the upstream repo: do not stage or commit them, and do not assume anyone else has them.
- **Known gap in the on-hold backport task:** its Phase 2 test steps use `docker compose up` and `http://localhost:3000`. Upstream docs also use Docker Compose for the stack, so the commands may be fine, but the URL is probably `https://admin-hyku.localhost.direct`. **[CONFIRM]** and fix before that task is released.
- Logs: `sc logs` is not a Stack Car command (it is not in the Stack Car README, https://github.com/notch8/stack_car). Read logs with `docker compose logs -f web`. **[CONFIRM]** whether Hyku writes log files to the host.
- The Stack Car and Docker notes for the knapsack are in `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md` (Local Development Rules). Tracy reports the problems are the same here, but the Hyku steps above are unconfirmed, so do not copy knapsack commands without checking.

---

## 6. Folders and files to leave alone (not done; planning agent with Tracy)

- `tasks/current/` and `tasks/active/` both hold a file named `2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md` (VERIFIED by directory listing 2026-10-14; contents not compared). Do not move, copy or delete either (the duplicate-copy rule in `rules/GUARDRAILS.md`).
- `tasks/active/` holds three undated files: `facet_label_defaults_investigation.md`, `investigate_wings_modelregistry_fix.md`, `upstream-wings-defensive-checks.md`.
- Proposed handling is in `projects/agent-tasks/summaries/2026-10-07-PLAN-SAMVERA-REORG.md` (a draft, not approved).

---

## 7. Reference documents (agent-tasks repo)

- `README.md` — workflow and role definitions
- `SESSION_CLOSEOUT.md` — research/implementation closeout
- `PLANNING_AGENT_CLOSEOUT.md` — planning closeout
- `TASK_TEMPLATE.md` — task file format
- `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md` — knapsack rules, upstream contribution pattern
