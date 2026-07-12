# Session Handoff — Galaxy Game — 2026-07-10

**Role**: Planning/Strategist (Claude web)
**Previous Sessions**: 2026-07-09 handoff (stale — work completed before this session)
**Next Session Should Focus On**: RSpec baseline confirmation, atmospheric research task dispatch

---

## Summary

Full day multi-agent session. Rake progressed from 12/17 → **17/17 passing** on clean DB.
All 3 phases PASSED. Key engine bugs fixed, multiple JSON task file violations corrected,
blueprint duplicate resolved. Three atmospheric research tasks drafted and one closed.
Venting design reframed with correct operational model.

---

## Completed This Session

| Item | Commit | Notes |
|---|---|---|
| task_execution_engine_v2 GATE 2 + set_unit_state fallback | `f8621212` galaxyGame | unless keyword restored, unit_type fallback added |
| PVE blueprint duplicate resolved | Local only | production/extractors/ copy deleted, resource/ kept |
| task_isru_stockpile_initiation fixes | Local JSON | display names → ids, phantom unit removed |
| task_car_300_charge_cycle fixes | Local JSON | space vs underscore, completion_criteria fixed |
| task_inflatable_tank_deployment fixes | Local JSON | display names → ids |
| task_deploy_volatiles_storage fixes | Local JSON | display names → ids |
| task_print_inflatable_tank_shells fix | Local JSON | removed cryogenic_tank target_type, engine default handles all |
| lunar_precursor_manifest_v2 | Local JSON | inflatable_pressure_tank count 2→3 |
| 2026-06-17 parent research task | agent-tasks | Closure directive sent — confirm M4 completed |
| Atmospheric venting design reframe | agent-tasks summaries/ | New MD: 2026-07-10-DESIGN-AI-MANAGER-CARGO-ROUTING-VENTING.md |
| Skimmer cycler handshake research | In progress M4 | Research task dispatched this session |

---

## Rake State

**17/17 passing** on clean DB (db:seed:replant required between runs).

| Phase | Status |
|---|---|
| power_comms | PASSED |
| isru_deployment | PASSED |
| gas_processing | PASSED |
| robot_logistics | PASSED |

**CRITICAL**: The rake is NOT idempotent. Always run db:seed:replant before
rake to get trustworthy results:
```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=development bundle exec rake db:seed:replant 2>&1 | tail -5'
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=development bundle exec rake luna_mission:execute 2>&1' | grep -E "PASS|FAIL|ERROR"
```

---

## Key Bugs Fixed This Session

### 1. task_execution_engine_v2.rb — Two bugs (commit f8621212)
- **GATE 2**: `unless` keyword was dropped by previous agent when applying LIKE fix,
  turning the gate into an unconditional raise. Restored.
- **set_unit_state_from_effect**: Only looked up units by `name` column using snake_case
  id. Units are stored with display names. Added unit_type fallback lookup.

### 2. JSON task files — display name violations (local only)
All task files that used display names (e.g. "Inflatable Cryogenic Tank") instead of
blueprint ids (e.g. "inflatable_cryo_tank") in effects blocks were corrected.
Convention: backend JSON must always use snake_case blueprint ids, never display names.

### 3. inflatable_cryo_tank vs inflatable_cryogenic_tank
Engine normalizes "Inflatable Cryogenic Tank" → "inflatable_cryogenic_tank" but manifest
seeds inventory as "inflatable_cryo_tank". Fixed by using id directly in task effects.

### 4. print_inflatable_tank_shells target_type
Task specified target_type: "cryogenic_tank" but deployed units are "inflatable_cryo_tank".
Fixed by removing target_type — engine defaults to "inflatable" LIKE query which catches all.

### 5. PVE blueprint duplicate
Two copies of planetary_volatiles_extractor_mk1_bp.json existed. UnitLookupService only
scans blueprints/units/resource/ — kept that copy, deleted production/extractors/ copy.

---

## Volume Mount Path — CONFIRMED

Docker compose maps: `./data/json-data → /home/galaxy_game/app/data`
NOT `/home/galaxy_game/data/json-data` — previous sessions had wrong path assumption.
All container file checks must use `/home/galaxy_game/app/data/...`

---

## Atmospheric Research Tasks — Status

Three research task files in backlog/current/ drafted by Gemini/Qwen:

