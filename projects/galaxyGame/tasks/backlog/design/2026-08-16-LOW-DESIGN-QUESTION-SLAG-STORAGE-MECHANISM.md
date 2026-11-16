---
status: backlog
priority: LOW
type: design-question
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: false
created: 2026-08-16
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
This is a DESIGN QUESTION, not an implementation task. Do not write code, do not
create data files, do not run RSpec. This task's only output is a written design
decision added back into this file — no Step 0 git-mv-to-active choreography
needed for a design-only session; a human (Tracy) or Claude drives this, not a
standard implementation agent.
```

---

# DESIGN QUESTION: Slag Storage Mechanism in Hollowed Low-Gravity Cavities

**Status**: BACKLOG — unresolved design question, not yet actionable
**Priority**: LOW
**Type**: design-question
**Created**: 2026-08-16

---

## Context

This question surfaced during a brainstorm about the tug/cycler hollowing-and-relocation
mechanic (see [[tug-cycler-hollowing-operations]] for the full sequence — tug hollows
Phobos/Deimos using slag as propellant to reposition them, depot comes online first for
gas processing/docking/refueling, shipyard conversion follows via cycler-delivered units,
same pattern repeats for 2 belt-sourced objects delivered to Venus).

On Luna, propellant/gas storage uses surface piles — established, working pattern. That
doesn't work here: **Phobos, Deimos, and any small captured asteroid have too little
gravity for pile-based storage to function.** Something else is needed for slag storage
inside the hollowed cavity.

Two rough directions were floated during the brainstorm, neither decided:
- Slag stored in **bundles attached to the cavity walls** — allows the material to be
  managed/moved deliberately
- Slag simply **floating loose** inside the cavity — unclear how this would be
  contained, retrieved, or prevented from drifting out an opening

Explicitly not resolved in the brainstorm. This task exists so the question isn't lost
before a real design session happens.

## Problem Statement

Determine a plausible, game-consistent mechanism for storing bulk processed slag
material inside a hollowed low-gravity cavity (moon or captured asteroid), given that
Luna's surface-pile storage pattern doesn't work without meaningful gravity.

Needs to answer, at minimum:
- How is slag physically contained inside the cavity (walls, netting, magnetic/electrostatic
  containment, sealed sub-chambers, something else)?
- How is it later retrieved/loaded for use as tug propellant?
- Does this differ for permanent depot storage vs. temporary staging before a tug departs?
- Any real-world microgravity bulk-material handling precedent worth drawing from
  (e.g. real proposals for lunar regolith/asteroid material handling in low-g)?

## Gotchas

- This is **speculative game design**, not physics simulation — the goal is a
  mechanism that's internally consistent and game-fun, not a rigorous orbital-mechanics
  derivation. Don't over-engineer a "correct" physics answer if a simpler game-consistent
  one serves the same purpose.
- Whatever gets decided here will need to be reflected consistently across every future
  tug/cycler hollowing task (Phobos, Deimos, the 2 Venus-delivered belt objects, and any
  future site) — so this should be settled once, as a reusable pattern, even though
  individual site task files are being built one at a time rather than as a formal spec.
- Don't let this block Phobos (or whichever site is worked first) — a placeholder/minimal
  storage assumption can be used for the first real task file if this design question
  isn't resolved yet; this file exists precisely so the placeholder doesn't get forgotten
  and treated as final.

## Acceptance Criteria

- [ ] A specific storage mechanism is decided and documented (not left as open options)
- [ ] Mechanism explains containment, retrieval, and any staging/permanent distinction
- [ ] Decision recorded back into [[tug-cycler-hollowing-operations]] (or wherever the
      broader mechanic ends up formally documented) so future site task files can
      reference it directly instead of re-deriving it
- [ ] Confirmed as usable/consistent for at least the two known upcoming sites
      (Deimos, and the belt-to-Venus operation) — not just Phobos-specific

## Stop Conditions

- N/A — this is a design discussion, not an implementation task with escalation triggers.

## Dependencies
**Blocked by**: none
**Blocks**: none directly, but should ideally resolve before the first hollowing task
  (likely Phobos) reaches its own storage-mechanism details — see Gotchas above for the
  fallback if it doesn't.
**Related**: [[tug-cycler-hollowing-operations]] — full mechanic context

---

## Resolution
*Fill in once the design session happens*

**Decided by**: [name]
**Decision date**: YYYY-MM-DD
**Mechanism chosen**:
**Reasoning**:
