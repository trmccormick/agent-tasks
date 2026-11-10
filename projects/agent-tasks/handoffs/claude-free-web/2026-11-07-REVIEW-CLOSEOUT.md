# Review Closeout — 2026-11-07 (claude-free-web)

**Session**: web review session with Tracy (he/him), 2026-11-07 morning to about 21:45 ET. Claude free web, review role. Claude drafts exact edits; Tracy applies, shows `git diff`, stages, commits (path-limited) and pushes; Claude reads back from origin.
**Evidence labels**: VERIFIED (read back from origin or checked against the file), REPORTED (stated by Tracy), INFERRED, DEFAULT (Claude's unconfirmed proposal).

---

## 1. What landed (all VERIFIED by read-back from origin)

**agent-tasks**
- `e11b222` pronoun fix in the 2026-11-05 closeout; `8fb27e2` GUARDRAILS note that Tracy uses he/him; `733d4b2` NEEDS_REVIEW entry "Workflow is still experimental: lanes, review stages, model roles and where design intent lives".
- `b662972` addendum on the 2026-10-26 portal wiki draft entry in `projects/galaxy_game/NEEDS_REVIEW.md`: `gemini-research-portals.md` is held and is reference only; the blueprint sample carries `"em_consumption_rate_mg_hr": 12.5` at L80 (the draft's factor-of-1,000 EM-rate claim now has file and line); destination for the root files not chosen.

**galaxyGame (`docs/wiki`)**: nine commits since `57b00fa`, origin `main` now `268f4db0`.
- `b4a92b78` `technical/portals_and_aws.md`: Operation bullets, Networks and Hubs, tech tree placement note, open questions.
- `bcc00f19` `player/settlement_progression_and_footholds.md`: portal EM wording now matches the ignition model.
- `858eb18a` `migration-log.md`: row for the five frozen portal pages.
- `2f276959` `technical/natural_wormholes_and_em.md`: Controlled Snap (Hammer Protocol) section.
- `830bf442` portals page: rift stays open until closed; Hammer Protocol pointer; closing and hub-capacity questions.
- `04c867b3` migration log: `docs/architecture/operations/wh-expansion.md` (Hammer Protocol and AWS counterbalance read; rest not reviewed) and a note that the older portal sources are not named in the log.
- `a49aeda1` natural wormholes: planned-Snap announcement, EM harvest window, stockpile model, open questions.
- `b171328d` natural wormholes: EM Yield and EM budget section, two open questions.
- `268f4db0` migration log: `docs/storyline/07_procedural_generation.md` reviewed; EM Yield concept recorded.

## 2. Design statements by Tracy today (REPORTED; on the wiki unless marked)

**Portals** (on `portals_and_aws.md`)
- Both halves are in one system, linked by the data network, which confirms both online before a transit opens; signal lag is expected.
- One endpoint fires and opens the rift, the other locks on and stabilizes it, as in an AWS pair. Either half can fire; only one does for a given opening.
- The firing side uses the most EM, at the opening. Portals also spend EM to reinforce the rift and reset mass limits, as wormhole stabilization does; the stabilization technology is part of the portal. Fusion or electrical power runs the magnetic fields.
- If EM is unavailable the link shuts down; nothing further is defined. The design task is to set how much EM a portal consumes. Portal mass limits were never defined; the aperture is small.
- Once open, the rift stays open until either side closes it (portals and artificial wormholes). Counterbalance rules do not apply to portals (miniaturized). Portals are small, low-mass and generally not space-based.

**Natural wormholes** (on `natural_wormholes_and_em.md`)
- Hammer Protocol: send enough mass through to cause a controlled Snap; this is how a natural wormhole is closed. Wording may need adjusting (Tracy).
- A planned Snap is announced system-wide. The AI Manager announces when stabilization satellites and NPC equipment or ships are due back in Sol. Players may send harvesters (including automated ships) during the harvest window; a harvester not back through before the Snap is lost. The AI Manager may let the wormhole degrade, or let player ships harvest and return; player transits add mass and could themselves cause the Snap.
- A degrading wormhole expels more EM than a stable one. Sol is EM-poor while stable, so its EM comes in bursts around these events and consumers draw on a stockpile. The EM Yield variable from the old storyline notes is possible early design; the 10 to 1,000 EM/hour range was not carried over.

**Latest statement, NOT yet on the wiki** (Tracy, 2026-11-07 21:45): passing mass through a wormhole affects its stability; the counterbalance acts as a natural stabilizing effect; Jupiter is a rare alignment that makes Sol more stable. Without stabilization satellites it would still eject EM and would degrade as matter passes through. Each craft transit would spike the EM ejected. On artificial wormholes the AWS harvests the ejected EM and uses it as stabilization fuel, the same as satellites.
- Already covered on the natural wormholes page: mass anchoring, threshold, ejection, the capture and feed-back loop, AWS as counterbalance.
- New, to add: per-transit EM spike; a stable Sol still ejects some EM and degrades with traffic. Proposed wording (DEFAULT): "Each transit of mass through a natural wormhole causes a spike of ejected EM. Counterbalance slows the degradation, but a stable mouth still ejects some EM and still degrades as mass passes through it." Open question to add: size of the per-transit spike relative to mass.

## 3. Open items

**EM and wormhole design (Tracy's numbers)**: yield curve, EM units, stockpile cap (Saturation Limit), how AWS and portal pairs draw EM (per opening, per hour, per mass, or a mix), portal mass limit, what a controlled Snap leaves behind, who may order one, notice length, who bears a harvester loss, what happens to anything in transit when a link shuts down, hub capacity, whether the network can fail to confirm both ends.
**Wording to look at**: the portals page line "Structural mount must be serious (primary structure / keel-class). Not a light-hull bolt-on." suggests ship or station hulls, while portals are small and generally not space-based. Tracy's call; not touched.
**Task files (agent-tasks, `projects/galaxy_game/tasks/`)**: the two 10-25 drafts (`PORTAL-DESIGN-RECOVERY`, `PORTAL-DESIGN-REVIEW`) are superseded; the 10-26 consolidation task is marked completed but still in `active/`. Lifecycle moves are Tracy's. Draft 2's progress block mentions `tasks/review/task_portal_documentation_review.md` as "to be removed"; not checked whether it exists.
**Root files from commit `65b0fc6`** (`gemini-research-portals.md` and three prompt or review-request files) and the JSON `galaxy_game/app/data/operational/natural_wormhole_anchor_mk1h_data.json`: held, destination not chosen. Candidate folders: `docs/agent/archive/gemni-chats`, `docs/agent/chat-logs`, `docs/archive/chats`.
**Migration log**: all rows say "Kept; awaiting owner approval to archive". Nothing archived. The older sources `docs/architecture/logistics/navigation/INTRA_SYSTEM_PORTALS.md`, `docs/architecture/systems/em_technology_tree.md` and `docs/architecture/logistics/wormhole_system.md` are not named in the log; do not archive the frozen portal pages on today's rows alone.
**Not yet read**: the rest of `wh-expansion.md` (1,375 lines); `docs/storyline/` files `02_crisis_mechanics.md`, `05_physics_topology.md`, `snap_event_and_network_expansion.md` (likely overlap with the Snap and EM material); `load_vetted_system` and the fixed prize values (`magnetic_moment` 0.82, `tei_score` 0.88) need checking against the code before anything goes in the wiki (the code is not in Claude's sparse checkout).
**Legacy conflict logged**: wh-expansion says the AWS "does not create a new wormhole"; the wiki (owner direction 2026-11-05) says one station initiates the rift and the other locks on. The wiki wins.

**Workflow items carried from the morning** (all still open; see NEEDS_REVIEW `733d4b2`): move model facts into `notes/AGENT_NOTES.md` and point other files at it; add a "Choosing a lane" section to `HUMAN_WORKFLOW_GUIDE.md` (and fix its stale items); reconcile `REVIEW_AGENT_GUIDE.md` (`ddadc92`) with the two-stage, reviewers-fix flow (line 7 and line 101 wording); `WEB_AGENT_SESSION_START.md` "only the human commits and pushes"; `PLANNING_AGENT_SESSION_START.md` pairing patterns and Rule 7; whether a directed agent may move a task from `drafts/` to `backlog/`; round cap (three, DEFAULT, unconfirmed); where intent lives for Samvera work; galaxy-game-ui guidance (UI session with ChatGPT and Qwen was active and on hold).
**Unanswered by Tracy**: whether to narrow the Stop hook or set up commit signing (it keeps asking Claude to amend Tracy's commits; Claude declined each time); whether to link GitHub for Claude write access (Settings, Connectors); which Copilot reviewed on 2026-11-05 (INFERRED Office 365).

## 4. Process notes
- Pasted terminal output drops or adds spaces at wrap points ("isan", "collectit", "andartificial"). Prove it with a grep for the spaced form in the staging check; do not call it a defect.
- Use `git --no-pager diff` (the pager captured a garbled diff once). Check the stat against the expected count before staging.
- Staging pattern that worked: one command that checks `git diff --quiet HEAD origin/main -- <file>`, greps new strings with counts, then `git add` and `git diff --cached --stat`; a failed check stages nothing. Then path-limited commit, separate push, read-back.
- Claude web can read the repos by calling `add_repo` (public repos); a write attempt was refused ("link your GitHub account"). Claude's clones were sparse; add folders with `git sparse-checkout add`.
- The session limit pauses the session; it does not end it.

## 5. First thing next session
1. `git pull --ff-only` in both repos; confirm galaxyGame is at or after `268f4db0` and agent-tasks at or after `b662972`.
2. Add the "latest statement" (section 2) to the natural wormholes page: per-transit EM spike and the stable-Sol note, with one open question.
3. Then Tracy picks: the three portal task files, the root-file destination, the storyline files, or the workflow items.
