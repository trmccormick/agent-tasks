# AI Manager — Autonomous Market Calibration Concept
**Created:** 2026-06-11
**Status:** Research concept — not yet actionable
**Origin:** Gemini architecture review session, June 11 2026
**Phase Gate:** Phase 6+ — requires Phase 5 simulation data before design can be finalized

---

## Concept Summary

As the Sol economy scales across multiple worlds, AI Manager constants (consumption rates,
EAP adjustments, transit buffers, shortage thresholds) will become increasingly difficult
to tune manually. A market calibration feedback layer — separate from the core Rails game
engine — could analyze simulation telemetry and suggest parameter adjustments for game
admin review before application.

This is not a replacement for the existing AI Manager. It is a calibration assistant that
operates above the AI Manager, watching aggregate economic health signals and proposing
tuning changes when the simulation drifts from design intent.

---

## Problem This Solves

**Phase 5 (Luna simulation observation):** Game admins manually review Virtual Ledger
health, identify drift, and adjust constants. Manageable for one settlement.

**Phase 6-9 (Multi-world Sol economy):** 10+ settlements, multiple cycler routes, active
player economy, concurrent terraforming operations. Manual calibration becomes impractical.
Constants that are correct for Luna Year 1 may be wrong for Sol Year 10.

The AI Manager escalates to game admins when Virtual Ledger goes critical — but admins
need tooling to diagnose root cause and propose calibration changes efficiently.

---

## Proposed Architecture (Concept Only)

**Key constraint:** This service lives OUTSIDE the Rails game engine. It is not embedded
in game logic and does not make real-time adjustments autonomously. All suggestions require
explicit game admin approval before application.

```
Simulation telemetry (Virtual Ledger trends, shortage frequency, import rates)
  → Market Calibration Service (external analytics layer)
    → Analyzes drift against design intent baselines
      → Generates parameter adjustment suggestions (JSON)
        → Game admin review interface
          → Admin approves/rejects
            → Approved changes applied to AI Manager constants
```

**Strategy pattern (per Gemini proposal):**
- Development: `MockCalibrator` — deterministic, predictable, no external dependencies
- Production: `LLMCalibrator` — calls local Ollama instance or cloud model for analysis
- Validation: All suggested adjustments validated against schema before admin review
- No autonomous application — human-in-the-loop always required

---

## What This Is Not

- Not a replacement for `AIManager::StrategySelector` or any existing AI Manager service
- Not an embedded Ollama API call inside Rails game logic
- Not autonomous market intervention — suggestions only, admin approves
- Not a Phase 4/5 concern — current manual calibration is sufficient at Luna scale
- Not a new market balancing mechanism — Virtual Ledger + EAP price ceiling handle that

---

## Architectural Constraints to Respect

- Virtual Ledger and EAP price ceiling are the canonical market balancing mechanisms
- AI Manager escalates to game admins on critical Virtual Ledger — that escalation path
  is where calibration suggestions would surface, not as a separate interrupt
- Embedding LLM calls in production game logic creates hard runtime dependency on local
  infrastructure — avoid
- All parameter changes must go through game admin review — no autonomous adjustment

---

## When This Becomes Actionable

- Phase 5 simulation is running and producing real telemetry data
- Multiple settlements are online and manual calibration is showing strain
- Virtual Ledger health monitoring is mature enough to produce meaningful drift signals
- Game admin review tooling exists to act on suggestions efficiently

Minimum viable trigger: Phase 6 with 3+ active settlements and recurring manual
calibration sessions needed per week.

---

## Related Files

- `research/LUNA-MVP-SIMULATION-DESIGN.md` — Virtual Ledger diagnostic purpose
- `research/LUNA-MVP-SIMULATION-DESIGN-ADDENDUM-2026-06-11.md` — market dynamics context
- Placeholder task: `tasks/phase6+/2026-06-11-MEDIUM-RESEARCH-AI-MARKET-CALIBRATION-SERVICE.md`
