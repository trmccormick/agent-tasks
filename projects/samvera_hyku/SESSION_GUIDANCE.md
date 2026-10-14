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

Commands below are copied from the knapsack guidance. They are **not yet confirmed for this repo**:

```bash
sc up -d            # start the stack
sc down             # stop the stack
sc logs web -f      # follow web logs
sc sh               # shell into the web container
```

- Never `docker compose down -v`: it destroys the database.
- Domains and the local proxy setup (`*.localhost.direct`, `sc proxy cert`, `sc proxy up`) may or may not match this repo. [CONFIRM]
- Do not copy the knapsack's `up.sc.local.sh` / `down.sc.local.sh` scripts into assumptions about Hyku. [CONFIRM whether Hyku has its own.]
- **Known gap in the on-hold backport task:** its Phase 2 test steps use `docker compose up` and `http://localhost:3000`, which are probably wrong for a Stack Car project. Rewrite them with the confirmed Stack Car steps before that task is released.
- A shared Stack Car and Docker checklist for both projects is planned and not written yet. When it exists, point to it from here and do not duplicate it.

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
