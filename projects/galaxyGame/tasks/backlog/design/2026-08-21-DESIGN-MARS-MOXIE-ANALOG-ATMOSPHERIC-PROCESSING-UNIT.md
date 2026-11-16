---
status: backlog
priority: LOW
type: architecture
system_domain: AI_MANAGER | UNITS
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-08-21
updated: 2026-08-21
estimated_effort: TBD (design only)
blocker_for: []
depends_on: []
---

# DESIGN: Mars MOXIE-Analog Atmospheric Processing Unit

**Status:** BACKLOG (design capture only — not dispatch-ready, no implementation scope yet)
**Created:** 2026-08-21 by Tracy
**Type:** Design intent captured before Mars operations are built out

---

## Context

Mars's O2/buffer-gas supply doesn't work like Luna's. Luna has no free volatiles and self-fulfills O2 shortfalls by dispatching a regolith harvester through the existing TEU→PVE chain. Mars has a thin CO2 atmosphere, so its equivalent unit works differently — captured here before Mars operations are actually built, so this design intent isn't lost.

---

## Design Intent (Tracy, captured 2026-08-21)

### Unit Characteristics
- A **MOXIE-analog unit** is standard baseline Mars settlement infrastructure — deployed everywhere, much like a heat pump or AC unit on Earth, not a rare/special deployment.
- The unit pulls in Mars atmosphere, compresses it, and runs it through a **gas separator**.
- Gas separator follows the same "generic multi-output unit" pattern as Luna's regolith gas separator and Venus HLT's CO2 splitter — **not** a single pure-output device.

### Outputs (from one intake)
1. **O2** — produced by MOXIE component from CO2, used for habitat breathing
2. **N2 + trace gases** — captured from the same intake, stored as buffer gas for habitat pressurization
3. Both outputs are **on-site and continuous/passive** — no harvest-and-transport step

### Escalation Trigger (Different from Luna)
- Even with standing local production, shortfalls are still possible:
  - Leaks
  - Unit damage
  - Population growth outpacing current on-site capacity
- Mars's escalation trigger is therefore **about scaling/reinforcing local production capacity** or covering a temporary shortfall ("top off local supplies")
- **NOT** "go get raw material from somewhere else" (Luna's model)

### Implication for AI Manager Escalation Logic
- The general escalation-ladder logic should stay **flexible/per-body**, not assume Luna's harvest-dispatch model universally.
- Mars needs a genuinely different self-fulfillment branch: **build/deploy more capacity**, not dispatch a harvester.

---

## Not Yet Decided / Left for Whoever Picks This Up

- Exact blueprint/unit name, capacity numbers, power draw — mark [FILL IN], needs terminal access to check existing conventions (e.g., does something like this already exist under a different name?)
- Whether N2/buffer-gas storage lives in the same unit or a paired one
- How the escalation ladder's "scale up capacity" branch actually gets modeled in code:
  - New unit deployment order?
  - Capacity-expansion job?
  - Something else?

---

## Related to Current Work

This is **NOT** part of the active Luna oxygen diagnostic task. The current fixture-bundle item #9 fix is Luna-specific (regolith → TEU → PVE trace). This Mars design note is for whenever Mars's own oxygen handling gets built out — keeping the escalation-ladder code generic/pluggable per body from the start, rather than hardcoding Luna's chain and having to retrofit Mars later.

---

## Handoff Summary
DESIGN CAPTURE: MOXIE-analog unit on Mars produces O2 + N2/buffer-gas from atmosphere via gas separator (multi-output pattern). Escalation is about capacity scaling, not raw material sourcing. Keep AI Manager escalation logic per-body flexible.
