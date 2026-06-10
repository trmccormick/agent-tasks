---
status: active
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

# Master Backlog Triage Registry
**Path**: `docs/new_agent/tasks/backlog/TRIAGE_REGISTRY.md`  
**Description**: The centralized tracking sheet for converting legacy tasks into high-fidelity 0x files. Pass this file to the agent context (`@TRIAGE_REGISTRY.md`) at the start of every triage session.

---

## 📊 Triage Summary Dashboard

| Status | Count | Description |
| :--- | :--- | :--- |
| 📋 **Legacy Remaining** | 0 | Legacy backlog concepts waiting for verification and distillation. |
| ⏳ **In Triage** | 0 | Tasks currently being analyzed and structured. |
| 🚀 **Ready for Copilot** | 14 | High-fidelity 0x tasks optimized for tomorrow's code sprint. |
| 🧼 **Completed & Cleaned** | 0 | Legacy files permanently deleted from the workspace. |

---

## 🚀 Active Sprint Pipeline (Ready for Copilot Burn)
*These tasks are perfectly formatted, verified against the codebase, and ready for code generation.*

### ISRU Track A (Core Infrastructure)
| Target 0x Task File | Legacy Source File | Domain | Target Implementation Files |
| :--- | :--- | :--- | :--- |
| `2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md` | N/A (New) | ISRU \| ARCHITECTURE | Gap analysis against current codebase |
| `2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md` | N/A (New) | ISRU \| MANUFACTURING | `app/services/isru/production_service.rb`<br>`app/models/manufacturing_unit.rb` |
| `2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md` | N/A (New) | ISRU \| TESTS | RSpec suite for ISRU Track A validation |
| `2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md` | N/A (New) | ISRU \| DATA | Factory/Seeds for operational scenarios |

### Resource Spawning System (Deposit Engine)
| Target 0x Task File | Legacy Source File | Domain | Target Implementation Files |
| :--- | :--- | :--- | :--- |
| `2026-05-18-OVERVIEW-Resource-Spawning-Task-Breakdown.md` | N/A (New) | PLANETARY \| RESOURCE_SPAWNING | Master task breakdown document |
| `2026-05-18-DESIGN-Deposit-Plausibility-Engine.md` | N/A (New) | PLANETARY \| RESOURCE_SPAWNING | Plausibility scoring logic |
| `2026-05-18-DESIGN-Deposit-Trigger-System-And-Equipment-Gating.md` | N/A (New) | PLANETARY \| RESOURCE_SPAWNING | Trigger conditions & equipment gates |
| `2026-05-18-DESIGN-Resource-Deposit-Model-And-Persistence.md` | N/A (New) | PLANETARY \| DATABASE | `app/models/resource_deposit.rb`<br>Database schema migrations |
| `2026-05-18-IMPL-Create-ResourceDeposit-Model.md` | N/A (New) | PLANETARY \| MODEL | Implementation of ResourceDeposit model |
| `2026-05-18-IMPL-Create-DepositSpawner-Service.md` | N/A (New) | PLANETARY \| SERVICE | `app/services/planetary/deposit_spawner.rb` |
| `2026-05-18-IMPL-Integrate-Spawning-With-Game-Events.md` | N/A (New) | GAME_EVENTS \| INTEGRATION | Game event system integration |

### Documentation & System Updates
| Target 0x Task File | Legacy Source File | Domain | Target Implementation Files |
| :--- | :--- | :--- | :--- |
| `2026-05-18-HIGH-DOCUMENTATION-AGENT-WORKSPACE-JUNE-CUTOVER-ALIGNMENT.md` | N/A (New) | DOCUMENTATION | Agent workspace documentation update |
| `2026-05-18-HIGH-DOCUMENTATION-UPDATE-ROUTING-COPILOT-PRO.md` | N/A (New) | DOCUMENTATION | Copilot Pro routing guide update |

---

## ⏳ Active Triage Queue (Claude MVP Priorities)
*No items currently in triage - all tasks are ready for implementation.*

| Target 0x Task File | Priority | Domain | Core Target Concept |
| :--- | :--- | :--- | :--- |
| *None* | *N/A* | *N/A* | *All tasks completed and organized* |

---

## 🧼 Execution History & Cleanup Log
*Once Copilot completes a task and the old file is deleted, move the row here to keep history clean.*

| 0x Task File | Legacy Source Deleted? | Completion Date | Implementing Agent |
| :--- | :--- | :--- | :--- |
| *Awaiting sprint execution* | *Pending* | *YYYY-MM-DD* | *Agent Name* |

---

## 📝 Notes & Context

### Recent Triage Session (June 8, 2026 — Evening)
- **Orbital Market System Architecture Review**: April 2026 task superseded by implementation. Core market infrastructure already live (orders, trade execution, GCC settlement). Extracted gas processing pipeline gap as Phase 5+ feature task (`2026-06-08-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md`).
  
- **Orbital Structure Deployment Standardization Review**: April 2026 task partially implemented (DepotAdapter reference exists but isolated). Extracted as Phase 5+ prerequisite (`2026-06-08-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md`) + two Phase 6+ enhancement tasks for multi-structure routing and dynamic orbital simulation.

**Total Tasks Created Tonight**: 4 new high-fidelity task files  
**Files Archived to Deprecated/**: 2 legacy April backlog items with header notes explaining extraction rationale  
**Remaining in April Backlog**: ~47 files awaiting triage review (wormhole-related tasks, bug fixes from early April)

### Previous Triage Session (May 18, 2026)
- **ISRU Track A**: Completed full task breakdown from legacy concept. Created 4 high-fidelity tasks covering architecture audit, unit manager integration, RSpec suite, and data fixtures.
- **Resource Spawning System**: Converted comprehensive plan into 7 actionable tasks (1 overview + 3 designs + 3 implementations) plus 2 documentation updates.
- **Total Tasks Created**: 14 new high-fidelity task files in `docs/new_agent/tasks/backlog/2026-05/`

### Next Steps for Copilot Agent
1. Prioritize **Orbital Structure Deployment Standardization** (blocks gas processing pipeline)  
2. Execute **Gas Processing Pipeline at L1 Station** once deployment standardization complete
3. Continue April backlog triage — suggested next: wormhole-related tasks or bug fix verification batch

---

## 🤖 Instructions for the Continue/Copilot Agent
> When this file is provided in your context window via `@TRIAGE_REGISTRY.md`:
> 1. Read the **Active Sprint Pipeline** to know what features are fully designed and ready to be coded.
> 2. Check the **Notes & Context** section for recent triage sessions and prioritization guidance.
> 3. All tasks in this registry have been verified against the codebase and contain specific implementation paths.