# Review Closeout — 2026-11-12 (claude-free-web)

**Session**: web review session with Tracy (he/him) on the Intel 2018 MacBook, evening of 2026-11-11 into 2026-11-12 (pushes are timestamped 2026-11-11 local). Claude free web, review role. Claude drafts exact edits or Qwen prompts; Tracy (or Qwen, directed) applies, shows `git diff`, stages, commits path-limited and pushes; Claude reads back from origin.
**Evidence labels**: VERIFIED (read from origin or the file this session), REPORTED (stated by Tracy or another agent), INFERRED, DEFAULT (Claude's unconfirmed proposal).
**This machine**: only agent-tasks and galaxyGame wiki work. `galaxy-game-ui` is not cloned here yet.

---

## 1. Direction (REPORTED, Tracy, 2026-11-11)

- All galaxy_game **backend work is on hold**. Focus is `galaxy-game-ui` (views being ported for cleanup and prototyping) plus the main galaxyGame wiki and the UI wiki.
- UI work is moving to the separate `galaxy-game-ui` repo (initial plan: Rails 8 and JavaScript).
- Proposal, **not adopted**, for Tracy's review: upgrade the main app to Rails 8 and reduce it to running the universe and serving the game API, so other ways of interacting with the game can be built later. Tracy: "seems a better separation".
- If it proceeds, **identity comes from the main application, at least for now** (no identity authority exists yet; the game is in development). The UI's own sign-in (`UserCsvLoader`, seeded admin) is prototype scaffolding.
- The UI work determines what the main app needs to expose. There is no API today.

## 2. What landed (all VERIFIED by read-back from origin)

**agent-tasks**, in order:
- `f92aa87` moved `tasks/drafts/NEEDS_REVIEW.md` (a distinct 151-line first-attempt reuse analysis, 100% rename) to `projects/galaxy-game-ui-first-attempt/summaries/2026-10-23-FIRST-ATTEMPT-REVIEW-AND-REUSE-ANALYSIS.md`.
- `26f8f92` closed UI NEEDS_REVIEW entry 14 into RESOLVED; addendum on entry 11 (`review/qwen-ui-prototype` no longer exists on the remote; its commits are on `main`).
- `6f285c6` UI NEEDS_REVIEW addenda on entry 15 (ChatGPT: per screen note data needed, freshness, read or change state) and entry 16 (web review of the proposal, section 3). Landed via another agent.
- `dcc9675` Rails 8 direction-note addendum in `projects/galaxy_game/NEEDS_REVIEW.md` (other agent).
- `981cb33` prompt-tuning addendum in `projects/agent-tasks/NEEDS_REVIEW.md` (not Claude's).
- `1f6dd59` ON HOLD addenda (5 standard) on the can_harvest, regex, magnetosphere, 2026-11-09 game-clock and 2026-11-10 Venus HLT entries, plus a "no longer in flight" addendum on the 2026-08-30 live-loop entry (its task is in `tasks/completed/2026-08/`, `status: completed`). Qwen wrote it; Claude checked the diff (10 insertions, 0 deletions).

Not held (Tracy's call): the two 2026-07-31 entries, 2026-08-01 naming, 2026-11-06 Eden.

## 3. Findings

**Old handoff (2026-11-07) was partly stale.** Preconditions held (galaxyGame `268f4db0` is an ancestor of HEAD `5c22617`). Its "first thing" item, the per-transit EM spike note on `natural_wormholes_and_em.md`, was not on the page when checked. Tracy later said another agent addressed it; **not re-verified**. A second same-day handoff exists: `2026-11-07-WEB-WIKI-OPTIONS-CLOSEOUT.md` (Mars-moon options).

**`status.md` is stale**: header and snapshot say 2026-10-26; last commit touching it was 11-05. Recursive counts now: active 2, `backlog/current` 33, `drafts` 21 (the snapshot says 1, 1, 14; counting method may differ).

**galaxy-game-ui repo (GitHub state, fetched this session)**
- `main` is the default branch, tip `229b97e` (2026-11-11), the newest commit. Last app-code commit was 2026-11-07 (`b9a777c`, `3d7d573`); everything since is docs.
- `archive/first-attempt-mockdata-dashboard` tip `67a1168` (2026-10-21) is an ancestor of `main`: 0 unique commits, 39 behind. `9f64cd3` (10-23) removed the first-attempt code from `main`. Nothing is stranded.
- `docs/initial-ui-wiki` (26 commits not on `main`): of the 18 files it added, 13 are byte-identical on `main`; three more were identical at the reconciliation commit `d05ac94` (later `main` edits only); the four `9647955` fixes are on `main`. The only content not on `main` is the old `docs/wiki/README.md` §4 (app baseline snapshot of 2026-11-08), §5 (a plan to port the lookup services and read the game JSON, now superseded), §8 (open-decisions table) and change history, plus the root `README.md`. Not line-checked for anything unique in those two READMEs beyond headings and links.

**Main app review for the Rails 8 / API proposal (galaxyGame `5c22617`, static read, nothing run)**
- No game API: only `api/assets#get_asset` (a file server). 16 of 28 controller files use `render json`, `format.json` or `respond_to` for the existing views.
- No authentication or authorization: empty `ApplicationController`, no Devise (sign-in routes are placeholders), no CORS gem.
- Open state-changing routes: `game/jump_time`, `set_speed`, `fast_forward`, `toggle_running`, the simulation-run routes. The "players cannot touch shared universe time" rule is not enforced anywhere.
- REPORTED in the 2026-11-09 entry (not re-read): `game#index`, `get_time` and `state` call `update_time!`; API reads must be pure reads.
- Rails 7.0.8.7. Gemfile and Dockerfile say Ruby 3.4.3, `.ruby-version` says 3.2.5. Gemfile carries `bigdecimal`, `mutex_m`, `drb` and `csv` shims. Last recorded full-suite run (2026-09-30): 4,764 examples, 143 failures, marked stale. As far as Claude knows 7.0 is past security support; check the Rails maintenance policy.
- 14 Admin controllers are the operator tools for the pre-player buildout.
- Minor: the asset endpoint's path check uses `start_with?` without a trailing separator.

## 4. Open items

**For Tracy to decide**
- Adopt the API direction or not. If yes: version from day one (`/api/v1`)? Admin and operator tools move to the new UI before main-app views are retired? Session vs token auth can wait until the main app has an auth layer.
- Authority wording across the docs: ISRU page says "if code contradicts these specs, the code is wrong"; Tracy's rule is that doc/code conflicts are discussed and he decides; a ChatGPT closeout says "code wins; document conflicts". Needs one wording (UI NEEDS_REVIEW entry 10).
- UI NEEDS_REVIEW decisions: entry 3 (seeds task), 6 (cyan accent), 11 and 12 (close sign-in record, retire T3b/T3c drafts), 13 (screen findings from 2026-11-03 screenshots), 4 (task lifecycle docs disagree).
- Whether to hold the four galaxy_game entries listed in section 2.

**Next jobs**
1. **`status.md` rewrite** (not started): header and Current Snapshot with verified counts and the pivot; move stale in-flight blocks and session-log lines out. Qwen prompt, exact text; clean session with STEP 0. The `SESSION_CLOSEOUT.md` §1b format for a status entry was **not read** this session, so no `status.md` entry is drafted here.
2. **galaxy-game-ui branch cleanup**, when the repo is on a machine Tracy can push from (nothing deleted yet). DEFAULT proposal: archive then delete.
   ```
   git fetch origin --prune
   git push origin origin/docs/initial-ui-wiki:refs/heads/archive/initial-ui-wiki
   git push origin --delete docs/initial-ui-wiki
   git branch -D docs/initial-ui-wiki
   ```
   Alternative: `git merge -s ours` to join histories without content. Optional: turn old §4 into a small baseline page.
3. **Main wiki, held**: DIRECTION paragraph for `docs/wiki/ui/03_admin_and_ai_manager.md` §9 (currently "PROPOSAL — NOT YET ADOPTED"). DEFAULT text, do not paste until Tracy decides: "**DIRECTION (owner, 2026-11-11, REPORTED; not yet adopted):** The preferred separation is for the main application to be upgraded to Rails 8 and to run the universe (simulation, game rules and persistence) and serve the game API, with presentation handled by separate applications. A separate-application boundary also lets other ways of interacting with the game be built later against the same API, not only the current UI prototype. The UI prototype's initial plan is a Rails 8 application with JavaScript. The Rails 8 upgrade, the API and any retirement of main-app views are unscheduled, and backend work is on hold." Follow-up if adopted: reword the "UI Data Sourcing Rule" in `ui/README.md`.
4. **Main wiki, carried from 2026-11-07**: portals page line 46 ("keel-class… not a light-hull bolt-on") conflicts with the 2026-11-11 owner statement that portals are low-mass and cannot transfer ships (Tracy's call); migration-log rows (all "awaiting owner approval to archive", older portal sources unnamed); four held root files from `65b0fc6` plus the anchor JSON (destination not chosen); per-transit EM spike (verify first); `wh-expansion.md` and the three storyline files unread.
5. **Not verified or not read**: the 2026-11-07 Mars-moon page (whether `station_construction_options.md` is it) and the NEEDS_REVIEW entry for tug/slag inconsistencies (not in the current file; archive not checked); UI wiki commits of 2026-11-11 (`docs/wiki/ui`); the 2026-11-10 ChatGPT handoffs were read for status only; the uploaded `WEB_AGENT_SESSION_START.md` was not diffed against the repo copy.
6. Workflow items from the 2026-11-07 closeout remain open (see `projects/agent-tasks/NEEDS_REVIEW.md`; that file is about 1,000 lines and well past "should stay small").

## 5. Process notes

- Fresh Qwen sessions need STEP 0: `rules/GUARDRAILS.md`, then `projects/<project>/README.md`, then `SESSION_GUIDANCE.md` (`DIRECT_SESSION_TEMPLATE.md`). GUARDRAILS is at `rules/GUARDRAILS.md`, not the repo root.
- Qwen pattern that worked: exact-text prompt, diff written to `~/scratch/<name>.diff`, Qwen does not stage or commit, Claude checks the diff, then path-limited commit and push, then `rm` the scratch file.
- A push was rejected once because another agent pushed first. Fix was `git pull --rebase`, then `git push`. Never force.
- Count expected insertions from the actual file: entries ending in two blank lines add one line, not two.
- Claude errors this session, corrected: treated the Rails 8 proposal as decided (it is a proposal for Tracy's review); expected 12 insertions (it was 10); said 13 Admin controllers (it is 14).

## 6. First thing next session

1. `git pull --ff-only` in both repos; confirm agent-tasks is at or after `1f6dd59` and galaxyGame at or after `5c22617`.
2. Check whether the per-transit EM spike is now on `natural_wormholes_and_em.md`.
3. Then Tracy picks: `status.md` rewrite, the galaxy-game-ui branch cleanup (needs the repo), the §9 decision, or the wiki wording items.
