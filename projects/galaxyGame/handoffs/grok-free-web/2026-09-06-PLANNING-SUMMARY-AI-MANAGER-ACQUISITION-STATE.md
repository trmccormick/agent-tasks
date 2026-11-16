# Planning Summary — AI Manager Lane (for Grok)
**Date:** 2026-09-06
**Author:** Qwen (planning agent, local Copilot session)
**Purpose:** Verified state-of-the-world for the AI Manager acquisition/escalation
territory, to ground the next mission. Supersedes the "next single mission"
section of `2026-09-06-SESSION-HANDOFF-AI-MANAGER.md` where the two differ.

> **Method:** Every claim below was re-verified against disk/git this session
> (2026-09-06). Where a prior handoff's narrative conflicts with disk, disk wins.
> This is the reason this summary exists — the 09-06 Claude handoff described the
> GCC power/battery task as "dispatched to Qwen, awaiting results," but the task
> file was **never dispatched** (see §5).

---

## 1. Verified Codebase State (AI Manager acquisition surface)

### Services that exist (line counts verified 2026-09-06)

| Service | Lines | Spec coverage | Notes |
|---------|-------|---------------|-------|
| `escalation_service.rb` | **627** | `escalation_service_spec.rb` ✅ | The real spine. `handle_resource_shortage`, `handle_expired_buy_orders`, `normalize_material`, `can_harvest_locally?` (line 457), strategy routing (automated_harvesting / deploy_manufacturing_unit / scheduled_import) |
| `procurement_service.rb` | 112 | `procurement_service_can_produce_locally_spec.rb` ✅ (partial) | ISRU→market→emergency chain. **`check_market_price` is hardcoded placeholder pricing** (oxygen: 500, water: 300, food: 800, structural_carbon: 2000). `produce_locally` only logs — no job queued |
| `resource_acquisition_service.rb` | 148 | ❌ none | Local-GCC vs external-USD fork. `check_expired_orders` delegates to `EscalationService.handle_expired_buy_orders` — so it's a thin trigger layer over Escalation |
| `resource_fulfillment_service.rb` | 33 | ❌ none | Thin wrapper delegating to `MaterialRequestService.request_materials` (exists at `app/services/material_request_service.rb`) |
| `foothold_planner.rb` | 370 | `foothold_planner_spec.rb` — **13 `it` blocks** (handoff said 10/10; spec has grown) | Closed work, verified present |
| `precursor_capability_service.rb` | 269 | `precursor_capability_service_spec.rb` ✅ | Shared capability layer — FootholdPlanner and ProcurementService both use it |

### Call sites (verified via grep, 2026-09-06)
The three acquisition services are referenced from:
- `market_stabilization_service.rb`
- `resource_planner.rb`
- `service_coordinator.rb`
- `operational_manager.rb`
- `testing/sandbox_environment.rb`

**Implication:** these are not dead code — they're woven into the manager loop.
Any consolidation decision must account for these call sites.

### ⚠️ Duplicate method found (new, not in any prior handoff)
`can_harvest_locally?` exists in **two places**:
- `ai_manager/escalation_service.rb:457` — `self.can_harvest_locally?(settlement, material)`
- `services/resource/acquisition.rb:35` — `def can_harvest_locally?(resource_name)`

Different signatures, different modules. The 08-23 NEEDS_REVIEW entry about
O2 credit without ISRU gate refers to the escalation_service one. The inventory
mission should determine whether these are two implementations of the same
concept (divergence risk) or genuinely different concerns.

---

## 2. The Key Finding for Your Next Mission

**There are already THREE overlapping acquisition services plus the escalation
spine.** The standing rule "do not build a parallel ProcurementService" is
correct, but the situation is worse than the handoff implies — there isn't one
parallel service to avoid, there are **three** that partially overlap:

```
ResourceFulfillmentService (33 lines)  → delegates to MaterialRequestService
ResourceAcquisitionService (148 lines) → local/USD fork + delegates to EscalationService
ProcurementService (112 lines)         → ISRU→market→emergency (placeholder pricing)
EscalationService (627 lines)          → the real spine: shortage handling, strategy routing
```

**The read-only inventory mission (your "next single mission") must cover all
four, not just EscalationService.** The deliverable should answer:
1. What does each service actually do vs. delegate?
2. Where do their responsibilities overlap or conflict?
3. Which one is the canonical acquisition path in the live manager loop?
4. Is the placeholder pricing in `ProcurementService#check_market_price`
   exercised anywhere, or is it dead?
5. Are the two `can_harvest_locally?` methods the same concept implemented twice?

