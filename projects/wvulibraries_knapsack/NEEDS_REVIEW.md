# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Qwen: add an entry here instead of just noting something in status.md when
an escalation trigger below fires. Remove/archive entries once resolved —
this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- Any file operation touches a gitignored or data path (wrong location,
  git add -f, git mv on an untracked file, editing .gitignore) — always
  needs a second check (GUARDRAILS Rule 28)
- A task is marked "complete" but the completion claim was not independently
  re-verified in the SAME session (e.g. you fixed something and inferred it
  works, but didn't re-run the actual test/rake/grep after the fix)
- A task's fix touches a system another already-completed task built
  assumptions on top of (cross-task dependency risk)
- Two research/design documents disagree about the same system
- You catch yourself repeating an identical action/output 3+ times without
  progress — stop, write an entry here, don't keep retrying

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-10-18 — projects/wvulibraries_knapsack/SESSION_GUIDANCE.md: laptop checks for the M4 (2026-10-19)
**What happened**: SESSION_GUIDANCE.md has three **[CONFIRM]** flags (VERIFIED: `grep -n -E 'CONFIRM|FILL IN'` run by Tracy on 2026-10-18; line numbers are at commit `b171acb` and will shift after edits): the rspec command in Local Development Rules "Tests" (line 92), the `ac025ec` / `e313f91` hashes in the upgrade checklist (line 251), and the description of `hykudev.lib.wvu.edu` and `demo-hykudev.lib.wvu.edu` (line 276). Three more statements need a running stack to confirm but carry no flag: the "Logs" wording (line 87), Startup Check 3's `docker ps` grep (line 44), and a console command that the guidance does not mention yet.
**What I already checked**:
- The grep above found no mention of `hyrax-webapp/log`, `sc console` or `rails console` in the file (VERIFIED, Tracy's paste, 2026-10-18).
- `sc logs` is not a Stack Car command, and `sc exec` runs in the `web` service (VERIFIED by `sc help`, 2026-10-15, as recorded in the 2026-10-15 handoff).
- Public GitHub, read through a web fetch tool on 2026-10-18 (REPORTED; the same tool returned a stale copy of one file that day, so treat it with care): `ac025ec` deletes both VERSION override initializers and also adds `/ingest/` and `/samvera/` to `.gitignore` and a one-line `.lesshst` file. `e313f91` adds `config/initializers/blacklight_advanced_search_shim.rb`. On `feature/hyku-v7.2.0-upgrade`, `docker-compose.yml` mounts `./data/logs/rails` at `/app/samvera/hyrax-webapp/log` and `./data/logs/solr` at `/var/solr/logs`; `main` has neither mount. Solr is set to `platform: linux/amd64`.
- Not checkable without a running stack: container names, the rspec run, the console, `ls hyrax-webapp/log`.
**What needs a second opinion**: Run the checks below in `wvu_knapsack` on the M4 (repeat on the 2018 Intel MacBook if it is used). Record `hostname` and `git branch --show-current` next to every result.

A. Logs: `ls data/logs data/logs/rails data/logs/solr` and `ls hyrax-webapp/log`. Expected on the feature branch (REPORTED, from the compose file above): Rails logs on the host in `./data/logs/rails`, Solr logs in `./data/logs/solr`. The log mounts exist only on `feature/hyku-v7.2.0-upgrade`, not `main`. Solr is `linux/amd64`, so it runs emulated on the M4 and natively on Intel.

B. Container names: bring the stack up with `sh up.sc.local.sh`, then `docker ps --format '{{.Names}}\t{{.Status}}'` and `docker compose ls`. Record the names. Does Startup Check 3's grep `wvu|knapsack|hyku` match them?

C. Tests: pick one small existing spec (`ls spec`) and run `sc exec bundle exec rspec spec/<path>`. Record the exact command and result. Do not add `web` after `sc exec`. Clears the flag at line 92.

D. Console: run `sc help` and record whether a console command exists. Then try `sc console` and record what works. The knapsack README says `sc exec web rails console`; whether that runs `docker compose exec web web ...` is an inference from `sc help`, not run.

E. Never run `docker compose down -v` or `scripts/cleanup-dev.sh`. Stop the stack with `sh down.sc.local.sh`.

F. Report only, no changes: is `.lesshst` tracked on main (`git ls-files .lesshst`)? It was added in commit `ac025ec` (REPORTED).

G. Hashes: `git branch --contains ac025ec` and `git branch --contains e313f91`. Narrows the flag at line 251, which asks which branch they are on.

H. Tracy only, no laptop (no agent has VM access): is `demo-hykudev.lib.wvu.edu` a tenant on the hykudev VM and not a separate VM? Clears the flag at line 276.

I. Submodule pin: `git submodule status hyrax-webapp` and `grep -n VERSION hyrax-webapp/config/initializers/version.rb`. Record the commit hash and the VERSION value. Tracy reports the upgrade branch was cut from main with the submodule updated, and that branch runs on hykudev (REPORTED, 2026-10-18). Public samvera/hyku 7.2-stable reads 7.2.0 (REPORTED, 2026-10-18), but #3399 was closed unmerged on 2026-10-15, so this pin may predate whatever put 7.2.0 there. This shows whether the build on hykudev has the 7.2.0 constant, which the notes assumed #3399 would supply.

Results go back to the web review agent, who drafts the SESSION_GUIDANCE.md edits one at a time (guard check, diff, then Tracy stages and commits).
**Status**: OPEN — owner: Tracy (M4 laptop; the Intel MacBook if used)
