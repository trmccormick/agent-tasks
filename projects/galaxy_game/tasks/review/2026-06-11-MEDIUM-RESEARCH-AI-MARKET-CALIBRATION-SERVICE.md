[MOVED_FROM: projects/galaxy_game/tasks/backlog/phase6+/2026-06-11-MEDIUM-RESEARCH-AI-MARKET-CALIBRATION-SERVICE.md]
[RATIONALE: Luna simulation calibration work — belongs in Phase 5, not surface infrastructure]
---
id: 2026-06-11-MEDIUM-RESEARCH-AI-MARKET-CALIBRATION-SERVICE
status: backlog
priority: MEDIUM
type: research
system_domain: AI_MANAGER
mvp_alignment: POST_LUNA_MVP
local_worker_safe: false
---

# TASK: Research — Autonomous Market Calibration Service Architecture
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-06-11
**Phase Gate**: phase6+ — do not dispatch until Phase 5 simulation is running and
multi-settlement calibration strain is observed

---

## Context

As Sol economy scales beyond Luna to multiple active settlements, AI Manager constants
will require ongoing calibration that becomes impractical to do manually. This task
tracks the research needed to design an autonomous calibration suggestion layer.

Full concept documented in:
`research/AI-MANAGER-MARKET-CALIBRATION-CONCEPT.md`

---

## Problem Statement

Manual tuning of AI Manager constants (consumption rates, EAP adjustments, transit
buffers, shortage thresholds) is manageable at Luna scale. At 3+ active settlements
with concurrent terraforming operations and active player economy, manual calibration
becomes a recurring bottleneck.

---

## Research Scope

- [ ] Review Phase 5 simulation telemetry outputs — what signals are available?
- [ ] Define drift detection criteria — what constitutes meaningful calibration need?
- [ ] Evaluate external analytics service architecture vs embedded approach
- [ ] Define admin review interface requirements
- [ ] Assess Ollama local instance viability for suggestion generation
- [ ] Define JSON schema for parameter adjustment suggestions
- [ ] Prototype MockCalibrator for development/testing
- [ ] Document integration points with existing Virtual Ledger escalation path

---

## Dependencies

**Blocked by:**
- Phase 5 simulation running with real telemetry data
- Virtual Ledger health monitoring mature and producing meaningful drift signals
- 3+ active settlements online showing manual calibration strain

**Do not dispatch until all blockers are resolved.**

---

## Related Files

- `research/AI-MANAGER-MARKET-CALIBRATION-CONCEPT.md` — full concept and constraints
- `research/LUNA-MVP-SIMULATION-DESIGN.md` — Virtual Ledger diagnostic context

---

## Notes

Origin: Gemini architecture review session, June 11 2026. Core strategy pattern
(Mock vs LLM, external service, admin-gated application) is sound. Implementation
detail deferred until simulation data makes requirements concrete.

This is a research task — no implementation until research phase produces a
design doc reviewed by Claude strategist.
