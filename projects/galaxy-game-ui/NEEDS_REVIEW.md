# Galaxy Game UI — NEEDS_REVIEW

Short, active list of items that need a second opinion or a human decision. Primary interface for review and planning agents: read this first, then `status.md`.

**Last updated:** 2026-11-03 (entries 1, 2, 5, 8 closed; entries 11-13 added; entry 6 and 7 refreshed. Drafted by a Claude web session as text, saved by Tracy)
**Evidence labels:** VERIFIED = a command or a read of GitHub `main` shows it. REPORTED = stated by a person or agent, not re-checked.

## How this file works
- One entry per item. State: OPEN (with a specific next action and owner) or RESOLVED (with reasoning). Nothing is silently dropped.
- Move RESOLVED entries to the bottom section; prune them once they are in `status.md`.
- Implementation agents add an entry instead of resolving alone when they:
  - touch gitignored or data paths,
  - mark something complete without same-session re-verification,
  - depend on or conflict with a completed task's architecture,
  - find two docs that disagree about the same system, or
  - catch themselves repeating an identical action three or more times.
- Agents paste raw command output in entries, not summaries of it.

---

## OPEN

### 3. Seeds draft contradicts a decision
- **State:** OPEN — owner: Tracy
- **Detail:** the seeds draft's criterion 2 deletes `UserCsvLoader`. Tracy decided to keep it. Login works: the 2026-11-03 screenshots show a signed-in session as `admin@galaxygame.local` (VERIFIED from the images).
- **Next action:** retire the seeds task, or reduce it to "run `db:seed`, confirm login" with the `UserCsvLoader` deletion removed.

### 4. Task lifecycle docs disagree
- **State:** OPEN — owner: planning agent, human decides
- **Detail:** the dispatch block's Step 0 moves `drafts → active`; Rule 12 as summarized in a Haiku log (REPORTED) says `drafts → backlog (human) → active`. The sign-in task went `drafts → backlog` by Tracy's approval (VERIFIED, `f7ea1b5`), the T3a-2 task went `drafts → completed`.
- **Next action:** reconcile in the agent-tasks workflow project (see its `NEEDS_REVIEW.md` entries on the prompt-construction incident, including the 2026-10-30 evaluation); confirm against the actual GUARDRAILS text first.

### 6. Decisions waiting on Tracy
- **State:** OPEN — owner: Tracy
- **Accent color:** the running prototype now uses a bright cyan on headings, borders and buttons (2026-11-03 screenshots, VERIFIED from the images); the actual token value was not checked (`--accent` was `#4aa8ff`, a placeholder). The cyan look comes from the first attempt's direction. It is not yet recorded as a decision. If Tracy confirms it, it goes into the UI design references page of the wiki (per the wiki-intent rule in `SESSION_GUIDANCE.md` section 3) and `--accent` is set to match.
- **Eden identifier** `AOL-732356` and wormhole link types: fixtures use `unknown` until confirmed.
- (T3c routing was a question here; see entry 12.)

### 7. Later tasks (status refreshed 2026-11-03)
- **State:** OPEN — owner: Tracy; Claude web drafts when asked; do not dispatch
- Landing page: shipped without a task (`f07953b` "Port Galaxy Game public landing page", VERIFIED title). Remaining issues are in entry 13.
- Sign-in page: shipped (`2e10b1d`); record not closed, see entry 11.
- Base components port (first-attempt `8ce1250`): not confirmed done; the dashboard has panels, cards and a sidebar, but which pieces were harvested is not recorded.
- Not started or unverified: the passwords pages (still WVU-styled before the sign-in work; current state not checked) and the WVU favicon (seen in the 2026-10-29 tabs; current state not checked).

### 9. Backend work paused; wiki intent that screens may depend on
- **State:** OPEN — owner: Tracy and ChatGPT (UI and gameplay intent); Claude web keeps the wiki wording
- **Decision (REPORTED, Tracy, 2026-11-03):** no new backend code until the screen structure and gameplay intent settle. UI and wiki come first. The stale task files for the main game (`galaxy_game`: 189 in `tasks/backlog`, 328 in `tasks/review`, 224 in `tasks/completed`, counted 2026-11-03) are reconciled with the wiki before any new backend task is created. The wiki holds the design intent; tasks stay thin and link to it.
- **Wiki intent that screens may depend on** (galaxyGame `main`, commit `09bf11f`; the settlement page notes below are in an updated copy awaiting commit as of this entry):
  - `engineering/interstellar_communications.md` — ESTABLISHED INTENT: no FTL. Inside a system, communication is light-speed and delayed by distance. Between systems it is store-and-forward through craft and WS/AWS stations. Urgent messages go by a craft through the wormhole, or by a courier shuttle if no craft is due soon. DIRECTION: reach depends on power. Screens should not assume instant messages.
  - `infrastructure/settlement_infrastructure.md` — the base / outpost / settlement / city tiers are DIRECTION and not implemented, so screens should not hard-code names or population thresholds. ESTABLISHED INTENT: a space station and an orbital depot are structures, not settlement types, and an orbital structure can exist without a settlement (the Earth LEO depot is the example). DIRECTION: the market may need to work per structure for structures that are not physically connected.
  - Cycler (REPORTED, Tracy, 2026-11-03; not yet in the wiki): a cycler is a craft type, not a settlement type. It flies a long route that other craft dock with, and can serve as a temporary station during builds.
