# Session Handoff — Galaxy Game UI Work
**Date:** 2026-11-03  
**Purpose:** Close the current session and resume the Galaxy Game UI prototype in a fresh session.  
**Primary project:** `trmccormick/galaxy-game-ui`  
**Coordination/docs repo:** `trmccormick/agent-tasks`

## 1. Scope for the next session

Resume work on the **Galaxy Game UI prototype**. This is the priority for the next session.

Do not resume the Hyku/Solr incident or development upgrade work in this session. That work has its own existing tasks and is intentionally separate.

The UI prototype is an information-architecture and visual-design laboratory for Galaxy Game. It lives in a separate Rails 8 app based on `wvu_moonshot`; WVU-specific branding and assumptions are being replaced with Galaxy Game views and styling. The prototype must reflect the main game's actual data and documented design intent rather than inventing game systems.

## 2. Required startup reading

Use the current checkout paths; do not assume another machine's absolute paths.

1. Universal startup instructions: `rules/GUARDRAILS.md` and `rules/SESSION_CLOSEOUT.md`.
2. Read `projects/galaxy-game-ui/NEEDS_REVIEW.md`.
3. Read `projects/galaxy-game-ui/SESSION_GUIDANCE.md`.
4. Read the first screen of `projects/galaxy-game-ui/status.md`.
5. Read the latest handoff under `projects/galaxy-game-ui/handoffs/`.
6. Check the live repositories and branches before relying on any state below.

The UI-specific guidance is authoritative for this prototype. In particular:
- UI choices must trace to the live Galaxy Game wiki first; consult deeper docs only when the wiki is silent.
- If intent is missing or conflicting, do not silently invent a decision. Use a clearly marked placeholder and add/update an OPEN item in `NEEDS_REVIEW.md` for Tracy.
- The main game's models and JSON define actual data shapes; the wiki defines design intent.
- Work one task at a time and verify each step in the running app, including a screenshot or pasted output.
- No commit or push without explicit human approval. Stage explicit paths only; never use `git add .` or `git add -A`.
- Fixtures belong under `db/mock_data/`, not a root `data/` directory.
- The UI app's Docker container is `galaxy_game`; `web` is the main game server.

## 3. Known prototype state (verify before acting)

The following is the most recent reported state available at closeout; it has not been re-verified in this session:

- UI repository: `trmccormick/galaxy-game-ui`.
- Working branch: `review/qwen-ui-prototype`.
- A prototype publication report stated that branch was published at commit `8ce1250a`.
- Reported route checks: `/` returned HTTP 200; `/dashboard` returned HTTP 500.
- A report was written locally at `~/scratch/qwen-reports/prototype-publication-report.txt`; this is a machine-local path and may not exist in the next environment.
- `projects/galaxy-game-ui/SESSION_GUIDANCE.md` is already committed on `agent-tasks/main` (commit `1cfcbff`). Do not recreate or revert it.
- T3a (CSS foundation) and T3a-2 (layout shell) are recorded as completed in the UI project guidance/status, but verify the actual branch and files before assuming they remain current.
- T3b (JSON fixture loader) and T3c (dashboard overview) remain in drafts according to the last checked `status.md`; the open review list also contains bookkeeping and sequencing issues.

Start by checking the actual branch, working tree, recent commits, and the current route failure. Do not assume the reported 500 is still present or that its cause is known. Capture the exact error/stack trace and identify the failing route/controller/view/helper before proposing a fix.

## 4. Design and gameplay constraints relevant to UI

These are reported design constraints from the current review queue and must be checked against the live wiki/code when a screen depends on them:

- **No FTL or universal messaging.** Within a system, messages are delayed by light-speed distance. Inter-system communication is store-and-forward through craft and wormhole/anchor stations. Screens must not imply instant interstellar messages.
- **Life support is currently a stub.** `Settlement::LifeSupport#life_support_status` has been reported to return `'operational'` unconditionally. Do not present it as a fully simulated live subsystem.
- **Settlement tiers are not settled/implemented.** Do not hard-code base/outpost/settlement/city labels or population thresholds as established facts.
- **Stations and orbital depots are structures, not settlement types.** Orbital structures may exist without a surface settlement.
- **Cyclers are craft, not settlements.** They travel a long route, accept docked craft, and may temporarily serve as a station during construction; this intent was reported by Tracy and may not yet be in the wiki.
- **Market ownership/association is unresolved beyond the current model.** The last review found `Market::Marketplace` associated with `Settlement::BaseSettlement`, with no market association found for orbital settlements/structures. Do not invent a generalized market model in the UI.
- Existing unresolved display/design placeholders include the accent color, Eden identifier, and wormhole link types. See `NEEDS_REVIEW.md`; do not silently promote placeholder values to canon.

For any screen being worked on, state which established facts, directions, and unresolved decisions it depends on.

## 5. First actions for the next session

1. Complete the required startup reading.
2. Verify the live state of `galaxy-game-ui`: branch, working tree, recent commits, application boot, and current route responses.
3. If `/dashboard` still fails, diagnose the 500 from the actual log/trace first. Do not make speculative view or route changes.
4. Reconcile the immediate UI work against `NEEDS_REVIEW.md` and the existing task drafts. Do not create a duplicate task or start multiple tasks in parallel.
5. Report the verified state and recommend the single next UI step to Tracy before making changes. The next step may be resolving the dashboard failure or selecting the next approved UI task; let the live state and Tracy's direction decide.
6. Keep main-game backend implementation paused. UI and wiki intent come first; do not create backend work as a side effect of prototype work.

## 6. Stop/approval boundaries

- No changes to the main `galaxyGame` backend as part of this UI session unless Tracy explicitly redirects the scope.
- No autonomous commit or push. Show the diff and wait for approval.
- Do not modify preserved first-attempt source in the UI repository's remote `main` or the local git-ignored first-attempt tree. Harvest reusable examples by copying them into the active prototype branch, documenting their source, and verifying them.
- Do not treat a reported status, old task, or previous agent summary as proof of the current checkout state.
- Keep this session focused on UI work; the separate Hyku/Solr tasks remain untouched.

## 7. Relevant project references

- UI session guidance: `projects/galaxy-game-ui/SESSION_GUIDANCE.md`
- UI review queue: `projects/galaxy-game-ui/NEEDS_REVIEW.md`
- UI status: `projects/galaxy-game-ui/status.md`
- UI app repository: https://github.com/trmccormick/galaxy-game-ui
- UI working branch (reported): `review/qwen-ui-prototype`
- Main game wiki: `docs/wiki/` in the `galaxyGame` repository