| Task File | Status | Notes |
|---|---|---|
| 2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-TRANSFER-VENTING-DESIGN.md | CLOSED | Reframed: venting is AI Manager cargo routing decision, not AtmosphericTransferService mode. Design saved to summaries/. |
| 2026-07-10-HIGH-RESEARCH-SKIMMER-CYCLER-HANDSHAKE-REFACTOR.md | In progress M4 | Research dispatched this session — confirm completion |
| 2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-HARVESTER-MOCK-REPLACEMENT.md | Backlog | Not yet dispatched — waiting on Gemini review |

**Venting design — key decisions locked:**
- Venting is a post-processing cargo routing decision made by AI Manager
- Decision is PRICE-DRIVEN via NpcPriceCalculator
- Processing capacity is POWER-CONSTRAINED (solar vs RTG per craft per body)
- Venus: higher solar → more processing, primary output LOX
- Titan: RTG only → limited processing, primary output CH4
- store_if_value_positive in HLT operational data is the existing policy hook
- MVP scope: one dip cycle per dispatch, not continuous simulation

---

## Conventions Confirmed This Session

- **Volume mount**: `./data/json-data → /home/galaxy_game/app/data` (not data/json-data/)
- **JSON task effects**: must use snake_case blueprint ids, never display names
- **Blueprint ids**: must include _mk1 for all initial versions
- **Engine unit naming**: count > 1 deploys as "unit_type 1", "unit_type 2" (space not underscore)
- **Rake is non-idempotent**: always db:seed:replant before running
- **data/json-data/ is gitignored**: Time Machine only, never force-add
- **UnitLookupService scans**: blueprints/units/resource/ for extractors (not production/extractors/)

---

## Next Session Priority Stack

| # | Task | Notes |
|---|---|---|
| 1 | Confirm RSpec baseline | Ryzen running full suite — paste result |
| 2 | Confirm 2026-06-17 task closed | M4 received closure directive — verify completed |
| 3 | Confirm skimmer cycler handshake research | M4 dispatched — review output |
| 4 | Gemini review of remaining 2 atmospheric research tasks | HARVESTER-MOCK-REPLACEMENT not yet dispatched |
| 5 | Dispatch HARVESTER-MOCK-REPLACEMENT | After Gemini confirms task file is final |
| 6 | SurfaceSuitabilityAnalyzer fixes | Still in backlog/current/, 3 RSpec failures tied to it |
| 7 | HLT Harvester Variants V2.1 | backlog/phase5+ — formula fixes landed, connection_schema local |

---

## View Hierarchy — Confirmed This Session

Three views exist or are planned:

| View | File | Status | Analogy |
|---|---|---|---|
| **Monitor** | `monitor_html.erb` | Built — admin dashboard, sphere status, no canvas | Orbital/world overview |
| **Surface View** | `surface_html.erb` | Built — canvas, tileset system, terrain/settlement layers | Civ4/FreeCiv strategic layer |
| **TerrainForge** | TBD | Planned — SimCity grid, exact unit placement, sintered zones | SimCity tactical layer |

**Architectural boundary — important:**
- **Phase structure (phases 1-15)** = AI Manager / simulation / economic milestones
- **UI track (`backlog/ui/`)** = visualization of simulation data — runs in parallel, never blocks phase progress
- TerrainForge tasks already exist in `backlog/ui/` — do NOT move them into phase structure
- `foundation_sintered`, unit deployment, settlement state = simulation data owned by the model layer
- TerrainForge READS that data to render grid placement — it does not own it
- A fully working Phase 15 economy can exist with no TerrainForge at all

**Regolith processing chain — locked this session:**
The full chain is: Harvester Rover → TEU (produces `regolith_processed`) →
PVE (removes oxides, produces `regolith_depleted`) → LSPU (sinters depleted
regolith → sets `foundation_sintered: true`).

For the rake as integration test harness, `foundation_sintered` being set by
the LSPU task is architecturally correct — terrain grid detail lives in
TerrainForge, not in the AI Manager simulation layer.

The empty `(verified: )` on `deploy_regolith_harvester_rover` is worth fixing
next session — task should verify rover was deployed and in correct state.

---

## UI Architecture — Layer Stack (Locked This Session)

Five distinct rendering layers — never conflate in code, assets, or task files:

