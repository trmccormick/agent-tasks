# Surgical Guardrails Audit & Documentation Refactoring
**Date**: 2026-07-01
**Researcher**: Planner Agent (Synthesis Report)
**Project**: galaxy_game
**Status**: AWAITING HUMAN APPROVAL

---

## STATUS SYNTHESIS REPORT

### 1. Scope Assessment — Section-to-Folder Mapping

The root `docs/GUARDRAILS.md` (681 lines) is a monolithic document mixing three distinct concerns:

| Concern | Lines | Category |
|---------|-------|----------|
| Agent role boundaries & golden rule | ~30 | **Agent Protocol** |
| Universal Docking & Chassis Integration | ~15 | **Game Architecture** |
| Material Loss Logic (Task 10) | ~8 | **Mission Profile / Game Design** |
| AI Manager Guardrails (sections 1-7) | ~90 | **Game Architecture + Agent Protocol** |
| Terrain Generation & Rendering (section 7.5) | ~120 | **Game Architecture** |
| Economic System Guardrails (section 8) | ~130 | **Game Architecture** |
| Sol as AI Training Data (section 9) | ~40 | **Game Architecture** |
| Player Experience Boundaries (section 10) | ~25 | **Game Design** |
| Sci-Fi Easter Eggs (section 11) | ~60 | **Game Design** |
| Environment & Container Management (sections 12-13) | ~140 | **Agent Protocol** |
| Monitor Interface & Layer System (section 14) | ~45 | **Game Architecture** |
| Sphere Creation Optimization (section 13 duplicate) | ~35 | **Game Architecture** |
| EM Power Transition & Shield Tech | ~35 | **Game Design** |
| Resource Allocation Engine Integration | ~8 | **Game Architecture** |

#### Existing docs/architecture/ Structure (Already Populated)

The `docs/architecture/` folder already has extensive, well-organized subdirectories:

```
docs/architecture/
├── economy/          ← 13 files covering contracts, currency, fiscal policy, ISRU pricing, ledgers, market ops
├── ai_manager/       ← 29 files covering architecture, economic logic, intent, orchestrator spec, etc.
├── wormhole/         ← Wormhole-related architecture
├── simulation/       ← Simulation architecture
├── starsim/          ← Stellar simulation
├── terrasim/         ← Terrain simulation
├── settlement/       ← Settlement architecture
├── logistics/        ← Logistics architecture
├── stations/         ← Station architecture (docking belongs here)
├── structures/       ← Structure architecture (shield tech belongs here)
├── units/            ← Unit architecture
├── systems/          ← System architecture
├── glossary/         ← Glossary entries
├── core/             ← Core architecture
├── isru/             ← ISRU architecture
├── operations/       ← Operations architecture
├── patterns/         ← Pattern documentation
├── planning/         ← Planning documentation
├── services/         ← Service architecture
├── concerns/         ← Cross-cutting concerns
├── biology/          ← Biology simulation
├── asteroid_conversion_logic.md
└── overview.md
```

The `docs/mission_profiles/` folder has 3 files covering profile library, Luna base establishment, and orbital settlement strategies.

#### Migration Mapping — Where Each Section Belongs

