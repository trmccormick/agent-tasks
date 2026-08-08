# RESEARCH: Post-Luna Mission Profile Inventory

**Date:** 2026-08-08
**Type:** Research inventory (no new design)
**Scope:** Cross-reference existing mission content against 7 post-Luna macro build stages
**Source:** `/Users/tam0013/Documents/git/galaxyGame/data/json-data/missions/` (active) + old-code historical reference

---

## Executive Summary

| Build Stage | Found? | Maturity | Key Locations |
|---|---|---|---|
| **L1 Depot** | Partial | manifest-only | `l1_station_depot_manifest_v1.json`, `leo_depot_construction_manifest_v1.json` |
| **Mars** | Full | profile+manifest+15 phases | `mars_settlement/` |
| **Phobos/Deimos** | Partial (thought experiment) | profile+manifest+phase (exploratory) | `super-mars-relocation/` |
| **Asteroid Belt** | Full | profile+manifest+phases (v1, v2, v3) | `psyche_mining_hub/`, `psyche_mining_hub_v2/`, `psyche_mining_hub_v3/`, `ceres_settlement/` |
| **Venus Station** | Full | 10 profiles+5 manifests+19 phases | `venus_settlement/` |
| **Cycler Network** | Fragmented | profile+phase (scattered) | `profiles/earth_mars_venus_cycler_route_establishment_profile_v1.json`, `phases/cycler_coordination_setup_v1.json`, `tasks_v2/task_cycler_*.json` |
| **Psyche** | Full | profile+manifest+phases (v1, v2, v3) | `psyche_mining_hub/`, `psyche_mining_hub_v2/`, `psyche_mining_hub_v3/` |

---

## In-Scope Build Stage Details

### 1. L1 Depot — PARTIAL (manifest-only, no profile/phases)

**Files found:**
- `l1_station_depot_manifest_v1.json` (86 lines) — Earth-Moon L1 Lagrange point logistics hub and cycler coordination capabilities
- `leo_depot_construction_manifest_v1.json` (78 lines) — Low Earth Orbit fuel depot construction using L1 depot pattern

**Maturity:** manifest-only. No standalone mission profile or phase files exist for L1/LEO depot as a build stage. The L1 depot manifest references cycler coordination but is primarily a craft manifest, not a full mission design.

**Gap:** This is the most under-designed stage in the current inventory. The macro build order places L1 immediately after Luna, but no dedicated profile or phases exist yet.

---

### 2. Mars — FULL (profile+manifest+15 phases)

**Files found in `mars_settlement/`:**
- **Profile:** `mars_orbital_establishment_profile_v1.json` (61 lines) — "Mars Orbital Establishment - Moons Conversion & Infrastructure"
- **Manifest:** `mars_orbital_establishment_manifest_v1.json` (115 lines)
- **Phases (15):** `phases/mars_genesis_phase0_*` through `phases/mars_genesis_phase8_great_warming/` plus `mars_cnt_foundry_establishment.json`, `mars_moons_resource_analysis.md`

**Maturity:** Full. Profile + manifest + 15 phases covering Deimos fuel depot, Phobos manufacturing, organic assessment, surface outposts through terraforming (great warming).

**Note:** This is the most complete Mars design in the inventory. Covers orbital establishment, moon conversion, and full terraforming pipeline.

---

### 3. Phobos/Deimos — EXPLORATORY (thought experiment, not production-ready)

**Files found in `super-mars-relocation/`:**
- `relocation_ac_b1_profile_v1.json` — Profile
- `manifest_v1.1.json` — Manifest
- `phases_v1.json` — Phases

**Maturity:** profile+manifest+phase structure exists, but this is a **thought experiment**: AI Manager settling a Mars-type planet with no Earth/Venus analogs, no moons. Not real design content ready for reuse. Classify as exploratory/training material.

**Files found in `miranda-mining-v2/`:**
- `miranda_mining_profile_v2.json` — Profile (Uranus moon)
- 9 task files (`miranda_*_tasks_v2.json`) — Tasks only, no manifest/phases
- `README.md`

**Maturity:** profile+tasks. Miranda is a Uranus moon mission, not directly tied to Phobos/Deimos build stage. The v1 version exists in `miranda-mining/` (1 file: `miranda_mining_profile_v1.json`).

---

### 4. Asteroid Belt — FULL (multiple versions)

**Ceres Settlement (`ceres_settlement/`):**
- Profile: `profiles/ceres_mars_belt_operations_hub_profile_v1.json`
- Manifest: `manifest/ceres_settlement_manifest_v1.json`
- Phases: `phases/ceres_phase1_orbital_establishment.json` (1 phase)
- **Maturity:** profile+manifest+1 phase (minimal)