| Layer | Source | Condition | Notes |
|---|---|---|---|
| Elevation/Geology | GeoTIFF → geosphere.terrain_map | Always | Base, physics-driven |
| Hydrosphere | hydrosphere model | When liquid present | H2O=blue, CH4=orange, NH3=purple — already in surface_view.js |
| Biomes | biosphere.biomes grid | Biosphere active only | Living layer — forests, grasslands etc. |
| Hydrosphere-as-Biome | Both models | Liquid + life present | Earth oceans are biomes; Titan methane seas could develop life |
| Infrastructure/Units | Settlement/unit data | When deployed | Landing pads, pipelines, craft, robots |

**Critical distinctions:**
- Terrain/geology ≠ biomes. Biomes are a LIVING layer, not a surface texture
- Hydrosphere is independent of biomes but can become one if life is present
- public/assets/biomes/ should eventually split into terrain/ (geological
  surface textures) and biomes/ (living layer assets only)
- Atmosphere is a separate overlay — same tile, different planet feel
- Settlement/unit markers not yet wired into Surface View

**Terrain family approach — no planet names in renderer:**
Returns {family, type, variant} — renderer never knows "I'm drawing Mars"
Families: regolith/, dust/, frozen/, volcanic/, temperate/, liquid/
Variants (8-12 per type) selected deterministically by world seed

**Backend wiring still in progress:**
- GeoTIFF → terrain_map ✅
- terrain_map → tile family → assets ⚠️ partially wired
- biosphere.biomes → biome tile assets ⚠️ partially wired
- Atmosphere overlay ❌ not yet implemented
- Settlement/unit positions → Surface View markers ❌ not yet implemented

---

## UI Art Direction — Confirmed This Session

**Three separate art pipelines:**

| Asset Type | Style | Use |
|---|---|---|
| Terrain tiles | 32px pixel art, top-down, readable | Surface View geological layer |
| Catalog renders | Photorealistic 3D, front+back views | Unit/craft catalog view |
| Infrastructure sprites | TBD isometric | TerrainForge grid placement |

**Assets produced so far (ChatGPT):**
- forest.png — 32px pixel art, Earthlike biome tile ✅
- Terrain concept sheet — 8 terrain families, 32x32 ✅
- HLT render (cargo bay open/closed) — catalog view ✅
- TEU Mk1 render (white/clean) — catalog view ✅
- TEU Mk2 render (darker, orange accent piping) — catalog view ✅
- 3d_printed_ibeam_mk1.png — regolith structural component ✅

**Next UI task:** Terrain Kit 001 — regolith, dust, frozen, volcanic
~100 tiles, covers Luna/Mars-type/ice moons/volcanic worlds
Lives in backlog/ui/ — do not mix with phase structure

---

## Agent Status at Session End

| Agent | Machine | Status |
|---|---|---|
| Ryzen Qwen | Ryzen | Running RSpec + status.md commit |
| M4 Qwen | MacBook | Skimmer cycler research + venting design reframe |
| Gemini | — | Reviewing atmospheric research task files |
| Claude | Web | This handoff |

---

## RSpec Baseline

Previous confirmed: 4106 examples, 2 failures (both confirmed flaky).
Ryzen running full suite now — update this section when result comes in.
Expected: same 2 flaky failures, no new failures from engine fix.

---

## Civilization Layer Design Principle — Locked This Session

Infrastructure IS the civilization layer (Civ4 analogy). It operates at
three scales simultaneously, each corresponding to a view:

| Scale | Civ4 Analogy | Galaxy Game | View |
|---|---|---|---|
| Tile | Mine, farm, road | Landing pad, TEU, solar rig, pipeline segment | TerrainForge |
| Regional | City, wonder | Settlement dome network, Worldhouse enclosure, rail network | Surface View |
| Planetary | Global improvement | Full terraforming, atmosphere modification, global ocean | Monitor |

A world itself can be built — Worldhouse → full terraforming progression.
The civilization layer grows across all three scales:
- Early game: tile-level (landing pads, ISRU units, solar rigs)
- Mid game: regional (pressurized corridors, Worldhouse construction)
- Late game: planetary (atmosphere, oceans, global biosphere)

The three views correspond naturally to these scales. Player zooms from
placing a solar farm on a TerrainForge grid square to watching a planet's
atmosphere change color in the Monitor view over decades.

Needs a formal architecture doc:
  docs/architecture/intent/civilization_layer_scale_design.md
Draft this next session (Gemini or Claude planning session).
