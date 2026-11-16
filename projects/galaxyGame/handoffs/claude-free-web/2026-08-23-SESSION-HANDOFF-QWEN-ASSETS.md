# Handoff: Asset Pipeline / Cycler Construction Ecosystem — 2026-08-24

**For:** Qwen (planning agent role)
**Context:** A long ChatGPT design session tonight (2026-08-23/24) covered the shared-catalog
construction-ecosystem strategy for depots/shipyard/tug/cycler assets. This is a design/creative
thread, not a code task — but Qwen should coordinate with the ChatGPT asset-gen session where
useful (repo verification, drafting blueprint stubs) and can pick up loose ends independently
where they don't require ChatGPT's creative input.

Full detail logged in [[asset-pipeline]] memory file — this handoff summarizes the state and
flags what's actionable now vs. what's still open.

---

## What's confirmed/decided (don't re-litigate)

1. **Construction sequence**: LEO Depot → L1 Depot → Shipyard Station → Tug → Gen-1 Cycler.
   Depots come FIRST and drive the initial shared catalog — the cycler does not dictate it.
2. **Manufacturing split**: Luna 3D-prints bulk structural mass (I-beams, panels, decking,
   radiation shielding) from depleted regolith and SHIPS it to orbit. Earth supplies ALL
   factory-built precision equipment (docking, airlocks, power, thermal, electronics, life
   support, comms/nav). Earth does NOT supply structural mass in this model — economic driver
   is Luna's shallow gravity well making Luna-shipped-to-orbit cheaper than Earth-imported.
3. **Structural kit concept**: I-beam + panel are simple, generic, bolt-together components —
   near-universal across depot/shipyard/tug/cycler, refining through Mk1→Mk4 tiers (visual
   reference already exists from a 2026-07-26 session). This is likely the single best
   candidate for the FIRST real component_blueprint + Visual Definition, since it's
   already fully Lunar (no provenance ambiguity) and has an existing visual reference.
4. **Classification rule**: sort components by REUSABLE INDUSTRIAL COMPONENT vs
   MISSION-SPECIFIC ASSEMBLY, not by "which vehicle it's on." Reusable: structural members/
   panels, mounting frames, pressure-hull sections, docking adapters, airlocks, power gen/
   distribution, thermal/radiators, utility manifolds, comms/nav, propellant tanks, cargo
   handling, radiation shielding, structural junctions. Mission-specific (mostly cycler-only,
   not deleted, just later): main propulsion cluster, transit-specific habitation, long-
   duration life support, cycler-specific docking/transfer architecture.
5. **Provenance/naming rule**: one conceptual component name (e.g. `docking_adapter`) with
   manufacturing origin/tech level as a PROPERTY of the manufactured instance — NOT separate
   blueprints per origin (`lunar_docking_adapter.json` + `earth_docking_adapter.json`) UNLESS
   the physical designs are genuinely substantially different. Blueprint schema must be able
   to express "same role/interface, different manufactured component" for cases where they
   really do diverge.
6. **Image output location CONFIRMED**: `data/images/<category>/`, mapped into the docker
   container. This is the real, actively-used convention (verified via `ls -la`), not just an
   assumption — matches the 2026-08-02 mount-fix incident.
7. **Render standard**: GalaxyGame Production Asset Render Template v1.0 — top-down
   orthographic, single object ~75% canvas, transparent 1024×1024 PNG, only {{UNIT_NAME}},
   {{FUNCTIONAL_ROLE}}, {{RECOGNITION_FEATURES}} vary per asset (everything else inherited
   from the assigned Visual Profile, e.g. `precision_industrial_v1`).

## What's flagged/open — needs a decision or verification, not yet resolved

1. **`ASSET_PROMPT_COMPILER_CONTRACT.md`** — dispatched to Ryzen tonight, still running as of
   this handoff. Check on its status first. Once it returns, cross-check its "Required Fields"
   section against item #5 above (component-identity vs manufactured-variant) — the contract
   may need an explicit `role`/`interface` concept, not just a flat provenance property.
2. **Design/ directory restructure proposal** (visual_profiles/, material_profiles/,
   render_profiles/, prompt_templates/) — ChatGPT revived Material/Render Profile concepts
   that were explicitly DEFERRED on 2026-07-28 pending "a real need." Tonight's cycler
   catalog work may BE that real need (multiple Lunar/Earth/Hybrid components needing
   consistent material language) — but this should be an explicit re-opening decision with
   stated reasoning, not silently adopted. Also unresolved: why `prompt_templates/` is
   plural when exactly one canonical template currently exists.
3. **Pipeline diagram discrepancy**: one diagram ends "Generated Production Assets → Game +
   Documentation" as joint consumers — not fully reconciled against the 2026-08-04 Production/
   Presentation split (Production = one render for the game; Presentation/docs = separate
   concern, not assumed to share the same output by default). Needs clarifying with ChatGPT
   directly: does "documentation" mean reusing the same production render, or a separate
   presentation-tier output?
4. **`cycler_mars_constructor` operational_data file is CONFIRMED STALE** — it has the cycler
   performing hollowing operations, contradicting the current established design (tug does
   hollowing, per [[tug-cycler-hollowing-operations]]). Low-priority cleanup candidate: flag
   for archiving or updating whenever operational_data is next touched. NOT a blocker.
5. **`cycler_belt_operations` — less obviously stale** (no hollowing_equipment listed) but not
   fully confirmed clean either. Worth a closer look if anyone works on cycler variants next.
6. **`base_cycler` base_craft blueprint propulsion question**: as written, compatible_units
   only lists `ion_engine`, blueprint materials only include `ion_thruster_components`, no
   nuclear reference anywhere. Tracy expected nuclear propulsion given the ship's scale (550m,
   600,000kg) but the file as written is ion-only. Ion propulsion is physically plausible for
   a cycler (continuous low-thrust vs high-thrust burns) — this may be correct-as-designed,
   not a gap. WORTH VERIFYING DIRECTLY rather than assuming either way: was ion-only
   intentional, or should nuclear_thermal_rocket_engine also be a compatible_unit?
7. **Recurring `prepare_kinetic_hammer` mission-phase step** appears identically in both
   cycler_mars_constructor and cycler_belt_operations — likely a real intentional mechanic
   (possibly mass-driver-style resource delivery) but not yet explained. Worth asking about
   directly next time this area comes up.
8. **Propulsion blueprint directory** confirmed via `ls -la`: 11 active engine types (basic,
   ion, capture thruster, direct fusion drive, ethane rocket, hydrolox, liquid rocket, methane,
   nuclear thermal, precision orbit control, slag propulsion) + `raptor_engine_bp.json` which
   is explicitly REFERENCE-ONLY, not a real in-game option — don't treat it as selectable.

## Next actionable step (once ChatGPT session resumes)

Work out the **LEO Depot's component list from scratch** (not derived from the cycler's
12-part breakdown) — this is the confirmed next design target. Once a first candidate list
exists, the I-beam/panel structural kit is likely ready to become the first real
`component_blueprint.json` + Visual Definition pair generated against the actual render
template (both are Lunar-only, no provenance ambiguity, existing visual reference already
approved).

## Housekeeping

`/areas/asset-pipeline.md` memory file is near its size cap (45.8K/49K) — needs a
consolidation/split pass next session (likely splitting tonight's construction-ecosystem/
cycler-variant content into its own file, since it's grown into a distinct topic from the
original render-template/visual-profile content the file was created for).