# GUARDRAILS Consolidation — Audit & Reasoning

**Date**: 2026-07-02
**Author**: STRATEGIST (session strategy)
**Purpose**: Document evidence and reasoning for key decisions in the GUARDRAILS consolidation task

---

## 1. em_power_shield_tiers.md Path Decision

### Question Resolved
Where does `em_power_shield_tiers.md` belong?

### Candidate Paths Checked
| Path | Exists? | Content |
|---|---|---|
| `docs/architecture/systems/em_power_shield_tiers.md` | **YES** (directory exists) | 14 files including `em_technology_tree.md` — directly related EM tech content |
| `docs/architecture/stations/em_power_shield_tiers.md` | Directory exists | 10 files focused on specific station designs (CERES_GATEWAY, asteroid_relocation_tug, etc.) |
| `docs/architecture/structures/em_power_shield_tiers.md` | Directory exists | Only README.md — sparse, "structures" implies physical buildings |

### Content Analysis (GUARDRAILS.md lines 648-681)
The section covers:
- **Energy system progression**: Fusion → EM → Wormhole (gameplay tech tree)
- **Shield technology tiers**: Tier 1 (Fusion-Powered), Tier 2 (EM-Core Retrofit), Tier 3 (Wormhole-Stabilized)
- **Upgrade events & vulnerabilities**: Transition windows, risks, sabotage mechanics
- **Security & market dynamics**: EM logistics, traitor opportunities, GCC impact

### Decision: `docs/architecture/systems/em_power_shield_tiers.md`

**Reasoning:**
1. The content is about **gameplay systems mechanics** (energy progression, shield tiers, upgrade events), not station blueprints or building structures.
2. `docs/architecture/systems/` already contains `em_technology_tree.md` — the natural companion document for EM power + shield tech evolution.
3. The section's "Documentation Requirements" explicitly calls out "tech tree and mission profiles" — systems-level documentation, not station-specific.
4. `docs/architecture/stations/` is for specific station designs (CERES_GATEWAY.md, etc.) — these are individual station blueprints, not system mechanics.
5. `docs/architecture/structures/` is too sparse and "structures" implies physical buildings, not technology systems.

**Command output confirming directory existence:**
```
$ test -d /Users/tam0013/Documents/git/galaxyGame/docs/architecture/systems && echo "EXISTS" || echo "DOES NOT EXIST"
EXISTS

$ ls /Users/tam0013/Documents/git/galaxyGame/docs/architecture/systems/
BIOME_TERRAFORMING_DESIGN.md
LUNA_ISRU_GAS_PROCESSING_AND_S...
PORT_CONNECTION_SYSTEM.md
ai_manager_economic_loop.md
alpha_centauri_prep.md
aol-732356.md
asteroid_conversion_physics.md
em_technology_tree.md          ← direct companion for em_power_shield_tiers
environmental_volume_intent.md
job_system_mechanics_spec.md
orphaned_system_economics.md
rig_system.md
survey_and_handshake_protocol.md
```

---

## 2. agent-tasks/rules/GUARDRAILS.md Re-verification

### Prior Research Claim (2026-07-01)
The prior research cited `agent-tasks/rules/GUARDRAILS.md` as 425 lines.

### Current Verification (2026-07-02)
```
$ wc -l /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
425 lines
```

**Confirmed: Still 425 lines.** The figure has not drifted since 2026-07-01.

### Content Summary (all 425 lines)
The file contains 26 rules organized into these categories:

| Category | Rules | Description |
|---|---|---|
| Tool Availability | Rule 0 | Available/forbidden tools list |
| Docker | Rule 1 | `docker exec -it web` format, no `cd` prefix |
| Database Migrations | Rule 2 | Rails generator only, never manual |
| RSpec Execution | Rules 3, 3a, 7 | Targeted specs only, pre-execution check, output format |
| Paths | Rule 10 | Host vs Docker path distinction |
| Documentation | Rules 11, 12, 13, 13a, 14 | Atomic docs, task lifecycle, handoff discipline, code payload |
| Economic | Rule 15 | Financial constants (USD/GCC peg, SCC, Broker Fee, Sales Tax, Lunar loss) |
| Safety | Rules 16-20 | No parallel RSpec, synthesis gate, integration spec quarantine, stop conditions, local model fabrication prohibition |
| Agent-Specific | Rules 21-22 | Qwen3.5 triage requirements, Continue model scope limits |
| Token Conservation | Rule 23 | Escalation ladder (local → cloud → premium) |
| Perplexity | Rule 24 | Task validation workflow |
| File Integrity | Rule 25 | No file recreation from scratch |
| Git Discipline | Rule 26 | No autonomous git commit/push (confirmed violation 2026-06-26) |