**Recommendation:** the existing task
`backlog/ai-manager/2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`
is the natural vehicle for this — it's already scoped as a lightweight
classification + gap-notes research task. **Scope it up** to explicitly include
the four-service acquisition surface + the duplicate `can_harvest_locally?`
finding, rather than drafting a new task. This avoids the duplicate-task
failure mode that hit the Lookup Service Caching task.

---

## 3. What's Closed (verified, don't re-open)

- **FootholdPlanner** — architecture + skeleton + spec (13 examples) + Super-Mars
  no-moon test case. Task in `completed/2026-09/`. Commits `b3d5b191`, `766f1c07`.
- **Material sourcing convention** — locked: facility-based + market-driven,
  no location-keyed sourcing blocks. Documented in
  `/memories/repo/material_sourcing_convention.md`.
- **Lookup Service Caching** — confirmed completed, duplicate removed.

---

## 4. What's Draft / Blocked (don't dispatch yet)

| Item | Status | Why blocked |
|------|--------|-------------|
| Material Sourcing & Acquisition Architecture (`backlog/ai-manager/2026-09-03-...`) | **DRAFT** | Must wait for the inventory mission to return. Frame as *extension of the acquisition spine*, not new architecture. |
| Multi-System Resource Coordination (`backlog/ai-manager/2026-06-07-...`) | **DEFERRED** | Needs multi-settlement + acquisition spine + wormhole cost inputs. Phase 9+. |
| Luna worked-example capture (`backlog/ai-manager/2026-09-01-...`) | **BACKLOG** | After Luna loop stable. |
| MissionPlanner dual entry (`backlog/ai-manager/2026-09-01-...`) | **BACKLOG** | Lower priority; Foothold API exists. |
| AI Manager outside phase structure (`backlog/ai-manager/2026-09-01-...`) | **BACKLOG** | Documentation task. |

---

## 5. GCC Power/Battery Task — Corrected Status

**Handoff claim (09-06 Claude):** "dispatched to Qwen, awaiting results"
**Verified disk state (2026-09-06):**
- Location: `backlog/current/2026-09-03-MEDIUM-RESEARCH-GCC-SAT-POWER-BATTERY-DISCREPANCY.md`
- Status header: `status: backlog` (never changed to active)
- Completion Report: **empty template**
- Synthesis report: **does not exist**
- Git history: one commit (`eb56f80` — relocation between backlog folders), **never in active/**

**Corrected status: NOT DISPATCHED. Ready to dispatch.**
The task file is well-formed. The one unchecked readiness box (file path
verification) is Step 1 of the task itself. This is a MEDIUM-priority research
task that gates confidence in the real-loop integration test. It is **not**
AI Manager lane work (system_domain: OTHER) — it's a general gameplay bug
investigation. Flag to Tracy for dispatch decision, not to Grok.

---

## 6. Standing Rules (unchanged, binding on this lane)

1. **Task-file ownership** — only edit/move your assigned task (Rule 29).
2. **AI Manager work** lives under `backlog/ai-manager/`.
3. **Resource-first is prior art** — escalation, construction economics, cycler, v2 tasks.
4. **No location-keyed sourcing** on material JSON.
5. **No parallel acquisition architecture** — extend the existing spine after inventory.
6. **Closeout:** task file → completed + honest status + status.md update.
7. **Handoff at session end** — update the AI Manager handoff file.

---

## 7. Recommended Next Actions (priority order)

1. **Scope up the existing service-inventory task** (`2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md`) to explicitly cover the four-service acquisition surface + duplicate `can_harvest_locally?`. Move to active, dispatch.
2. **Review the inventory findings** before touching the Material Sourcing & Acquisition Architecture task.
3. **GCC power/battery task** — flag to Tracy for dispatch (not AI Manager lane).
4. **NEEDS_REVIEW #8** (Oxygen fixture chain-tracing) — still OPEN, still needs Tracy's design input. The inventory mission should confirm whether the `can_harvest_locally?` O2 gate fix (from 08-28) is still in place.

---

## 8. Cross-links

- Prior AI Manager handoff: `handoffs/grok(free web)/2026-09-06-SESSION-HANDOFF-AI-MANAGER.md`
- Project closing handoff: `handoffs/claude(free web)/2026-09-06-SESSION-HANDOFF-CLOSING.md`
- Foothold design: `docs/architecture/ai_manager/FOOTHOLD_PLANNER_ARCHITECTURE.md`
- Super-Mars scenario: `docs/architecture/ai_manager/SUPER_MARS_NO_MOON_TEST_CASE.md`
- Escalation architecture doc: `docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md`
- Material sourcing convention: `/memories/repo/material_sourcing_convention.md`
- NEEDS_REVIEW: `projects/galaxy_game/NEEDS_REVIEW.md`
