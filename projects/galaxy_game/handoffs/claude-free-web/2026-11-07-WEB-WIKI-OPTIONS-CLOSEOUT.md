# Handoff: Claude web session, 2026-11-07 (evening)
Agent folder: `claude-free-web`. Project: galaxy_game. No task file; session scope below.
Session stopped at the usage limit (paused, not finished). Evidence labels: VERIFIED = read or run this session; REPORTED = stated by Tracy or another session; ARITHMETIC = Claude's own calculation, not checked against code.

## Scope
1. Wiki upkeep in galaxyGame (README table row, missing Code Alignment banners).
2. Design discussion: how to convert or use Phobos and Deimos (station, depot, mining outposts) for the first full Sol settlement, as training data for the AI Manager.
3. Eden (AOL-732356) JSON review, filed as a NEEDS_REVIEW note.

## Commits
- agent-tasks `639eaaf`: NEEDS_REVIEW (galaxy_game) Eden JSON entry. VERIFIED on origin/main.
- galaxyGame: none yet.

## Uncommitted (in Tracy's galaxyGame working tree, awaiting "commit")
- `docs/wiki/README.md`: the `technical/` row ended in a literal `\n`, so the `ui/` row did not render. Split into two rows.
- Code Alignment Status banner (`DESIGN INTENT`, Last Audited 2026-11-07, "not audited against code") added after the H1 of: `technical/natural_wormholes_and_em.md`, `technical/portals_and_aws.md`, `infrastructure/modular_orbital_panel_swarms.md`, `logistics/skimmer_and_mixed_volatiles.md`.
- Result (REPORTED by Tracy's terminal output): `git diff --stat` shows 5 files, 14 insertions, 1 deletion; `python3 scripts/lint_wiki.py` went from 69 errors to 65 (banners 0). Remaining 65: links 61 (outward links to frozen docs), naming 2, anchors 2. Naming and anchor errors not yet examined.
- Planned commits: README first, banners second, each path-limited.

## Tracy's decisions and principles (REPORTED, provisional)
- Eden JSON: leave as a NEEDS_REVIEW note; do not change the JSON or the generator now. The copy reviewed was an early generation and may have been tweaked since.
- Placed asteroids in Eden are deliberate: one of the first tasks converts asteroids to stations, and for the wormhole and settled worlds a tug may need to move them. Belt, Kuiper and Oort populations should be defined by belt parameters and density, with individual objects spawned on survey; "unknown until surveyed" is an acceptable value.
- Phobos/Deimos intent is conversion into a depot and station similar in ability to the Earth L1 station and depot. Repositioning is not essential. The plan can change.
- Options considered: (1) no-tug-repositioning conversion in place, the tug or another craft carrying hollowing equipment and removing slag, slag engines possibly added later for station keeping; (2) Luna-style settlement on the moons producing materials; (3) belt bodies delivered by tug for the station and depot, moons as mining outposts; (4) cycler first with harvesting equipment, tug later fills with slag, cycler as temporary staging; (5) belt bodies for Venus using the established pattern.
- A tug would not be sent to Mars if no repositioning or delivery happens there.
- Mission profiles and plans provide only steps and options, fed to the AI Manager as training data. A craft can be fitted, so a recommended fit is a default. The AI Manager must review available information, decide a plan, react to reality and re-plan, and calculate options itself in other solar systems. No embedded values that change with tech level or situation.
- Tasks in `missions_v2/tasks` are options and a small subset of the full set in `missions/tasks_v2`. They are meant to be reused for different deployments (for example comms equipment on any new settlement). Units come from the craft inventory or the manifest; the AI Manager buys or makes missing ones. The AI Manager should verify the manifest in a review step before the mission and load the required items into the craft.
- Current setup is for testing; results get tuned and fed back as training data. AI Manager rework for v2 is future work and out of scope for this work.
- Luna base is built and tested first; Sol deployment is added step by step, extending the rake verification.

## Findings (all ARITHMETIC unless marked; assumptions: slag engine Isp 1,200 s, tug 9 MW, 90 percent efficiency, rock about 1.9 g/cm3)
These are for the wiki as a Sol worked example and for NEEDS_REVIEW. They are NOT to be stored as option fields.
- Slag engine data (uploaded `slag_propulsion_engine_mk1_bp` and `_v1_data`): thrust 2,500 kN at Isp 1,200 s needs about 14.7 GW jet power and 212 kg/s of slag; listed draw is 500 kW. Power-limited thrust is about 85 N. Mass 15,000 kg matches its bill of materials (VERIFIED).
- Tug data (uploaded `asteroid_relocation_tug_bp`, `_v1_2_data`): max asteroid mass 1e10 kg against Phobos about 1.07e16 kg and Deimos about 1.5e15 kg. Processing 3,000 kg/h and slag 1,200 kg/h. Listed units draw 18,000 kW against 9,000 kW declared. `relocation_speed_km_per_day: 50`, `max_economic_missions: 50` against `operational_duration_days: 1825`, and a belt round trip of at least about two years. Bill of materials 2.525e6 kg against empty mass 2.5e6 kg (VERIFIED).
- Moons: in-place cavity of about 5,000 m3 is about 1e7 kg, about 137 days for one tug; a 250,000 m3 hub-class cavity is about 4.7e8 kg, about 18 years for one tug. Repositioning Phobos to areostationary radius is about 0.67 km/s and about 6e14 kg of propellant at Isp 1,200 s, which is megaproject scale (about 13 TW of jet power over a century).
- Mars staging: Earth to Phobos orbit about 4.8 km/s, about 259 days; slag staged at Mars cuts tug launch propellant from about 2.4 to 3.6e6 kg to about 1.3e6 kg. Belt bodies to Mars: about 2.4 to 4.4 km/s, 18 to 31 percent of body mass as propellant; about 1e7 kg takes about 1.1 to 1.4 years.
- Venus: belt body Δv about 10.5 to 14.8 km/s, propellant 59 to 72 percent of body mass; a 9 MW tug moves about 3e6 kg in a year, about 1e7 kg in 2.4 to 3.4 years.
- Phobos station keeping against tidal decay (about 1.8 m per century, from memory of the literature): about 0.7 kN average thrust, about 210 kg/h slag. Not needed on game timescales.
- Anchor threshold (VERIFIED by reading, conflict not resolved): `AI_MANAGER_WORMHOLE_EXPANSION.md` Anchor Law uses 1e16 kg per body; `pre_player_era_logistics_architecture.md` section 4.3 uses 1e15 (planetary conversion) and 5e16 (AWS pattern, ambiguous between per-body and aggregate); `station_construction_options.md` uses 1e15 for snap resilience and "AWS mass class 8.5M kg", which matches the Planetary Staging Hub empty mass, not the AWS blueprint (2.0e6 kg). Whether the threshold counts mass before or after hollowing is not stated. The new wormhole and portal wiki pages state anchoring qualitatively and carry no figures. Per the 2026-11-07 NEEDS_REVIEW entry, quote both sources to Tracy; do not choose.
- Eden JSON (already filed, `639eaaf`): asteroid mass independent of radius (implied densities 52,700 to 119,000,000 kg/m3), periods and temperatures computed for a 1 solar-mass, 1 solar-luminosity star, star radius and luminosity inconsistent.

## Code observations (VERIFIED by reading galaxyGame source; none run; not yet in NEEDS_REVIEW)
- `Asteroid#composition_type` and `typical_rotation_period` return random values on each call and shadow the database column; `ExcavatedCavity.structural_stress_factor` reads the rotation period.
- `system_builder_service.rb` sets `properties['rubble_pile']` only when `SmallMoon` density is below 2.0; nothing reads the flag; Eden asteroid density is in kg/m3, so the unit of SmallMoon density needs checking (data is gitignored; a Qwen check was drafted).
- `AIManager::FootholdPlanner`: scores and the 1e12 kg accessibility threshold are hardcoded; task names in its sequences resolve to library tasks 7/7 for `surface_feature`, 1/5 `orbital_depot`, 0/8 `captured_asteroid`, 1/7 `atmospheric`, 1/4 `subsurface`, 0/1 `hybrid`; no moon-conversion pattern or structural class.
- `lib/tasks/ai_manager_teaching.rake`: `teach:*` loads v1 profiles and manifests, but the construction-manager methods it calls are stubs with canned results defined inside the rake, and learn_* helpers return hardcoded values; `learned_patterns.json` content not seen.
- `lunar_precursor_mission_validation.rake` (uploaded copy) still contains Titan code and hardcodes phase ids absent from `precursor_mission_profile_v1.json` (`titan_delivery`, `venus_delivery`).
- `TaskExecutionEngineV2` builds capabilities from `required_hardware[*].task_affinity` in the manifest; comma-joined affinity strings are appended whole; no step compares plan needs with the manifest or inventory.
- V2 files: `deploy_solar_rig_v2.json` (templates copy) has a stray `s` after the closing brace; effect keys differ (`action` vs `type`); `foundry_establishment_v2.json` hardcodes an environment; `venus_mission_plan_v2.json` references `registry/venus_foundry_v2.json` but the phase file is `foundry_establishment_v2.json`; `puck_conversion_hub_profile.json` has a missing comma (invalid JSON); `psyche_mining_hub_v3_profile.json` has a phase object inside `success_conditions.complete_phases`; `planetary_staging_hub_mk1_data.json` has `//` comments; `luna_base_profile_v2` has `gravity_range [0.1, 0.3]` (g) beside `gravity 1.62` (m/s2); the precursor profile's prerequisite chain sums to about 1,060 days against a stated range of 776 to 876; the Luna precursor profile and the `luna_mission:execute` skip-all-phases drift is recorded in `status.md` and the staged task `2026-09-10-HIGH-ARCHITECTURE-MISSIONS-V2-PHASE-LIBRARY-INTEGRATION.md` is still `status: backlog` (VERIFIED at origin 8fb27e2).
- Tasks referenced by the Psyche and Puck profiles that do not exist in `missions/tasks_v2`: `task_cycler_delivery_v1`, `task_cycler_arrival_v1`, `task_shipyard_shuttle_ops_v1`, `task_deploy_infrastructure`, `task_enable_direct_docking`.

## Corrections to earlier statements
- Earlier this session I suggested the Eden placed asteroids probably should not be in the JSON; Tracy corrected that, they are deliberate.
- My 2026-10-26 portal-draft finding (no 12.5 mg/hr figure) was corrected by another web session (`em_consumption_rate_mg_hr: 12.5` at L80 of `gemini-research-portals.md`).

## Open items and next action
1. Tracy: say "commit" for the two galaxyGame commits above. Owner: Tracy (or directed agent).
2. Qwen prompts drafted this session, results not seen: (a) density units and `rubble_pile` check on SmallMoon data, output to `~/qwen-scratch/density-rubble-check.md`; (b) read-only missions_v2 inventory (phases, profiles, manifests, plans, task_index, guides, phase_registry.json, task existence, rake Titan check), output to `~/qwen-scratch/missions-v2-inventory.md`. Cleanup when done: `rm -r ~/qwen-scratch`. Owner: Tracy to run and upload.
3. Next web session: draft (not create) a NEEDS_REVIEW entry covering the tug and slag-engine data inconsistencies, the anchor-threshold conflict, the missing task refs and the code observations above.
4. Next web session: draft the wiki page for the Mars-moon options, as one finished file for `docs/wiki/engineering/` (check README rules first). Shape agreed with Tracy: applies when (conditions), steps and decision points, roles needed (not unit ids), results as states, and a Sol reference example tagged as gameplay tuning. Include structural class (monolith, fractured, rubble pile; unknown until survey) as the decision point, and the closed option of repositioning with what it would need.
5. Decisions for Tracy: destination for the root research files from `65b0fc6` (candidates `docs/agent/archive/gemni-chats`, `docs/agent/chat-logs`, `docs/archive/chats`); whether tasks name a unit role and let the fit pick the unit; whether the manifest-review step is a generic phase or AI Manager logic (deferred as future work); whether a profile lists several candidate plans or each option is its own profile.
6. The 2026-11-07 workflow entry and the 2026-11-05 capability-matrix entry ask for a Claude workflow-review pass; not started.
7. Wiki lint: examine the 2 naming and 2 anchor errors (`venus_mars_ballistic_corridor.md` anchor `#sol-tri-system-volatile-loop`).
8. Out of scope: AI Manager v2 rework; any change to the Eden JSON or generator.

## Blockers
None. Claude cannot write to the repo (write trial refused, REPORTED 2026-11-07); this file must be saved and committed by Tracy.
