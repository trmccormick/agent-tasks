# Session Handoff — 2026-10-13 to 2026-10-15 — Workflow review and project guidance (web session)

**Scope:** agent-tasks workflow work (closeout rules, README, `needs_review.md`, `status.md`, the `galaxy_game` guidance note, the `samvera_hyku` folder) and the project guidance files for `wvulibraries_knapsack` and `samvera_hyku`. It is filed under `projects/agent-tasks/handoffs/claude-free-web/` because the work was on the agent-tasks repo, not on any single project.

Written by the web review agent on 2026-10-18, closing a session last active 2026-10-15 22:45. **VERIFIED** = command output Tracy pasted, or a file read directly. **REPORTED** = stated by Tracy, or read second-hand (for example through a summarising fetch tool, which returns summaries, not verbatim files). Nothing in this file was re-checked on 2026-10-18; other sessions may have pushed since `eefacd0`.

## How to start the next session

1. Read `WEB_AGENT_SESSION_START.md`, this file, and the 2026-10-14 and 2026-10-15 entries at the end of `projects/agent-tasks/status.md`.
2. Ask Tracy to run `git fetch` and `git log --oneline -10`. This handoff is VERIFIED only through `eefacd0`.
3. Start with "Not done yet" items 1 and 2: they need results from Tracy's laptop and clear most of the `[CONFIRM]` flags.
4. Pacing rule that worked: one small edit per command, a guard check before each edit, paste the `git diff` before anything is staged, and `git status -sb` after every push.

## What landed (VERIFIED on origin, `eefacd0`)

Hashes are from push output and `git log` that Tracy pasted.

