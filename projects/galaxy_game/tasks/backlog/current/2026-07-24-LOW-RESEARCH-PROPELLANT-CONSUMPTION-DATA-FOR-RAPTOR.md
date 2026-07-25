---
status: backlog
priority: LOW
type: research
system_domain: CRAFT/EXHAUST
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## Task: Find Real Blueprint Propellant Consumption Data for Raptor-Class Engines

### Context
`galaxy_game/app/models/craft/harvester.rb` line 127 uses a documented placeholder:
```ruby
propellant_consumed = (extraction_rate || 100) * 0.1
```
Comment says: "This is still a design parameter — real propellant tracking requires craft blueprint data"

The EXHAUST_COMPOSITION constants use real Starship Raptor stoichiometry ✓, but the scalar multiplier (0.1) has no basis in published Raptor performance data.

### Research Goal
Find publicly available propellant consumption rates for SpaceX Raptor engines and determine what value should replace `0.1`.

### Sources to Check
- SpaceX public Raptor specs (thrust, Isp, chamber pressure, propellant flow rate)
- Starship/Super Heavy technical documentation
- Any published mission profiles with propellant burn rates
- Compare against our `extraction_rate` field — is it analogous to thrust? Flow rate? Something else?

### Deliverable
One sentence: the correct multiplier value (or range) and its source, ready to paste into harvester.rb.

### Notes
- This is a LOW-priority research task — the current 0.1 is an honest placeholder with documentation
- The real blocker was fabricated data without flagging; that's resolved
- This prevents the placeholder from silently becoming "real" in future iterations