- **Code facts that affect screens** (VERIFIED, read of `main` at `09bf11f`): a market (`Market::Marketplace`) belongs to a `Settlement::BaseSettlement`; I found no market association for orbital settlements or orbital structures. `Settlement::LifeSupport#life_support_status` always returns `'operational'` (a stub). The population rule that sets a settlement's type never assigns `station`.
- **Next action:** ChatGPT: when you document a screen, note which of the items above it depends on. Anything that depends on a DIRECTION or OPEN item is marked as such in the screen notes and is not built as settled fact. Tracy decides.

### 10. Wiki spot-check feedback: unsourced numbers, authority wording, status labels
- **State:** OPEN — owner: Tracy decides; the wiki session (Qwen/Gemini) applies
- **Evidence:** web-agent spot check of galaxyGame `main` on 2026-11-03: `docs/wiki/technical/isru_fabrication_chain.md` read in full (`26a4c88`), plus status headers of four other pages (VERIFIED). Small sample: patterns to check, not a verdict on the wiki.
- **Finding 1 (sources):** the per-tier kW and thermal tables in the ISRU page are arithmetically consistent, but the three docs it cites as authority (`docs/architecture/isru/README.md`, `cnt_production.md`, `logistics/SYSTEM_INDUSTRIAL_CHAINS.md`) contain no matching figures, and the banner says the profiles were added in this expansion. Not proven invented (blueprint data was not searched properly). Label them PROPOSED/placeholder or cite a source; UI fixtures and power panels may borrow them.
- **Finding 2 (authority wording):** "If code contradicts these specs, the code is wrong" is on the ISRU page (VERIFIED today) and, per an earlier snapshot to re-check, on `eap_and_ai_manager_escalation.md` and `precursor_mission_deployment.md`. It conflicts with the wiki's own "verify against code" stance and with Tracy's rule that doc/code conflicts are discussed and he decides. Suggested wording: "a mismatch is logged as a gap for the owner to decide".
- **Finding 3 (status labels):** four status formats seen on four pages (frontmatter `status: DESIGN INTENT — owner-confirmed`, `LIVE (design intent + code-verified note…)`, a "short intent page" line, a "Code Alignment Status" banner). UI work needs one vocabulary; entry 9 already uses ESTABLISHED INTENT / DIRECTION.
- **Next action:** Tracy decides whether to pass this to the wiki session; if yes, apply findings 1-3 as small edits, one page at a time.

### 11. Sign-in task: implemented, record not closed, not verified by the web agent
- **State:** OPEN — owner: Tracy confirms; planning agent does the bookkeeping
- **Evidence:** app repo `review/qwen-ui-prototype` has "Update sign-in page for Galaxy Game" (`2e10b1d`) and "updated tailwind.css" (`209d37a`) (VERIFIED titles). The task file is still `tasks/backlog/2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md` with `status: backlog` (VERIFIED 2026-11-03). No screenshot of `/session/new` (signed out) or of a failed sign-in has been reviewed by the web agent; the task's acceptance criteria ask for both.
- **Next action:** Tracy looks at both pages. If fine, the planning agent `git mv`s the task to `tasks/completed/` with a one-line completion note citing `2e10b1d`; if not, list the fixes first.

### 12. T3b and T3c drafts are stale; where did the dashboard data come from?
- **State:** OPEN — owner: Tracy decides
- **Evidence:** T3b (`2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS`) and T3c (`...DASHBOARD-OVERVIEW-FROM-FIXTURES`) are still `status: drafts`, and `status.md` still lists T3b as the next task. The "Command Center" dashboard exists with mock data and a "mock data for demonstration purposes only" footer (2026-11-03 screenshots), so it was built without them. Whether its data comes from a JSON fixture loader or from hard-coded or helper data is not recorded.
- **Next action:** ask the implementing agent how the dashboard gets its data. Then Tracy decides: retire both drafts, or rewrite them against the wiki-recovery task and entry 9. The fixture rule in `docs/gameplay/sol_core_buildup.md` (label the mock state; do not present post-Snap wormhole/admin screens as the default first login) applies either way. Update `status.md` "Next task" to match.

