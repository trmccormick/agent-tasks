# Review Closeout — 2026-11-14 (claude-free-web), galaxy_game wiki cleanup

**Role**: review (web). Follows `2026-11-12-WEB-REVIEW-WIKI-CLEANUP-CLOSEOUT.md` (same folder).
**Evidence labels**: VERIFIED (read from the repo or file this session), REPORTED (stated by Tracy or another agent), INFERRED.
**Task file**: none. Direct sessions. Backend work remains ON HOLD (REPORTED, Tracy, 2026-11-11). Wiki and UI work continue in parallel; ChatGPT works the UI wiki and design clarifications in a separate stream (voice sessions included).

## Landed this session (all pushed; VERIFIED on origin/main against the expected result)

- Outward-link conversions (README rule 5): `logistics/market_and_ai_bootstrapping.md` (`19fd906`), `logistics/financial_engine.md` (`db39f2e`), `engineering/01_post_snap_reconnection_protocol.md` (`b3da954`), `status-and-gaps.md` (`989dace`, script-based: 5 links to plain text, 2 mis-pathed wiki links repointed with lowercase anchors).
- Banner fixes: `technical/em_numeric_design_questions.md` (`ae1ad8d`, authored as Copilot), `technical/natural_wormholes_and_em.md` (`21418c3`), `technical/precursor_discoveries_and_research_opportunities.md` (`d477ca2`).
- `technical/portals_and_aws.md` (`758721f`): new DIRECTION bullet "Both ends maintain the rift" (owner brainstorm, 2026-11-14; not locked) and "or can no longer maintain it" added to the two open-until-closed sentences. Portals use "side/end", AWS uses "station" (owner point: portals can sit on planet surfaces).
- Reverted ChatGPT's edits to the frozen doc `docs/reference/CELESTIAL_BODY_DATA_CONVENTIONS.md` (`5c9028f`, restored to the 2026-10-11 state; VERIFIED identical to `d7894ca`). Rule: anything outside `docs/wiki` is frozen, to be retired; do not edit it. The substance (resource generation ownership OPEN) lives in `design/starsim_and_protection_classes.md` and `ui/03_admin_and_ai_manager.md`.

## Linter state (VERIFIED, main `989dace`)

21 errors: naming 2 (`status-and-gaps.md`, `migration-log.md`), links 15, anchors 2, banners 2.
- Remaining outward links: `markets/markets_eap_and_peg.md` 3, `player/getting_started.md` 2, `services/ai_manager.md` 2, `migration-log.md` 1, `technical/biology/terraforming_seeds.md` 1.
- README.md has 5 folder-link errors (astronomy/, logistics/, player/, engineering/, technical/ have no README.md): needs Tracy's choice (folder index vs page links).
- Anchors: `player/getting_started.md` (`#development`), `technical/venus_mars_ballistic_corridor.md`.
- Banners: `technical/sphere_models_and_interactions.md` and `technical/terrasim.md` (ChatGPT work in progress; Tracy says new pages without banners are expected while the wiki is under construction; report them as a cleanup list, not problems).
- Scratch linter: Tracy's `scripts/lint_wiki.py` is local and untracked (the corrected version). Tracy to commit it when sessions are quiet.

## Method that works (keep)

- One narrow task per prompt; one Edit call for multi-edit pages if the executor stalls. Executors often announce a plan and stop; a continuation nudge naming the exact Edit calls fixes it. Multi-link pages stalled Qwen three times; one edit per prompt worked.
- For tables of small exact substitutions, a script with asserts (refuses to run on a mismatch) run by Tracy is faster and safer than several executor prompts. Test it first against the linter on a clone of main.
- Verify from the file, not from terminal paste. Soft-wrap drops spaces in pasted output (false alarms at least four times). Decisive checks: `grep -c` for the suspected string, `cat -e`, a byte-for-byte comparison against the expected regex conversion of `git show <parent>:<page>`, or attach the file.
- Expected counts are relative to the executor's own starting count. Executors sometimes give summary reports; require the diff stat, and verify after the push on origin/main.
- Implementation startup: three template files plus `git pull --no-rebase`; no NEEDS_REVIEW and no wiki README read. agent-tasks is at `/Users/tracymccormick/Documents/git/agent-tasks` (outside the executor's workspace; give the full paths).
- Two parallel streams push to the same main (you and ChatGPT); pushes can race ("cannot lock ref"): `git pull --no-rebase` and push again.

## Owner statements recorded (REPORTED, Tracy)

- Roles (planning, research, implementation) are not tied to models; Qwen is the local model currently used; ChatGPT (GitHub read/write, no shell) can act as implementation or planning agent for documentation work.
- Portals are miniaturized wormhole technology; use `natural_wormholes_and_em.md` for decisions where the portal page is silent.
- EM opens the rift; fusion power and magnetic containment keep it active; a small, duration-dependent amount of EM may be needed while open; amounts not locked. Either end failing its part closes the rift (DIRECTION).

## Open items and decisions for Tracy

- **Voice-session pages (ChatGPT, 2026-11-14) to confirm**: `player/first_hour_and_entry_state.md` now says the Sol-Eden connection "has opened to Eden's already-established settlements" (confirm Eden had settlements before the Snap; else DIRECTION), and the line "Sol is active, Eden's post-isolation condition is not yet known to incoming players, and System B is empty" carries the original `(owner, 2026-11-05)` attribution on reworded text. Eden page bullet "portal technology and EM harvesting appear only after AWS research matures; neither is available during the isolation period" is ambiguous (research happens during isolation). "Wormhole Transit Consortium (WTC)" is a new named entity; not checked against code or other pages.
- **Not reviewed**: content of `natural_wormholes_and_em.md` (+121 lines), `game_time_and_simulation_loop.md`, `sol_system_reference.md`, `starsim_and_protection_classes.md`, `migration-log.md`, `sphere_models_and_interactions.md`, UI wiki page bodies, the 7 UI draft tasks in agent-tasks (11-13), and the `galaxy-game-ui` repo (not attached to this session).
- From the earlier audit: Eden anchor-mass threshold (wiki 1.0e15 and 5.0e16 kg; frozen doc 1e16; before or after hollowing); portal draft root-file and JSON placement; archive the 2026-08-30 entry and close the sprite and lava-tube entries; where the blueprint naming rule lives; System B build-up vs "System B is empty"; reconcile `engineering/01_post_snap_reconnection_protocol.md` with the confirmed Eden sequence (the gap widened with the 2026-11-14 updates); authority wording pass; canonical StarSim page; per-row `[PROPOSED / PLACEHOLDER]` simplification.
- Dependabot count on galaxyGap main keeps rising: 97 (11-12) to 117 (11-13) to 119 (11-14; 4 critical, 28 high, 59 moderate, 28 low). Relevant to the Rails 8 baseline entry; not acted on (backend on hold).

## Next action

1. Pull both repos; read `projects/galaxy_game/CHATGPT_TO_CLAUDE.md`; check for new commits touching `markets_eap_and_peg.md`, `getting_started.md`, `services/ai_manager.md`.
2. Re-run the linter on current main, then continue outward links: `markets_eap_and_peg.md` (3), `player/getting_started.md` (2), `services/ai_manager.md` (2), then the 1-link pages. Check each page's links and anchors first; script or one-link prompts.
3. Offer a read-only pass over the voice-session pages and the unread files above when Tracy asks.

## Files left uncommitted

None from Claude in this repo.