### Gap Analysis Methodology for Executor
The executor must compare each generic rules section in galaxyGame's GUARDRAILS.md against these 26 rules. Any generic rule in galaxyGame's file that has NO corresponding rule in agent-tasks/rules/GUARDRAILS.md is a **genuine gap** (category 2). Any content that overlaps is a **duplicate** (category 1).

### Gap Analysis Results (Produced by STRATEGIST, 2026-07-03)
After reading all 425 lines of agent-tasks/rules/GUARDRAILS.md and comparing against every generic-agent-rule section in docs/GUARDRAILS.md:

**There are only 2 genuine gaps (Category 2):**

| Gap | What to Add | Where in agent-tasks |
|---|---|---|
| RAILS_ENV=test + DATABASE_URL unset requirement | Mandatory prefix for ALL test commands | Enhancement to Rule 2 and Rule 3 |
| Container lifecycle prohibition (NEVER start/stop/restart) | "Containers always running" clause with forbidden commands list | Enhancement to Rule 1 Docker section |

**Everything else in galaxyGame's GUARDRAILS.md that appears to be a generic agent rule is already covered by one of the 26 rules in agent-tasks/rules/GUARDRAILS.md.** The "Environment & Container Management" section (~140 lines) is more verbose but covers the same ground.

**Important:** Line count alone (425 lines) doesn't confirm content hasn't been reorganized since 2026-07-01. The executor must read both files side-by-side before merging.

---

## 3. Prior GUARDRAILS Task Files — Full Inventory

Found via `find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/ -iname "*guardrails*" -type f | sort`:

### Refactored-Task-Files (2026-07-01 research)
1. `.../refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` — Unified reconciliation task
2. `.../refactored-task-files/2026-07-01-TASK-UPDATE-2/GUARDRAILS_REF_PACKAGE.md` — Reference package with section-to-folder mapping
3. `.../refactored-task-files/2026-07-01-TASK-UPDATE-2/TASK_REF_GUARDRAILS_MIGRATION_V2.md` — Migration task reference
4. `.../refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md` — Pre-work for missing directories
5. `.../refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md` — Pre-work for path ambiguities
6. `.../refactored-task-files/DISPATCH_GUARDRAILS_CONSOLIDATION_RYZEN.md` — Dispatch file

### Working-Temp (ephemeral handoff files)
7. `.../refactored-task-files/_working-temp/2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md` — Qwen validation
8. `.../refactored-task-files/_working-temp/GEMINI_HANDOFF_GUARDRAILS_RECONCILIATION.md` — Gemini handoff
9. `.../refactored-task-files/_working-temp/QWEN_HANDOFF_GUARDRAILS_VALIDATION.md` — Qwen handoff

### Reorganization Attempts (stale, pre-consolidation)
10. `.../backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-DOCUMENTATION-GUARDRAILS-SPLIT.md`
11. `.../backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-FEATURE-GUARDRAILS-SPLIT.md`
12. `.../backlog/reorganization attempt 2/2026-02/2026-02-11-HIGH-GUARDRAILS-SPLIT.md`
13. `.../backlog/reorganization attempt 2/2026-03/2026-03-22-MEDIUM-DOCUMENTATION-GUARDRAILS-SPLIT.md`
14. `.../backlog/reorganization attempt 2/2026-03/2026-03-22-MEDIUM-TASK-GUARDRAILS-SPLIT.md`
15. `.../backlog/reorganization attempt 2/2026-04/2026-04-02-HIGH-ARCHITECTURE-GUARDRAILS-SPLIT.md`
16. `.../backlog/reorganization attempt 2/2026-04/2026-04-04-HIGH-ARCHITECTURE-GUARDRAILS-SPLIT.md`

### Review folder
17. `.../tasks/review/TASK_GUARDRAILS_SPLIT.md`

**Total: 17 superseded files.** All should be archived or deleted after execution of this consolidated task.

---

## 4. Four-Way Sort — Summary of Settled Decisions

| Category | Action | Destination |
|---|---|---|
| **1. Duplicates** | Remove from docs/GUARDRAILS.md | No new files — content already in agent-tasks/rules/GUARDRAILS.md |
| **2. Gaps** | Merge into agent-tasks/rules/GUARDRAILS.md | Requires executor to re-verify current content before merging |
| **3. Game-specific process rules** | New project-specific file | `/Users/tam0013/Documents/git/galaxyGame/docs/` (name TBD, NOT "GUARDRAILS") |
| **4. Game design content** | Distribute to galaxyGame docs/ subdirectories | Per section-to-folder mapping in consolidated task file |

---

## 5. Consolidated Task File Location

The unified task file is at:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md`

**Phase placement rationale**: Per PHASE_STRUCTURE.md, phases 5+ are Luna simulation testing prerequisites only. This GUARDRAILS consolidation is a cross-cutting documentation architecture task that doesn't fit any specific phase's scope. It belongs in `backlog/` directly at the root level.
