Session Handoff — Galaxy Game — 2026-07-26 (evening)

## Closed this session (verified, not just asserted)

1. **InfrastructureCostCalculator NEEDS_REVIEW entry** — CLOSED
   Confirmed via grep (zero hits outside spec/test.rb across app/, lib/, config/) that the class has zero live callers anywhere — genuinely dead/untested code. Signature fixed anyway at infrastructure_cost_calculator.rb:152 (`can_produce?(destination, ...)` → `can_produce_locally?(material.chemical_formula)`) for correctness. New low-priority backlog task filed documenting the zero-caller finding and an open keep-vs-remove decision: 2026-07-26-LOW-BUGFIX-INFRASTRUCTURE-COST-CALCULATOR-DEAD-CODE.md.

2. **AI Manager Service Inventory (CRITICAL)** — CLOSED
   All 68 audited services added to the inventory doc (16 MVP / 52 Phase 2, verified). New "Known Incomplete Services" section (6: ConstructionManager, CraterDomeConstructionService, StationConstructionService as full stubs; ProductionService, SimEvaluator, SystemArchitect as partial stubs) and "Utility Calculators" section (5) added. Header updated 93→121 across the inventory doc, architecture doc, and status.md in one pass. Commits: 3c153eda, 976643a. Task file moved active/ → completed/2026-07/. Unblocks NPC Economy Lifecycle and Manufacturing Chain Overview docs (both still sitting in backlog/current/, not started).

## New findings/tasks created this session

**Manufacturing::Service duplicate (dead code, likely)** — filed to backlog/current/, not started.
Surfaced during the inventory audit: `Manufacturing::Service` (manufacturing/service.rb) has zero production callers per grep, duplicates the live `ManufacturingService` (manufacturing_service.rb, called from market_stabilization_service.rb and resource_planner.rb). An older analysis doc, CORE_CONCEPT_MAP.md (docs/wiki_reorganization/analysis/, 2026-07-16), lists it as a "likely owner" of the manufacturing chain, but that doc also carries other confirmed-stale claims and uses hedged language throughout — treated as a weak lead, not authoritative. Two follow-up tasks filed:
- 2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md — git-blame/doc-staleness investigation, no code changes
- 2026-07-26-MEDIUM-RESEARCH-MANUFACTURING-CURRENT-STATE-VS-DOCS.md — trace actual live Luna-MVP manufacturing call chain vs. documented architecture, check for other duplicate/orphaned services beyond this one

**Visual Definition v1.0 for RH-400** — filed to backlog/2026-07/, not started. This is the one to pick up first next session on the asset-pipeline side.
A manual PromptBuilder validation test (informal, not a task file) confirmed the four-layer asset pipeline (Blueprint → Operational Data → Visual Definition → Design System → PromptBuilder) works conceptually, but no unit — including RH-400, the pilot unit — has an instantiated Visual Definition yet. 18 gaps identified (8 critical, 7 moderate, 3 low). Two root causes: (1) no Visual Definition instance exists anywhere, so everything gets inferred from blueprint prose and Design System defaults; (2) the Icon Bible file (`2026-07-19-HIGH-DESIGN-GALAXYGAME_ICON_BIBLE.md`), referenced by name in three canonical docs, does not actually exist in the workspace — needs locating or reconstructing.