**Psyche Mining Hub v1 (`psyche_mining_hub/`):**
- Profile: `profiles/psyche_mining_hub_profile_v1.json`
- Manifest: `manifest/psyche_mining_hub_manifest_v1.json`
- Phases (4): `psyche_orbital_establishment_phase_v1.json`, `psyche_surface_mining_phase_v1.json`, `psyche_industrial_refining_phase_v1.json`, `psyche_relocation_phase_v2.json`
- **Maturity:** Full (profile+manifest+4 phases)

**Psyche Mining Hub v2 (`psyche_mining_hub_v2/`):**
- Profiles (4): `psyche_hub_v2_orchestrator.json`, `psyche_mining_hub_v2_profile_v1.2.json`, `psyche_mining_hub_v2_v2.0.json`, `psyche_mining_hub_v2_v2.1.json`
- Manifests (2): `psyche_v2_manifest.json`, `standard_npc_base_kit.json`
- Phases (1): `psyche_relocation_phase_v2.json`
- **Maturity:** profile+manifest+phase (evolved, multiple iterations)

**Psyche Mining Hub v3 (`psyche_mining_hub_v3/`):**
- Profile: `psyche_mining_hub_v3_profile.json`
- Manifest: `psyche_mining_hub_v3_manifest_v1.2.json`
- Coordination: `parallel_mission_coordination_v1.json`
- **Maturity:** profile+manifest (latest iteration)

---

### 5. Venus Station — FULL (most extensive inventory)

**Files found in `venus_settlement/`:**
- **Profiles (10):** `venus_orbital_depot_profile_v1.json`, `venus_atmospheric_harvesting_profile_v1.json`, `venus_cloud_city_operations_profile_v1.json`, `venus_industrial_integration_profile_v1.json`, `venus_interplanetary_logistics_profile_v1.json`, `venus_elysium_terraforming_profile_v1.json`, `venus_wastegate_industrial_hub_profile_v1.json`, `venus_asteroid_relocation_network_profile_v1.json`, `venus_portal_enhanced_wastegate_profile_v1.json`, `venus_advanced_industrial_operations_profile_v1.json`
- **Manifests (5):** `venus_settlement_manifest_v1.json` through `v6` (v5 missing)
- **Phases (19):** `01_orbital_depot_establishment.json` through `07_venus_elysium_terraforming.json`, including sub-phases (01a, 01b, 01c, 01d, 03a, 03b, 03c, 06a, 06b, 06c)

**Maturity:** Full. Most extensively designed stage in the entire inventory with 10 profiles, 5 manifests, and 19 phases covering orbital depot through terraforming.

---

### 6. Cycler Network — FRAGMENTED (scattered across multiple locations)

**Files found:**
- `profiles/earth_mars_venus_cycler_route_establishment_profile_v1.json` — Aldrin cycler Earth-Venus-Mars-Earth orbital route (mission_profile template)
- `profiles/mars_saturn_cycler_route_establishment_profile_v1.json` — Mars-Saturn cycler route
- `phases/cycler_coordination_setup_v1.json` — Cycler coordination systems for L1 depot profile (phase 3, prerequisite: depot_infrastructure_construction_v1)
- `tasks_v2/task_cycler_base_deployment_and_coordination.json`
- `tasks_v2/task_cycler_docking_infrastructure_setup.json`
- `tasks_v2/task_cycler_transit_preparation.json`
- `ariel_settlement_cycler_manifest_v1.json` (top-level) — Uranus settlement cycler configuration
- `ariel-settlement-v2/ariel_settlement_cycler_manifest_v1.json` — Duplicate in ariel directory
- `wormhole_expansion/cycler_construction_base_deployment_phase_v1.json` — Cycler as mobile construction base

**Maturity:** Fragmented. A cycler profile exists (Earth-Mars-Venus), a coordination phase exists (tied to L1 depot), and 3 dedicated cycler tasks exist in tasks_v2. But no unified cycler manifest or comprehensive phase structure. The cycler network is treated as infrastructure supporting other stages rather than a standalone build stage.

---

### 7. Psyche — FULL (multiple versions, same as Asteroid Belt entry)

Psyche is the primary target within the Asteroid Belt stage. See section 4 above for full details.

**Maturity:** Full across v1, v2, and v3 iterations. Latest is v3 with profile+manifest+parallel coordination.

---

## Out-of-Scope Directories (Noted Only)

These directories exist but are not tied to the confirmed macro build order (Earth → Luna → L1 → Mars → Phobos/Deimos → Asteroid Belt → Venus Station → Cycler Network):