| Root GUARDRAILS Section | Destination | Rationale |
|------------------------|-------------|-----------|
| **Universal Docking & Chassis** | `docs/architecture/stations/` | Craft docking is station/craft architecture |
| **Material Loss Logic** | `docs/mission_profiles/` or `docs/architecture/logistics/` | Interplanetary transit logistics |
| **AI Manager: Anchor Law** | `docs/architecture/wormhole/` + `docs/architecture/settlement/` | Wormhole stability + settlement patterns |
| **AI Manager: Market & GCC** | `docs/architecture/economy/` | Already has 13 economy files — natural home |
| **AI Manager: Operational Boundaries** | `docs/architecture/ai_manager/` | Already has 29 AI manager files |
| **Namespace Preservation / Sabatier** | `docs/architecture/core/` or `docs/architecture/concerns/` | Code architecture concern |
| **Path Configuration Standards** | `docs/architecture/core/` | Core infrastructure |
| **Terrain Generation & Rendering** | `docs/architecture/simulation/` + `docs/architecture/starsim/` | Terrain = simulation, rendering = starsim |
| **No Fog of War / Worldhouse / Desert** | `docs/architecture/simulation/` | Terrain rendering rules |
| **Economic System Guardrails** | `docs/architecture/economy/` | Already has comprehensive economy docs |
| **Earth-Luna Anchor & Import Parity** | `docs/architecture/economy/` | Pricing model — already covered by FISCAL_POLICY_AND_FEES.md |
| **AI Manager Treasury & Robot Fleet** | `docs/architecture/ai_manager/` | AI manager economic logic |
| **Sol as AI Training Data** | `docs/architecture/ai_manager/patterns/` | Pattern learning documentation |
| **Player Experience Boundaries** | `docs/planning/` or `docs/architecture/overview.md` | Game design / UX |
| **Sci-Fi Easter Eggs** | `docs/architecture/planning/` or `docs/architecture/glossary/` | World-building / naming conventions |
| **Monitor Interface & Layer System** | `docs/architecture/simulation/` + `docs/architecture/terrasim/` | UI/rendering architecture |
| **Sphere Creation Optimization** | `docs/architecture/settlement/` or `docs/architecture/simulation/` | Celestial body generation |
| **EM Power Transition & Shield Tech** | `docs/architecture/stations/` + `docs/architecture/structures/` | Energy systems + shield architecture |
| **Resource Allocation Engine** | `docs/architecture/ai_manager/` | AI manager service integration |
| **Agent Role Boundaries** | **STAY in GUARDRAILS.md** | Agent protocol — operational rule |
| **Environment & Container Management** | **STAY in GUARDRAILS.md** | Agent protocol — operational rule |
| **Database Environment Protection** | **STAY in GUARDRAILS.md** | Agent protocol — operational rule |

### 2. Implementation vs. Intent — Economic Constants Audit

#### Verified Against GLOSSARY_SYSTEM_MECHANICS.md (Economic Layer section):

| Constant | GUARDRAILS.md Value | GLOSSARY Value | Status |
|----------|---------------------|----------------|--------|
| SCC Surcharge | 0.5% | 0.5% | ✅ Aligned |
| Broker Fee | 0.3% | 0.3% | ✅ Aligned |
| Sales Tax | 3.37% | 3.37% | ✅ Aligned |

#### Hardcoded References Found in docs/GUARDRAILS.md:

| Line Range | Value | Context | Assessment |
|------------|-------|---------|------------|
| ~line 150 | `40%` | Sabatier tax reduction for local fuel production | **GAME LOGIC** — not a fee constant, should be in ISRU pricing docs |
| ~line 200 | `5-10%` | Material loss rate for interplanetary transit | **GAME DESIGN** — mission profile constraint |
| ~line 230 | `10^16 kg` | Gravitational anchor mass threshold | **GAME PHYSICS** — wormhole architecture |
| ~line 250 | `>95%` | Terraformed system integrity threshold | **GAME DESIGN** — AI manager constraint |
| ~line 270 | `85%` | Autonomous construction success rate | **GAME DESIGN** — validation metric |
| ~line 310 | `10%` | Max contract value as % of GCC supply | **GAME ECONOMY** — already in economy docs |
| ~line 320 | `5 active contracts/day` | Daily contract limit | **GAME DESIGN** — player experience |
| ~line 330 | `90 days` | Max contract duration | **GAME DESIGN** — player experience |
| ~line 340 | `50%` | NPC overdraft limit | **GAME ECONOMY** — already in economy docs |
| ~line 350 | `200%` | Player debt ceiling vs net worth | **GAME ECONOMY** — needs verification against economy docs |
| ~line 360 | `2%` | Minimum annual interest on overdrafts | **GAME ECONOMY** — needs verification |
| ~line 370 | `25%` | LDC stabilization reserves | **GAME ECONOMY** — already in economy docs |
| ~line 380 | `10%` | System-wide liquidity reserve | **GAME ECONOMY** — already in economy docs |
| ~line 390 | `5%` | Annual GDP emergency funds | **GAME ECONOMY** — needs verification |
| ~line 400 | `±5%` | GCC/USD daily exchange rate band | **GAME ECONOMY** — already in economy docs |
| ~line 410 | `5%` | Annual GCC minting limit | **GAME ECONOMY** — already in economy docs |
| ~line 430 | `>30%` | NPC debt expansion restriction threshold | **GAME ECONOMY** — needs verification |
| ~line 500 | `90%` | NPC efficiency cap vs player | **GAME BALANCE** — player experience |
| ~line 510 | `1.5x` | Player contract GCC multiplier vs NPC | **GAME BALANCE** — player experience |
| ~line 600 | `30%` | Shielded asteroid volume | **GAME PHYSICS** — asteroid conversion |
| ~line 610 | `70%` | Slag ratio from asteroid hollowing | **GAME PHYSICS** — already in glossary |