| Commit | What |
|---|---|
| `8eca82d` | `needs_review.md`: commit-conventions entry rewritten (CONTRIBUTING.md exists; Tracy's decision recorded) |
| `e9145bf` | `SESSION_CLOSEOUT.md` 1b item 6: no entry for a session with nothing to hand over |
| `750c2b1` | Backlog task: knapsack CONTRIBUTING "Commit messages" subsection (not dispatched) |
| `0640ca6` | Previous web session's handoff committed |
| `e79c255` | `galaxy_game/SESSION_GUIDANCE.md`: task-folder note (`backlog/current/`) |
| `6d0d80f` | `SESSION_CLOSEOUT.md` sections 2 and 2a: project guidance can name a different held-work folder |
| `514dcce` | `README.md`: Stale Active Task Protocol line |
| `2fa6af4` | `status.md`: 2026-10-13a session log |
| `3a7804c` | Facet-limiting backport task moved to `samvera_hyku/tasks/backlog/`, on hold |
| `6a7bcd2` | Stale `projects/hyku/status.md` archived to `archive/projects/hyku/` |
| `59773ae` | `samvera_hyku/status.md`: Pending Items appended |
| `66f8f50` | `samvera_hyku/status.md`: stale "do not modify core Hyku" rule corrected |
| `97c5d64` | `samvera_hyku/SESSION_GUIDANCE.md` created |
| `4a34146` | `status.md`: 2026-10-14 session log (Tracy's own commit; +14 lines) |
| `56e8731` | Knapsack guidance: `sc logs`, `down -v`, paused `up.prod.local.sh` |
| `1d0606f` | Knapsack guidance: no-VM-access rule, pinning note, stale gotchas |
| `9db1414` | Knapsack guidance: new checklists A and B |
| `792b5de` | Knapsack guidance: test command, cleanup-script warning, troubleshooting |
| `66ae0e6` | `samvera_hyku/SESSION_GUIDANCE.md` section 5 rewritten |
| `876c73c` | `status.md`: 2026-10-15 session log |
| `eefacd0` | Knapsack guidance: `sc help` results recorded, `sc exec` note corrected |

**Decisions Tracy made:**
- SHA-pinning of `hyrax-webapp` is valid and mostly used on the production VM, until Samvera locks stable releases (they are working on this for 7.2). The upgrade branch tracks `7.2-stable`.
- No agent has access to any VM. DevOps does most VM work and Tracy is the backup. Agents work only on Tracy's laptop. They may read the public live site, but Cloudflare may block parts of it.
- Hyku is multitenant Hyrax. A fix may start in Hyrax and then be overridden in Hyku. "Do not modify core Hyku" is a knapsack rule only. WVU runs only the knapsack build; work on Hyku and Hyrax feeds it.
- Local agents may run `up.sc.local.sh` and `down.sc.local.sh`. They never run `docker compose down -v` or `scripts/cleanup-dev.sh`: Tracy runs those. (Tracy did not object to this wording; it was not an explicit choice between options.)
- `up.prod.local.sh` (production setup locally over HTTP) is paused and may be removed: the IIIF viewer needs HTTPS.
- A one-line fix does not need a task file. Tracy made the log-mount and `cleanup-dev.sh` fixes in `wvu_knapsack` directly.

## Not done yet

1. **Laptop checks (Tracy)** — `ls hyrax-webapp/log`; the container names for the `docker ps` grep in the guidance; run one spec with `sc exec bundle exec rspec spec/path`; try `sc console` (the knapsack README's `sc exec web rails console` probably runs `docker compose exec web web ...`; this is an inference from `sc help`, not run). Also whether Hyku writes logs to the host. Then update the matching `[CONFIRM]` flags in both guidance files. Already VERIFIED: `sc help` lists `sc sh`, `sc down`, `sc pull`; `sc logs` does not exist; the merged compose config keeps Solr's `/var/solr` mount beside `./data/logs/solr`.
2. **Addendum to the 2026-10-15 `status.md` entry** recording what item 1 resolved, once Tracy has run the checks.
3. **`samvera_hyku/SESSION_GUIDANCE.md` section 5** — every Hyku local step is `[CONFIRM]`. The on-hold backport task's Phase 2 steps (`docker compose up`, `localhost:3000`) may be fine apart from the URL, which is probably `https://admin-hyku.localhost.direct`. Before that task is ever released, its dispatch interface must be replaced with the one from `TASK_TEMPLATE.md`. `projects/samvera_hyku/README.md` was not read.
4. **Stale entries belonging to other sessions (left as-is)** — the 2026-10-13b entry in `status.md` and `projects/wvulibraries_knapsack/handoffs/2026-10-13-PLANNING-AGENT-CLOSEOUT.md` say HYKU-720 is paused or active. It is in `completed/` (VERIFIED: `git ls-files`, 2026-10-15). The knapsack guidance section "Current Project State (as of 2026-10-12)" is stale for the same reason. Tracy or the planning agent decides.
5. **`wvu_knapsack` repo items (REPORTED)** — the README says `sc logs -f web` and `sc exec web rails console`. The log-mount change is on `feature/hyku-v7.2.0-upgrade`, not on `main`. `scripts/cleanup-dev.sh` was edited by Tracy; its header was not re-read after the final push. The dead `status.md` link in the knapsack `CONTRIBUTING.md` is not yet a task. The CONTRIBUTING commit-message task (`750c2b1`) is still in `backlog/`, with `[FILL IN]` header fields and no dispatch interface, so it is not ready to dispatch.
6. **`needs_review.md` entries still OPEN** — handoff lifecycle, artifact categorization, generic framework/gem upgrade checklist (checklists A and B are knapsack-specific, so this one is not resolved), the `Priority` field missing from the entry template, and the commit-conventions entry until the CONTRIBUTING task runs.
7. **Pending Items appends not done** for `wvulibraries_acda_portal`, `wvulibraries_databases` and `wvulibraries_knapsack`, plus the rest of the 2026-10-11 handoff's "Not done yet" list (README index line for `HUMAN_WORKFLOW_GUIDE.md`; files mentioning `SESSION_CLOSEOUT` without a section number; Rule 21 and Rule 18 decisions; the first real use of stop-early / `resume: true`). Item 3 of that list (section 2 vs 2a) is resolved by `6d0d80f` and `514dcce`.
8. **Upstream and production items (REPORTED)** — `samvera/hyku#3399` is Tracy's approved PR and waits on an adjustment from the Hyku lead; when it merges, remove the `BlacklightAdvancedSearch` shim (`e313f91`; confirm the hashes `ac025ec` and `e313f91` against `git log`). The facet-limiter regression after 7.2.0 (10 items instead of 5 on hykudev) is unresolved. The duplicate-collection-facet task needs Tracy to confirm snapshots of both VMs before any reindex.
9. **`samvera_hyku` folder** — `tasks/current/` and `tasks/active/` both hold `2026-07-10-MEDIUM-BUGFIX-VIEW-SPEC-DEVISE-WARDEN-ISOLATION.md`; three undated files sit in `tasks/active/`; the `status.md` header is stale; `2026-10-07-PLAN-SAMVERA-REORG.md` is a draft, not approved. ChatGPT's review file `2026-10-13-CHATGPT-REVIEW-CATALOG-INDEX-IMAGE-RESOLUTION.md` landed on origin and was not read by this session.

## Found during the session (not changed here unless stated)

- `sc logs` is not a Stack Car command. The knapsack guidance and README said it was. Corrected in the guidance (`56e8731`, `eefacd0`). (VERIFIED: Tracy ran it.)
- On `main`, the base `docker-compose.yml` had no log mount, so under Stack Car there was no `./data/logs`. Tracy added the mounts on the upgrade branch. (REPORTED, plus VERIFIED by the merged `docker compose config` Tracy pasted.)
- `scripts/cleanup-dev.sh` header claimed it does not touch other Docker projects, but its `docker image prune -f` and `docker builder prune -f` steps are machine-wide. Its restart hint named `up.local.sh`, which does not exist. Tracy corrected it on the upgrade branch. (REPORTED.)
- Tracy's laptop has only three knapsack start scripts: `up.sh` (VMs), `up.sc.local.sh` (Stack Car) and `up.prod.local.sh` (paused). (REPORTED.)
- The Stack Car README does not list `sc down`, `sc sh` or `sc pull`, but `sc help` does. The README is incomplete. (VERIFIED.)
- `down.sc.local.sh` runs `sc down` then `sc proxy down`, which also stops the shared proxy. (REPORTED.)
- Guidance gotchas about the VERSION override and `docker rmi` were stale. Corrected in `1d0606f`.

## This session's closeout

- **Scope:** files in the agent-tasks repo only. No code was changed by this agent in `wvu_knapsack` or `samvera/hyku`, and no VM was touched.
- **Maintenance performed:** moved the on-hold backport task into `samvera_hyku`; archived the stale `hyku/status.md`; created `samvera_hyku/SESSION_GUIDANCE.md` and corrected its Stack Car section; added checklists A and B, the VM rule and corrected gotchas to the knapsack guidance; resolved the `SESSION_CLOSEOUT.md` section 2 vs 2a conflict by a project override; wrote three session log entries.
- **Exceptions (left as-is):** the 2026-10-13b entry and the `ea2cccf` planning handoff; the knapsack "Current Project State" section; `samvera_hyku` task-folder duplicates; every file listed under "Not done yet".
- **Closeout note:** the formal closeout, this handoff, was missed on 2026-10-15 and done on 2026-10-18 at Tracy's prompt.
- **Uncommitted at the last check:** none (VERIFIED: `git status -sb` after the push of `eefacd0`, 2026-10-15). This handoff file itself is not committed until Tracy commits it.
- **Stop condition:** none for the committed work. Open items are above; owner Tracy unless noted.

## Workflow topics Tracy may want to discuss (not decided)

- Handoff location (REPORTED by Tracy, 2026-10-18): `projects/{project name}/handoffs/claude-free-web/`, a recently updated format. `SESSION_CLOSEOUT.md` and `WEB_AGENT_SESSION_START.md` may not say this yet (read through a summarising tool, so not certain). The 2026-10-11 handoff sits in `projects/agent-tasks/handoffs/claude-web/`, the old folder; whether to move it is Tracy's call. Still undecided: file naming, and whether a new session writes a new file or updates the previous one.
- A session that spans several days: this one ran from 2026-10-13 to 2026-10-15 with a Session Log entry per day and no handoff. A rule for when to write the handoff would have caught the miss.
- Whether guidance files should avoid repeating the same Stack Car facts across projects. `samvera_hyku` now points to the knapsack file for them.

## Environment notes (REPORTED unless stated)

- Two laptops appeared in terminal prompts: `tracymccormick@Tracys-MacBook-Pro` has the agent-tasks clone; `tam0013@LIB-DCL-TRACYM4` has `wvu_knapsack` at `~/Documents/git/wvu_knapsack`.
- The first laptop drops its Wi-Fi. One `git push` failed with "Could not resolve host" and was retried successfully. Check `git status -sb` for `ahead` after every push.
- `perl -pi -e '... if $. == N'` run as separate commands shifts line numbers after any edit that changes the line count. Edit bottom to top, or use one pass. This caused one bad diff, caught before commit.
- Pasting long lines into the terminal can drop spaces at the wrap point. Verify with `grep -c` before committing.
- `WebFetch` returns summaries, not verbatim files. `raw.githubusercontent.com` works; GitHub directory and branch pages are blocked. Treat results as REPORTED.
- Only the human stages, commits and pushes (GUARDRAILS Rule 26).