| Directory | Files | Content Type |
|---|---|---|
| `europa-subsurface-exploration/` | 7 files | Europa exploration phases (ice penetration, ocean access) |
| `ganymede-cryovolcanic-research/` | 7 files | Ganymede orbital/magnetic survey phases |
| `callisto-resource-extraction/` | 7 files | Callisto subsurface exploration phases |
| `io-volcanic-resource-extraction/` | 7 files | Io surface landing/volcanic resource phases |
| `jupiter-orbital-hub/` | 9 files | Jupiter helium-3 processing complex |
| `ariel-settlement-v2/` | 14 files | Ariel settlement with cycler manifest + heavy lift manifests |
| `dione-ice-mining/` | 2 files | Dione ice mining profile only |
| `enceladus-plume-harvesting/` | 3 files | Enceladus plume characterization phase |
| `neptune-orbital-hub/` | 16 files | Neptune small moon depot phases |
| `uranus-ammonia-depot/` | 2 files | Uranus ammonia depot profile |
| `uranus-atmospheric-siphon/` | 7 files | Uranus L3 anchor tasks |
| `uranus-aws/` | 1 file | Uranus AWS profile |
| `uranus-l3-anchor/` | 2 files | Uranus dynamic anchor construction tasks |
| `uranus-settlement-v2/` | 2 files | Uranus settlement profile v2 |
| `mercury-orbital-hub/` | 7 files | Mercury interplanetary power hub phases |
| `portia_depot_conversion/` | 3 files | Portia depot conversion profile |
| `rhea-resource-extraction/` | 1 file | Rhea resource extraction profile |

**All out-of-scope:** found, not yet reviewed, not part of current MVP path. These represent outer solar system expansion content that would come after the core inner solar system build order is established.

---

## Additional Active Directories Not in Original Scope

| Directory | Files | Notes |
|---|---|---|
| `luna_base_establishment/` | Luna precursor (already known) | Reference for "full" maturity |
| `tasks_v2/` | 60+ task files | Cross-cutting tasks (cycler, ISRU, lava tube, etc.) |
| `phases/` | Top-level phases | Cycler coordination setup tied to L1 depot |
| `profiles/` | Top-level profiles | Earth-Mars-Venus cycler route, Mars-Saturn cycler route |
| `manifests_v2/` | Top-level manifests | Luna precursor manifest v2 |
| `events/` | Event definitions | Game event system |
| `npc-base-deploy/` | NPC base deployment | Settlement infrastructure |
| `orbital-construction/` | Orbital construction | Construction templates |
| `pc-001-atmospheric-production-rush/` | Atmospheric production | Production template |
| `archived_missions/` | Archived content | Historical missions |
| `wormhole-discovery/` | Wormhole discovery | Sci-fi expansion content |
| `wormhole_expansion/` | Wormhole expansion | Includes cycler construction base phase |

---

## Gap Analysis

### Genuinely Missing / Under-Designed Stages

1. **L1 Depot** — Most critical gap. The macro build order places L1 immediately after Luna, but only two manifest files exist (no profile, no phases). This is the bottleneck for the entire post-Luna sequence since all downstream stages depend on L1 infrastructure.

2. **Cycler Network** — Fragmented across profiles/phases/tasks with no unified design. The cycler network is infrastructure that enables multiple stages but lacks its own comprehensive build order.

3. **Phobos/Deimos (dedicated)** — `super-mars-relocation/` is a thought experiment, not production-ready. Mars settlement has some Phobos/Deimos phases embedded within it (mars_genesis_phase0_*), but no standalone Phobos/Deimos build stage design exists.

### Partially Designed Stages

4. **Ceres** — Has profile+manifest+1 phase. Minimal compared to other stages. Could serve as Asteroid Belt secondary target.

5. **Miranda Mining** — Profile+tasks only (no manifest/phases). Uranus moon, not directly on MVP path but could inform outer system logistics.

### Well-Designed Stages (Ready for Reuse)

6. **Mars** — Full design with 15 phases covering orbital through terraforming.
7. **Venus Station** — Most extensive: 10 profiles, 5 manifests, 19 phases.
8. **Psyche** — Full design across v1/v2/v3 iterations with profile+manifest+phases.

---

## Recommendations for Task Staging

Based on this inventory:

1. **Priority gap to fill:** L1 Depot needs a dedicated mission profile and phase structure before downstream staging can proceed.
2. **Cycler Network** should be designed as infrastructure supporting L1 + Mars + Venus stages, not as a standalone build stage.
3. **Mars, Venus, Psyche** are ready for task extraction from existing designs — no new design needed.
4. **Phobos/Deimos** content exists within Mars settlement phases (mars_genesis_phase0_*) but may need extraction into a dedicated build stage.
5. **Ceres** could be added as Asteroid Belt secondary target with minimal additional design work.

---

## Old Code Historical Reference

Old code (`data/old-code/galaxyGame-01-08-2026/galaxy_game/data/json-data/missions/`) contains:
- `mars_settlement/` — Earlier versions of Mars settlement (cycler establishment, genesis phases, resource processing, skimmer deployment)
- `venus_settlement/` — Earlier Venus settlement designs
- `titan-resource-hub/` — Titan resource hub (outer solar system, not MVP path)
- `old_mission_ideas/` — Various old mission ideas

These are historical reference only and do not affect current staging decisions. The active directories contain evolved versions of the Mars and Venus designs.