### 13. Prototype screen findings (2026-11-03 screenshots; each is a small separate change)
- **State:** OPEN — owner: Tracy decides which to fix
- **Currency name (VERIFIED):** the dashboard says "GCC (Galactic Credits)"; `docs/architecture/intent/PLAYER_UI_VISION.md` line 5 says "Galactic Crypto Currency".
- **Landing page (VERIFIED from the images):** while signed in as admin it still shows "Sign In" and "Create Account" in the hero, plus a "Create Account / Watch Demo" call to action at the bottom. Its footer says "Galaxy Game UI prototype" without the "mock data" wording the dashboard footer has, and it shows invented figures ("147 decisions today", "12%") and wormhole hubs.
- **Two header styles:** the landing page uses the T3a-2 header (gold brand, no logo); the dashboard uses a logo, a cyan brand and a sidebar.
- **Dashboard details:** the stat card says 8 settlements but the table lists 3; the bottom Navigation card repeats the sidebar; the "Sign out" button looks clipped at the right edge (may be the screenshot crop).


### 14. Misplaced historical review file in active drafts folder
- **State:** OPEN — owner: Claude (housekeeping); Tracy approves any destructive cleanup
- **Detail:** `tasks/drafts/NEEDS_REVIEW.md` is an October 23 first-attempt review artifact, not a current draft task. The active review tracker is this project-level `NEEDS_REVIEW.md`. The first-attempt archive already has its own `NEEDS_REVIEW.md`, so the historical drafts copy must not be moved over or overwrite it.
- **Next action:** Claude compares the drafts copy with the archived review material, identifies an appropriate historical archive location/name if it is not already preserved, and proposes the exact move/removal. Keep the active drafts folder limited to current draft tasks and its README. Do not delete or overwrite anything until the archival destination and duplicate status are confirmed.

### 15. Player entry state, wiki data rule, and parallel wiki/UI work (for the admin dashboard and first-hour screens)
- **State:** OPEN — owner: Tracy and ChatGPT (UI and gameplay intent); Claude web keeps the wiki wording
- **Working arrangement (REPORTED, Tracy, 2026-11-05):** the wiki and the UI are being worked on at the same time, and the admin UI dashboard starts today. UI notes land in this file. UI sessions write only under `ui/` in the wiki and in this project folder; wiki problems found while building go here for the wiki sessions to fix.
- **Wiki rule that applies to every screen** (VERIFIED, read of galaxyGame `main` at `3cafb88`, `docs/wiki/ui/README.md`, "UI Data Sourcing Rule"): numbers shown in the runtime UI come from live code (`app/` models and services) or the `operational_data` JSON. Wiki placeholder tables and anything tagged `[PROPOSED / PLACEHOLDER]` are not data sources. Where no source exists, show an empty state and log the gap here. Do not copy the wiki number.
- **What players meet at entry** (REPORTED, Tracy, 2026-11-04; now in the wiki as `player/first_hour_and_entry_state.md`, commit `262cc9bb`):
  - Players arrive once AWS technology is online. The first players pick an active location, not the unknown old Eden stations.
  - A player starts at a foothold or station run by the AI Manager through NPC corporations, with basic equipment that depends on landing on Luna, Mars or a station. The starts are not exclusive; a station player can portal to a surface settlement.
  - A player on a base has a dashboard to manage assets (robotic harvesters, deployed ships), track things, and manage market listings.
  - Data lag applies between systems, so screens that show remote data should show its age.
  - Still OPEN in the wiki: what equipment each start gives, which locations count as active, how a player chooses a start, and what the first-hour dashboard shows.
- **Not a screen to design:** `infrastructure/surface_habitat_options.md` records a thought exercise (a house in a pressurized dome). Tracy said it is not a planned structure or a player option.
- **Draft status:** the pages added on 2026-11-04 (`logistics/ai_manager_economic_loops.md`, `infrastructure/power_options.md`, `technical/portals_and_aws.md`, `infrastructure/terraforming_options.md`, `player/settlement_progression_and_footholds.md`, `player/eden_disconnect_and_emergent_history.md`) are unverified drafts from a brainstorm. Only points marked ESTABLISHED INTENT were stated by Tracy. Do not build a screen on a DIRECTION or OPEN item without marking it as such.
- **Next action:** ChatGPT: when you document the admin dashboard or a first-hour screen, list which of the items above it depends on and mark any that rest on DIRECTION or OPEN.

---

## RESOLVED

- **1. T3a bookkeeping** — closed by agent-tasks `ccd9ca7` (VERIFIED): T3a moved to `tasks/completed/2026-10/`, T3c `status: drafts`, `status.md` refreshed. T3a code is `b1eb0d3`.
- **2. Two lessons for T3a-2** — closed: T3a-2 was implemented as `c01b23d` (one file changed, VERIFIED) and approved by Tracy 2026-10-30; both lessons are in its task file.
- **5. Possible uncommitted `status.md` edits** — closed: Tracy's `git status --short` on 2026-11-03 showed no such edits. Housekeeping left: delete the scratch folder with `rm -r ~/scratch/qwen-reports`.
- **8. Commit authorship** — closed: commits now carry an `Agent: <name>` line (`c01b23d`, `ccd9ca7`, `e223b62`, `5cdc4ea`, `48a7e0b`, VERIFIED).