Locked design decisions from this session's review (baked into the task file):
- manufacturing_style, technology_level, and literal color values should NOT be raw fields on a unit's Visual Definition — they should be Design-System-derived (a `minimum_technology` reference + semantic color roles like industrial_primary/hazard_warning, resolved centrally)
- Two independently-proposed priority fields (Qwen's "visual_priority", a later review's "Recognition Priority") are the same concept — pick one name for the actual instantiation, don't create both
- A proposed fifth pipeline layer ("Asset Profile" — which assets exist, vs. Visual Definition's "what it looks like") was raised but deliberately NOT adopted — needs its own Architecture/Schema/Design Standard/Implementation classification review before being folded in, not a same-session wave-through

Task file ready to hand off: 2026-07-26-MEDIUM-DATA-VISUAL-DEFINITION-V1-RH400.md

## Standing design question — not yet written into any doc

**Manufacturing origin vs. tech tier, visually.** Discussed but not formalized: split into (1) a small fixed set of "fabrication archetypes" tied to actual available materials/ISRU chains (e.g. advanced aerospace fabrication, regolith-composite ISRU printing, metal-rich asteroid ISRU fabrication) rather than one archetype per planet — this avoids needing a bespoke "Mars look," "Luna look," etc. as the game expands to more worlds — crossed with (2) "tech tier" as a refinement-within-archetype modifier, not a look that overrides archetype.

Supporting visual evidence found this session: a Mk1→Mk4 3D-printed regolith I-beam reference set showing a real refinement gradient at the same construction method (Mk1 rough/porous/near-concrete → Mk4 smooth/dark/near-machined-metal) — a strong candidate as the canonical Design System reference for what `minimum_technology` tier should visually resolve to for the regolith-composite archetype. Not yet formalized into any doc.

Also found: a July 12 file (ASSET_PROMPTS_NEW.md) with Earth-manufactured vs. Lunar-manufactured catalog prompt templates, predating the July 15-19 design-session synthesis — not yet confirmed whether DESIGN_SYSTEM_SUMMARY.md already captures this distinction or whether it's still a gap. Worth a quick grep check next session before assuming it's new material.

Gemini-generated sample renders (I-beam and flat-panel 4-panel sets) reviewed and judged good quality/consistency this session — worth comparing against whichever tool produced the Mk1-4 progression to see if either tool has a natural strength at a particular finish/tier level.

## Recurring pattern — new this session

**Planning/narration loop** (distinct from the earlier 2026-07-22/23 "re-reading same file" stall): an agent repeatedly narrated the same multi-step plan ("this is a massive update, let me...") dozens of times without landing a single edit, when asked to distribute 68 rows across ~14 existing categorized sections (too much simultaneous judgment-call decision-making for one pass). Fix that worked: drop the categorization requirement — append as one new flat section instead of sorting into existing categories, then small separate appends for anything else. Mechanical, low-judgment appends unstuck it immediately. Worth remembering for any future task that asks an agent to both categorize AND edit a large file in one pass — split those into separate steps up front next time, don't wait for the loop to happen first.

## Deliberately deprioritized (carried forward, unchanged)

- Gameplay Loops Overview (HIGH) — still in backlog/current/, not started. mvp_alignment: OTHER, six-loop scope flagged as spawn-more-tasks risk.
- Investigate slow base_satellite_spec.rb runtime — still in backlog/current/, not started, diagnosis-first scope.

## Late addition: reviewed the three Luna simulation rake scripts, DB issue resolved

Tracy shared three rake files reflecting where Luna simulation work has actually progressed to — closer to "run and observe" than "test and assert" than I'd given credit for earlier tonight:

- **`luna_mission.rake`** (`luna:execute`) — the existing pass/fail integration harness. Confirmed last known state was 17/17 passing (corrected in memory from a stale 12/17). Produces a genuinely detailed execution report per task (phase, task file, PASS/FAIL, verification string), not just a bare pass count — e.g. `deploy_lspu: PASS (verified: deployed Surface Prep Unit LSPU (id=138))`. Good diagnostic tool, but structurally a one-shot mission-sequence validator, not an ongoing simulation.

- **`luna_operations_simulation.rake`** (`luna:simulate_operations`) — this is the piece I'd been describing as "still needed" a few turns earlier tonight, except it already exists. Daily-tick loop via `LunaOperationsSimulationService`: tracks inventory over simulated time, logs import decisions with cost-per-kg reasoning, produces a final inventory snapshot per tracked resource. This is the actual "watch the AI Manager stockpile/react to market conditions" observability Tracy was asking for — headless, no Civ4/UI dependency. Worth treating as the primary tool for observing AI Manager economic behavior going forward, rather than building new tooling for that purpose.

- **`lunar_precursor_mission_validation.rake`** — more of a static manifest/schema completeness checker (confirms hardware entries, output fields, phase1/phase2 structure exist and are well-formed) than a live-state simulation. Useful as a pre-flight sanity check, not a source of behavioral output.

**Reframed priority as a result of this**: the missing piece isn't "build a way to observe the Luna simulation" — that exists (`luna:simulate_operations`). The immediate practical next step is just running it against a clean stack and actually reading the output, not building new infrastructure.

**DB issue during this review, now resolved**: While running `luna_mission:execute` live, Postgres logs showed a repeating `FATAL: role "root" does not exist` every 30 seconds. Initially unclear whether this was blocking the rake itself — it wasn't: the same run produced real, verified deploys (Surface Prep Unit LSPU id=138, Comms Equipment Mk1 id=139, foundation_sintered set true), confirming the Rails app's own DB connection was healthy throughout. The noise was a separate, previously-unfixed issue: the postgres healthcheck in `docker-compose.dev.yml` used `CMD-SHELL` with array-syntax arguments, which silently dropped the `-U postgres` flag and fell back to querying as OS-default "root" (not a valid Postgres role). Haiku reset the stack to apply a fix (switched healthcheck to direct `CMD` format, added a proper `ENTRYPOINT` calling `docker-entrypoint.sh` in the Dockerfile) — verified afterward: non-root `rails` container user, zero root-role errors in fresh logs, healthchecks passing silently, clean app connection with postgres:password. This had apparently been assumed already fixed a session or two ago; worth remembering it wasn't, and that it's a distinct bug from the app's own (already-healthy) DB connectivity — don't conflate the two again.

**Next session, concretely**: confirm the reset stack comes back up clean, rerun `luna_mission:execute` to re-establish a known-good baseline (stack reset means the 17/17 result should be re-verified, not assumed to still hold), then run `luna:simulate_operations` and actually read through the tick-by-tick output — that's the direct answer to "show me the AI Manager stockpiling/reacting to the market," no new tooling required.

## Standing reminders (carried forward, still true)

- After any `git mv`, `find <filename>` across the whole tasks/ tree is mandatory.
- Don't trust agent-reported counts/totals at face value — spot-check with the actual command before accepting a total.
- `delegate :method, to: :association` needs checking that the target actually implements the delegated method.
- Ownership: pre-Player, everything is owned by `Organizations::Corporation`.
- Only informal (non-task-template) prose tasks are appropriate for same-session, no-file-state-change diagnostic work (e.g. the manual PromptBuilder validation). Anything producing a new file, a code change, or a new backlog item goes through the real TASK_TEMPLATE.md format.