#### Critical Finding: Economic Constants Are NOT Hardcoded in Ruby

The GUARDRAILS.md Section 8 states:
> "Never hardcode these values in Ruby — read from game constants."

This is a **design rule**, not a code reference. The actual economic constants (0.5% SCC, 0.3% Broker, 3.37% Sales Tax) are defined in `GLOSSARY_SYSTEM_MECHANICS.md` under the Economic Layer section and should be the single source of truth.

**Recommendation**: Add a cross-reference in GUARDRAILS.md pointing to GLOSSARY_SYSTEM_MECHANICS.md as the canonical fee/tax reference, and verify that `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` matches.

### 3. Proposed Action — Refactoring Plan

#### Phase 1: Audit & Verify (No Changes)
1. Read `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` to confirm fee/tax alignment
2. Read `docs/architecture/ai_manager/AI_MANAGER_ECONOMIC_LOGIC_UPDATE.md` for treasury/robot fleet alignment
3. Read `docs/architecture/wormhole/` files for Anchor Law alignment
4. Cross-reference all game design percentages against GLOSSARY_SYSTEM_MECHANICS.md

#### Phase 2: Extract Game Architecture Content (New Files)
Move domain-specific content from GUARDRAILS.md into existing architecture subdirectories:

| Source Section | New File | Destination |
|---------------|----------|-------------|
| Universal Docking & Chassis | `docking_chassis_integration.md` | `docs/architecture/stations/` |
| Material Loss Logic | `interplanetary_transit_loss_model.md` | `docs/architecture/logistics/` |
| Anchor Law (wormhole physics) | `gravitational_anchor_requirements.md` | `docs/architecture/wormhole/` |
| Terrain Generation Architecture | `terrain_generation_architecture.md` | `docs/architecture/simulation/` |
| Terrain Rendering Rules | `terrain_rendering_rules.md` | `docs/architecture/terrasim/` |
| Monitor Interface & Layer System | `monitor_interface_layers.md` | `docs/architecture/terrasim/` |
| Sphere Creation Optimization | `celestial_body_generation.md` | `docs/architecture/settlement/` |
| EM Power Transition & Shields | `em_power_shield_tiers.md` | `docs/architecture/stations/` |
| Resource Allocation Engine | `resource_allocator_integration.md` | `docs/architecture/ai_manager/` |
| Namespace Preservation / Sabatier | `namespace_and_autoloader_rules.md` | `docs/architecture/core/` |
| Path Configuration Standards | `path_configuration_standards.md` | `docs/architecture/core/` |

#### Phase 3: Extract Game Design Content (New Files or Existing)
| Source Section | Destination | Notes |
|---------------|-------------|-------|
| Player Experience Boundaries | `docs/architecture/overview.md` or `docs/planning/` | UX/game balance |
| Sci-Fi Easter Eggs | `docs/architecture/glossary/` or `docs/architecture/planning/` | World-building |
| Sol as AI Training Data | `docs/architecture/ai_manager/patterns/` | Pattern learning |

#### Phase 4: Trim GUARDRAILS.md to Agent Protocol Only
After extraction, the root `docs/GUARDRAILS.md` should contain ONLY:
- Agent role boundaries (Planner vs Executor)
- Golden Rule (host vs Docker)
- Database protection rules
- Container management rules
- Test execution rules

**Expected final size**: ~150 lines (down from 681)

#### Phase 5: Create Migration Index
Add a section at the top of the trimmed GUARDRAILS.md with links to all migrated content:

```markdown
## 📋 Documentation Index

This file contains **agent operational rules only**. All game architecture,
design, and domain-specific guardrails have been migrated to dedicated docs.

### Game Architecture
- [Economic System](architecture/economy/README.md)
- [AI Manager](architecture/ai_manager/AI_MANAGER_ARCHITECTURE.md)
- [Wormhole Physics](architecture/wormhole/README.md)
- [Terrain Generation](architecture/simulation/terrain_generation_architecture.md)
- [Terrain Rendering](architecture/terrasim/README.md)
- [Stations & Docking](architecture/stations/README.md)
- [Shield Technology](architecture/stations/em_power_shield_tiers.md)
- [Celestial Body Generation](architecture/settlement/celestial_body_generation.md)

### Game Design
- [Player Experience](planning/player_experience.md)
- [Easter Eggs](architecture/glossary/easter_eggs.md)
- [AI Training Data](architecture/ai_manager/patterns/sol_training_data.md)

### System Mechanics Reference
- [Glossary & Economic Constants](GLOSSARY_SYSTEM_MECHANICS.md) — **Canonical fee/tax reference**
```

#### Phase 6: Update agent-t/rules/GUARDRAILS.md Sync
The `agent-tasks/rules/GUARDRAILS.md` is the operational guardrails copy used by all agents. After refactoring, ensure it stays in sync with the trimmed project GUARDRAILS.md (they should be identical for agent rules).

### 4. Deep Audit Findings — Evidence & Issues

#### Issue 1: Duplicate Section Numbering
- Sections are numbered 1, 2, 3, 5, 7, 7.5, 8, 9, 10, 11, 12, 13, 14, 13 (duplicate), ⚡, 🛠️
- Section 4 is missing entirely
- Section 13 appears twice (Database Protection + Sphere Creation)
- **Severity**: Low — cosmetic but confusing for navigation

#### Issue 2: Mixed Concern Levels
The document mixes:
- **Agent operational rules** (Docker, RSpec, database protection) — should be in GUARDRAILS.md
- **Game architecture decisions** (terrain generation, namespace rules) — should be in docs/architecture/
- **Game design constraints** (economic limits, player experience) — should be in docs/planning/ or mission profiles
- **Implementation notes** (Sabatier bug fix, incident precedents) — should be in commit history or architecture docs

#### Issue 3: Economic Constants Fragmentation
The three canonical fees (0.5% SCC, 0.3% Broker, 3.37% Sales Tax) appear in:
1. `docs/GUARDRAILS.md` Section 8 (Rule 15) — as design rules
2. `docs/GLOSSARY_SYSTEM_MECHANICS.md` Economic Layer — as canonical definition
3. `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` — as implementation reference

**Recommendation**: GLOSSARY_SYSTEM_MECHANICS.md is the canonical source. GUARDRAILS.md should cross-reference it, not redefine the values.

#### Issue 4: Stale Implementation Notes
Several sections contain time-bound implementation notes that belong in architecture docs or commit history:
- "Sabatier Bug Fix [2026-01-15]" — resolved bug, not a standing rule
- "Incident Precedent [2026-01-15]" — historical note
- "Architecture Correction [2026-02-05]" — should be in simulation architecture docs
- "Phase 1: ✅ Implemented" — status marker, not a rule

#### Issue 5: Overlap with Existing Architecture Docs
The `docs/architecture/economy/` folder already has 13 files covering economic topics. Much of Section 8 (Economic System Guardrails) duplicates content that should be consolidated into those existing files rather than maintained in GUARDRAILS.md.

Similarly, `docs/architecture/ai_manager/` has 29 files — the AI Manager guardrails in GUARDRAILS.md overlap significantly.

### 5. Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Breaking cross-references during migration | Medium | High | Create migration index before deleting content |
| Losing game design intent during extraction | Medium | High | Preserve all original text in new files, don't summarize |
| agent-tasks/rules/GUARDRAILS.md drift | High | Medium | Sync both copies simultaneously |
| Agent confusion about which GUARDRAILS to read | Low | Medium | Clear documentation index + README update |

### 6. Approval Checklist

- [ ] **Approve scope**: Section-to-folder mapping is correct
- [ ] **Approve extraction order**: Phase 1 → 2 → 3 → 4 → 5 → 6
- [ ] **Approve trimming**: GUARDRAILS.md should be ~150 lines of agent rules only
- [ ] **Approve canonical source**: GLOSSARY_SYSTEM_MECHANICS.md as single fee/tax reference
- [ ] **Approve migration index**: Links from trimmed GUARDRAILS to all migrated content
- [ ] **Review Phase 2 files**: Before committing, review each new file for accuracy
- [ ] **Sync agent-tasks copy**: After project GUARDRAILS.md is trimmed, update agent-tasks/rules/GUARDRAILS.md

---

**This report is AWAITING HUMAN APPROVAL. No files will be modified until approved.**
