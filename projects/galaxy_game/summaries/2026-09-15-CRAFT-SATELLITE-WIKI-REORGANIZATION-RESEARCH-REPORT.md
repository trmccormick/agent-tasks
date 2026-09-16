# Craft & Satellite Documentation Research Report

**Date**: 2026-09-15  
**Scope**: Read-only documentation research from `docs/wiki_reorganization/`  
**Purpose**: Evidence-based inventory for LDC crypto-mining satellite wiki entry planning  
**Mode**: Read-only — no files modified, created, staged, committed, or deleted

---

## A. Scope and Source Boundary

### Wiki Reorganization Root Reviewed
`/Users/tam0013/Documents/git/galaxyGame/docs/wiki_reorganization/`

### Sources Included (Reorganized Wiki)
All documents within `wiki_reorganization/` across its subdirectories: `inventory/`, `analysis/`, `proposals/`, `phase2_alignment/`, `phase3_alignment/`, `phase4/`, and `economy/`.

### Legacy/Original Wiki — Excluded as Authoritative Target
- `docs/wiki/` (original wiki pages like `Celestial-Systems.md`, `Financial-Engine.md`, `Logistics-and-Hauling.md`) — referenced only as historical sources in Phase 4 canonical index entries
- `docs/architecture/` (pre-reorganization architecture docs) — referenced only as source material for the reorganization proposals
- `docs/agent/`, `docs/planning/`, `docs/developer/` — development artifacts, not wiki content

### Supporting References (Outside Wiki Reorganization, Used Only for Evidence Verification)
- `docs/GUARDRAILS.md` §8 (economic guardrails cited in economy docs)
- `docs/DECISIONS.md` (economic constants referenced in economy overview)
- Code model paths referenced within wiki docs (e.g., `app/models/craft/base_craft.rb`, `app/models/units/base_unit.rb`) — cited only where wiki docs explicitly reference them as evidence

---

## B. Reorganized Wiki Inventory

### All Pages, Indexes, and Navigation Files in wiki_reorganization/

| Path | Title/Role | Relevance to Craft/Satellite |
|------|-----------|----------------------------|
| `README.md` | Phase 1 discovery overview | Low — mentions craft in architecture reconstruction summary |
| `inventory/DOCUMENT_INVENTORY.md` | 368+ document catalog | Medium — lists craft-related models/services |
| `inventory/DOCUMENT_AUTHORITY_MAP.md` | Authority classification | Low — status trackers, not craft content |
| `analysis/CONFLICT_REPORT.md` | 15 identified conflicts | Low — no craft-specific conflicts listed |
| `analysis/CORE_CONCEPT_MAP.md` | 20+ concepts mapped to code owners | **High** — Craft/Ship concept map with namespace evidence |
| `analysis/TERMINOLOGY_MAP.md` | 22 terminology inconsistencies | **High** — Craft vs Ship vs Vessel; Unit vs Module; Component vs Module |
| `analysis/ARCHITECTURE_RECONSTRUCTION.md` | Reconstructed architecture | **High** — Craft::BaseCraft hierarchy, Satellite in celestial bodies, Units hierarchy |
| `proposals/PROPOSED_DOCUMENTATION_STRUCTURE.md` | Proposed 13-folder wiki org | **High** — craft_architecture.md proposed location; unit_architecture.md |
| `phase2_alignment/CORE_GAME_LOOP_STATUS.md` | Core game loop status | **Medium** — Craft deployment step, BaseCraft hierarchy CONFIRMED |
| `phase2_alignment/BACKLOG_REORGANIZATION_PROPOSAL.md` | Backlog reorg proposal | Low — mentions base_craft.rb archival |
| `phase3_alignment/PHASE3_CANONICAL_ALIGNMENT_REPORT.md` | Canonical alignment report | Medium — confirms craft hierarchy, notes AI Manager doc gap |
| `phase3_alignment/RESOLVED_CONFLICTS.md` | 6 resolved conflicts | Low — no craft-specific conflicts |
| `phase3_alignment/TRUE_BLOCKERS_ONLY.md` | Zero true blockers | Low — none craft-related |
| `phase3_alignment/WIKI_ALIGNMENT_REVIEW.md` | Wiki structure evaluation | **High** — Section 05_Units_and_Craft proposed with craft_types.md |
| `phase3_alignment/DOCUMENTATION_UPDATE_PLAN.md` | Doc update plan | Low — D1 AI Manager gap, not craft-specific |
| `phase3_alignment/OPEN_DESIGN_DECISIONS.md` | Open design decisions | Low — TL/MK relationship only |
| `phase4/README.md` | Phase 4 overview | Low — structural only |
| `phase4/CANONICAL_DOCUMENT_INDEX.md` | One authoritative page per topic | **High** — CRAFT canonical page defined; UNIT supporting page |
| `phase4/WIKI_SITE_MAP.md` | Complete navigation hierarchy | **High** — Section 10 Transportation with CRAFT canonical page |
| `phase4/DOCUMENT_CLASSIFICATION.md` | Every doc classified | **High** — CRAFT_OPERATIONAL_EVOLUTION.md = Redirect; skimmer_craft_intent.md = Canonical |
| `phase4/DOCUMENT_RELOCATION_PLAN.md` | Doc relocation plan | **High** — CRAFT section with source→target mappings |
| `phase4/MISSING_WIKI_PAGES.md` | Pages that should exist | **High** — CRAFT listed as P1 missing page; TRANSPORTATION_OVERVIEW as P0 |
| `phase4/CONTRIBUTOR_GUIDE.md` | Wiki contributor guide | Medium — Section 10 Transportation = craft/stations/deployables |
| `phase4/ARCHIVE_PLAN.md` | Documents for archive | **High** — CRAFT_OPERATIONAL_EVOLUTION.md, skimmer_craft_intent.md, base_rig_intent.md all superseded→STATIONS |
| `economy/01-overview-and-design.md` | Economy overview | Medium — mentions units/modules/rigs in launch cost context |
| `economy/02-currencies-and-accounts.md` | GCC/USD currencies | **High** — LDC crypto-mining satellite terminology, mint authority |
| `economy/03-market-and-pricing.md` | Market/pricing | Low — transport costs only |
| `economy/04-bonds-and-financing.md` | Bonds/GCC minting | **High** — GCC mining satellite deployment, financing, mining rate |
| `economy/05-launch-and-operational-fees.md` | Launch fees | Medium — launch cost calculation mentions units/modules/rigs |
| `economy/06-contracts-and-players.md` | Contracts/players | Low — docking_ports as special equipment; modular construction |
| `economy/AUDIT-ECONOMY-DOCS.md` | Economy docs audit | Low — structural audit |
| `economy/GAPS.md` | Economic documentation gaps | Medium — Gap I: GCC mining recalculate_stats/mine_gcc disconnection |

---

## C. Existing Craft and Satellite Statements

### Statement 1: "Craft" as Umbrella Term
- **Path**: `analysis/TERMINOLOGY_MAP.md` §11; `analysis/CORE_CONCEPT_MAP.md` Craft/Ship row
- **Heading**: "Craft vs Ship vs Vessel" / "Craft / Ship"
- **Extract**: `"Craft" — app/models/craft/, docs/architecture/stations/CRAFT_OPERATIONAL_EVOLUTION.md — PREFERRED — canonical namespace`
- **Claim Type**: Terminology reference
- **Classification**: Confirmed current behavior
- **Evidence Status**: Code namespace CONFIRMED; `Craft::BaseCraft` hierarchy CONFIRMED in phase2_alignment/CORE_GAME_LOOP_STATUS.md
- **Note**: "Vessel" appears only in player-facing docs ("player-owned vessels"), not as a model term

### Statement 2: Craft::BaseCraft Subclasses
- **Path**: `analysis/ARCHITECTURE_RECONSTRUCTION.md` §Major Systems → Manufacturing Flow; `phase2_alignment/CORE_GAME_LOOP_STATUS.md`
- **Heading**: "Manufacturing Flow" / "What exists"
- **Extract**: `[CONFIRMED] Craft::BaseCraft subclasses (Harvester, Rover, Ship, Spaceship)`
- **Claim Type**: Current-behavior claim
- **Classification**: Confirmed current behavior
- **Evidence Status**: CONFIRMED by code and docs
- **Note**: Satellite subtypes mentioned in CORE_CONCEPT_MAP: "Craft have variant management... Satellite subtypes"

### Statement 3: Ship Exists in Two Locations (Conflict)
- **Path**: `analysis/TERMINOLOGY_MAP.md` §11
- **Heading**: "Craft vs Ship vs Vessel"
- **Extract**: "`Ship` exists in two places: `app/models/craft/ship.rb` — within the craft namespace; `app/models/ship.rb` — at root level (possibly legacy)"
- **Claim Type**: Terminology reference / Conflict flag
- **Classification**: Conflicting/stale claim
- **Evidence Status**: Requires code verification (is root-level ship.rb still active?)
- **Concern**: Namespace ambiguity — same entity type in two locations

### Statement 4: Satellite as Celestial Body Subtype
- **Path**: `analysis/ARCHITECTURE_RECONSTRUCTION.md` §Celestial Body System
- **Heading**: "Celestial Body System"
- **Extract**: `CelestialBody → Satellite (between Moon and MinorBody in hierarchy)`
- **Claim Type**: Definition / Architecture
- **Classification**: Confirmed current behavior
- **Evidence Status**: CONFIRMED — Satellite exists as a CelestialBody subtype in the celestial body hierarchy
- **Note**: This is a CELESTIAL BODY "Satellite" (moon-like natural object), NOT a spacecraft satellite. The wiki docs do not explicitly distinguish these two uses of "satellite."

### Statement 5: GCC Mining Satellite — Terminology Definition
- **Path**: `economy/02-currencies-and-accounts.md` §1 "Terminology: GCC Mining"
- **Heading**: "Terminology: 'GCC Mining'"
- **Extract**: `"GCC mining satellite" remains the established asset/lore name. "GCC mining" means LDC-authorized, processing-hardware-driven GCC issuance/settlement activity. It is not physical extraction, commodity production, or a full proof-of-work gameplay system.`
- **Claim Type**: Definition / Terminology reference
- **Classification**: Settled design intent
- **Evidence Status**: Explicitly documented in canonical economy doc; no code evidence cited for the satellite itself
- **Note**: This is the PRIMARY authoritative definition of "GCC mining satellite" in the reorganized wiki

### Statement 6: LDC as Sole Mint Authority
- **Path**: `economy/02-currencies-and-accounts.md` §1; `economy/04-bonds-and-financing.md` §6.3
- **Heading**: "GCC Identity" / "LDC as Sole Recipient"
- **Extract**: `"LDC (Luna Development Corporation) is the initial authorized issuer/mint"`, `All newly minted GCC flows exclusively to the LDC... No other entity can directly receive newly minted GCC.`
- **Claim Type**: Design intent
- **Classification**: Settled design intent
- **Evidence Status**: Explicitly documented; no code evidence for enforcement mechanism (see Gap H in GAPS.md)
- **Note**: GCC explicitly stated as "not physical material, commodity inventory, cargo, or a physical extraction output"

### Statement 7: Mining Satellite Operational Parameters
- **Path**: `economy/04-bonds-and-financing.md` §6.1 "Narrative"
- **Heading**: "GCC Minting & Pre-Seeding Architecture"
- **Extract**: `a base rate of 1000 GCC per hour, plus hardware-dependent bonuses from fitted computer/GPU components — actual output varies by loadout, not fixed. The base-rate-only yield is 6,000 GCC per 6-hour cycle`
- **Claim Type**: Current-behavior claim / Design intent
- **Classification**: Settled design intent (with implementation gap)
- **Evidence Status**: Documented in wiki; GAPS.md Gap I notes `recalculate_stats` vs `mine_gcc` disconnection — documentation describes recalculate_stats behavior, not mine_gcc behavior
- **Note**: "fitted computer/GPU components" and "loadout" are the only rig/fitting-related terms found

### Statement 8: GCC Mining Satellite as Deployable Asset
- **Path**: `economy/04-bonds-and-financing.md` §3.1, §6.1
- **Heading**: "Launch Service Bonds" / "Narrative"
- **Extract**: `A GCC mining satellite is deployed into a valid orbital location (orbital, planetary orbit, or Lagrange point). Once in position, the satellite begins autonomous GCC mining using onboard computational units.`
- **Claim Type**: Design intent
- **Classification**: Settled design intent
- **Evidence Status**: Explicitly documented; financing structure with bond example provided
- **Note**: Satellite is described as a deployable asset requiring launch financing — but NOT explicitly called "first deployable asset"

### Statement 9: CRAFT as P1 Missing Wiki Page
- **Path**: `phase4/MISSING_WIKI_PAGES.md` §Transportation Section
- **Heading**: "Transportation Section (New Pages)"
- **Extract**: `CRAFT | P1 | Craft types and capabilities`
- **Claim Type**: Planning intent
- **Classification**: Deferred future design
- **Evidence Status**: The CRAFT wiki page does NOT yet exist in the reorganized wiki — it is proposed but not created
- **Note**: TRANSPORTATION_OVERVIEW is P0 (more urgent); CRAFT is P1

### Statement 10: CRAFT Canonical Page in Site Map
- **Path**: `phase4/WIKI_SITE_MAP.md` §10 Transportation
- **Heading**: "Transportation"
- **Extract**: `[CRAFT.md] | Canonical | Craft types and capabilities`
- **Claim Type**: Planning intent / Proposed structure
- **Classification**: Deferred future design
- **Evidence Status**: Proposed canonical page; does not exist yet as a file
- **Note**: Site map shows CRAFT as canonical within Transportation section

### Statement 11: Unit Model — Deployable Entities
- **Path**: `analysis/ARCHITECTURE_RECONSTRUCTION.md` §Settlement System
- **Heading**: "Settlement System"
- **Extract**: `Units::BaseUnit (deployable entities, has_many :units) — Robot, Habitat, Extractor, Fabricator, Processor, Battery, Computer, LifeSupport, Propulsion, Storage, PlanetaryUmbilicalHub`
- **Claim Type**: Definition / Current-behavior claim
- **Classification**: Confirmed current behavior
- **Evidence Status**: CONFIRMED by code and docs
- **Note**: Units are "deployable entities" — this is the primary deployable classification in the wiki

### Statement 12: Component vs Module Distinction
- **Path**: `analysis/TERMINOLOGY_MAP.md` §2
- **Heading**: "Component vs Module"
- **Extract**: `Component: Smaller, used in manufacturing chain (raw → processed → component → blueprint → assembly). Module: Larger, used for structure/settlement construction. The distinction between "component" and "module" is not clearly documented.`
- **Claim Type**: Terminology reference / Conflict flag
- **Classification**: Unverified claim / Insufficient evidence
- **Evidence Status**: Wiki identifies the gap; recommends keeping both terms but notes lack of explicit documentation
- **Note**: This is a KNOWN gap that should be addressed before satellite documentation

### Statement 13: Rig Terminology — Superseded Document
- **Path**: `phase4/ARCHIVE_PLAN.md` §Merged Into Transportation
- **Heading**: "Superseded Documents"
- **Extract**: `rig_system.md → STATIONS (superseded/)`
- **Claim Type**: Archive classification
- **Classification**: Confirmed current behavior (document is superseded)
- **Evidence Status**: rig_system.md is classified as superseded; content merged into STATIONS
- **Note**: No active rig documentation exists in the reorganized wiki — only archive references

### Statement 14: Recommended Fit / Loadout References
- **Path**: `economy/04-bonds-and-financing.md` §6.1
- **Heading**: "GCC Minting & Pre-Seeding Architecture"
- **Extract**: `hardware-dependent bonuses from fitted computer/GPU components — actual output varies by loadout, not fixed`
- **Claim Type**: Design intent
- **Classification**: Settled design intent (sparse)
- **Evidence Status**: Only mention of "loadout" in wiki_reorganization; no definition of recommended_fit found
- **Note**: "fitted" and "loadout" are used but never defined as formal terms

### Statement 15: Deployable Terminology
- **Path**: `analysis/ARCHITECTURE_RECONSTRUCTION.md`; `phase2_alignment/CORE_GAME_LOOP_STATUS.md`
- **Heading**: "Settlement System" / "Step 4: Transportation / Deployment"
- **Extract**: `Units::BaseUnit (deployable entities...)`, `Deploy craft to target bodies`, `Craft deployment and orbital mechanics`
- **Claim Type**: Terminology reference / Current-behavior claim
- **Classification**: Confirmed current behavior
- **Evidence Status**: Units explicitly called "deployable entities"; craft deployment is a game loop step
- **Note**: No distinction between "deployable asset" (satellite) and "deployable entity" (unit) in wiki docs

---

## D. Crypto-Mining Satellite Findings

### What Is Explicitly Documented

1. **Name/terminology**: "GCC mining satellite" is the established asset/lore name — explicitly stated in `economy/02-currencies-and-accounts.md`
2. **Nature**: It is processing-hardware-driven GCC issuance infrastructure, NOT physical extraction or commodity production — explicitly stated
3. **LDC ownership/mint authority**: LDC is the initial authorized issuer/mint; all newly minted GCC flows exclusively to LDC — explicitly stated in two economy docs
4. **Mining rate**: 1000 GCC/hour base rate, 6-hour cycles, 6000 GCC/cycle, 24000 GCC/day/satellite — explicitly documented with calculation
5. **Hardware bonuses**: Fitted computer/GPU components provide variable output beyond base rate — mentioned but not defined
6. **Deployment location**: "orbital, planetary orbit, or Lagrange point" — explicitly stated
7. **Financing structure**: Launch service bonds (180-day maturity, 5% interest) and GCC mining bonds (24-month term, collateralized by satellite asset) — explicitly documented with JSON examples
8. **GCC is virtual currency**: "not physical material, commodity inventory, cargo, or a physical extraction output" — explicitly stated

### What Is Implied But Not Explicit

1. **Satellite as craft subtype**: The wiki places "Satellite" in the CelestialBody hierarchy (natural moon-like object) AND mentions "Satellite subtypes" under Craft in CORE_CONCEPT_MAP. The relationship between a spacecraft satellite and the celestial body Satellite is NEVER clarified.
2. **First deployable asset status**: The satellite is described as being deployed first (bootstrap phase), but is never explicitly called "the first deployable asset" or given priority ordering among deployables.
3. **Ownership/operation details**: LDC is mint authority, but who physically operates/maintains the satellite? Is it AI Manager controlled? Player-operable? Not stated.
4. **Rig/fitting system**: "fitted computer/GPU components" and "loadout" are used but never defined. The relationship between rigs, fittings, loadouts, and the satellite's mining output is undefined.
5. **Physical vs virtual boundary**: While GCC is explicitly virtual, the satellite itself — is it a physical spacecraft model, a virtual compute node, or both? Not clarified.
6. **Maintenance/repair/decommissioning**: No documentation of what happens when a mining satellite fails, needs repair, or reaches end-of-life.

### What Is Absent

1. **Authoritative definition of "craft"**: No canonical wiki page for CRAFT exists (it's a P1 missing page). The proposed site map shows CRAFT.md as canonical but the file does not exist.
2. **Satellite explicitly categorized as craft subtype**: While CORE_CONCEPT_MAP mentions "Satellite subtypes" under Craft, no explicit taxonomy statement exists.
3. **Deployable craft distinguished from other deployables**: No section distinguishes deployable satellites from deployable units, station modules, or static infrastructure.
4. **Rig vs recommended_fit distinction**: Neither term is defined in the reorganized wiki. The archive plan shows `rig_system.md` as superseded into STATIONS, but no active rig documentation exists.
5. **Physical mining/extraction vs virtual GCC issuance boundary for the satellite**: While GCC is explicitly virtual, the satellite's physical capabilities (power, compute, cargo capacity) are not documented in the wiki.
6. **LDC-controlled issuance infrastructure vs generic compute infrastructure**: The wiki states LDC controls minting but doesn't document what makes the satellite's infrastructure LDC-controlled versus any entity's compute-capable infrastructure.
7. **Craft construction/repair/refit/maintenance states**: No documentation of craft lifecycle states (under construction, under repair, refit, normally operating).

### What Conflicts

1. **Satellite = CelestialBody vs Satellite = Craft subtype**: The architecture reconstruction places "Satellite" as a CelestialBody hierarchy node (between Moon and MinorBody). The CORE_CONCEPT_MAP mentions "Satellite subtypes" under Craft. These are two different meanings of "satellite" with no disambiguation.
2. **Ship in two namespaces**: `Craft::Ship` and root-level `Ship` — wiki identifies this as a conflict requiring investigation.
3. **Mining rate discrepancy**: Economy docs state 1,000,000 GCC/cycle daily from LDC mining satellites (02-currencies-and-accounts.md §Administrators) but also 1000 GCC/hour per satellite = 24,000 GCC/day/satellite (04-bonds-and-financing.md). The relationship between "1M GCC/cycle" and per-satellite output is unclear.

### What Requires Later Code/Data Verification

1. **recalculate_stats vs mine_gcc disconnection**: GAPS.md Gap I explicitly documents that `recalculate_stats` (base_craft.rb line 372) computes mining rate but `mine_gcc` independently aggregates fitted units — no data flow between them. Documentation describes recalculate_stats behavior, not actual runtime behavior.
2. **Whether GCC mining enforcement exists**: GAPS.md Gap H notes "No enforcement of the documented daily issuance schedule" — the wiki documents 1M GCC/cycle but no code enforces it.
3. **Halving schedule and supply cap**: Both marked "TBD" in the wiki — design intent without implementation.
4. **Whether satellite operational data JSON exists**: Economy docs reference `data/json-data/operational_data/crafts/space/satellites/crypto_mining_satellite_data.json` but this is a config file reference, not wiki documentation.

---

## E. Terminology Boundary Findings

### Craft vs Satellite
- **Status**: CONFLICTING / UNRESOLVED
- **Evidence**: "Satellite" appears as both a CelestialBody subtype (natural moon-like object) and a Craft subtype ("Satellite subtypes" in CORE_CONCEPT_MAP). No disambiguation exists. The wiki proposes a CRAFT canonical page that does not yet exist.
- **Gap**: No authoritative craft taxonomy exists in the reorganized wiki.

### Craft vs Unit/Module
- **Status**: PARTIALLY DOCUMENTED
- **Evidence**: Units are "deployable entities" (Robot, Habitat, Extractor, etc.) under Settlement→Units::BaseUnit. Modules are "building blocks for structures/settlements." Craft are "mobile vehicles" (Harvester, Rover, Ship, Spaceship). The scale distinction is implied but not explicitly documented.
- **Gap**: No explicit boundary between craft (mobile) and units (deployable but structure-associated).

### Craft vs Station
- **Status**: PARTIALLY DOCUMENTED
- **Evidence**: Stations are in Section 10 Transportation as a supporting page. Cyclers described as "evolution from simple transport vehicles to portable space stations." Orbital depots manage "constellations of docked structures." No explicit craft-vs-station boundary.
- **Gap**: Where does a craft end and a station begin? Cycler blurs this line.

### Deployable Asset vs Permanent Installation
- **Status**: IMPLICIT BUT NOT EXPLICIT
- **Evidence**: Units are "deployable entities." Worldhouses are explicitly NOT deployable ("structures built over natural terrain"). Satellite deployment is described in financing context but not classified as a deployable asset type.
- **Gap**: No taxonomy of deployable vs permanent entities.

### Rig vs Module/Unit/Component
- **Status**: ABSENT FROM REORGANIZED WIKI
- **Evidence**: `rig_system.md` is superseded→STATIONS in archive plan. "fitted computer/GPU components" and "loadout" appear in mining context but are undefined. No active rig documentation exists.
- **Gap**: Complete absence of rig terminology in the reorganized wiki.

### Rig vs recommended_fit
- **Status**: ABSENT FROM REORGANIZED WIKI
- **Evidence**: Neither "rig" nor "recommended_fit" is defined anywhere in wiki_reorganization. The user's planning intent notes that "recommended_fit is an NPC/test/reference configuration, not a rig or finalized player-fitting system" — but this is NOT in the wiki.
- **Gap**: Complete absence.

### Physical Extraction vs Virtual GCC Issuance
- **Status**: EXPLICITLY DISTINGUISHED (for GCC)
- **Evidence**: "GCC is technically digital/cryptographic, fiat-like virtual ledger currency... It is not physical material, commodity inventory, cargo, or a physical extraction output." GCC mining = "LDC-authorized, processing-hardware-driven GCC issuance/settlement activity. It is not physical extraction."
- **Note**: This distinction is well-documented for GCC. The satellite's PHYSICAL capabilities (power draw, compute hardware, orbital mechanics) are NOT documented.

### LDC-Controlled Issuance Infrastructure vs Generic Compute
- **Status**: IMPLICIT BUT NOT EXPLICIT
- **Evidence**: LDC is "sole mint authority" and "initial authorized issuer/mint." All GCC flows to LDC. But what makes the satellite's infrastructure LDC-controlled? Is it the operational data configuration? The bond structure? A code-level permission check? Not documented.
- **Gap**: No explanation of authorization mechanism for minting infrastructure.

### Craft Construction vs Normal Operations/Maintenance States
- **Status**: ABSENT
- **Evidence**: No documentation of craft lifecycle states (under construction, under repair, refit, maintenance, normally operating). Docking_ports mentioned as "special equipment" in contracts doc. UniversalDockingService CONFIRMED in code evidence.
- **Gap**: Complete absence of craft operational state taxonomy.

---

## F. Documentation Gaps and Review Questions

### Priority Gaps Blocking LDC Crypto-Mining Satellite Wiki Entry

1. **No authoritative CRAFT page exists** (P1 missing page). Without a craft taxonomy, the satellite's classification as a craft subtype cannot be authoritatively stated.
   - *Question for review*: Should the satellite be explicitly categorized as a craft subtype in the wiki? If so, should this be documented before or after the CRAFT canonical page is created?

2. **Satellite = CelestialBody vs Satellite = Craft subtype ambiguity**. The word "satellite" means two different things in the wiki with no disambiguation.
   - *Question for review*: Should the wiki use a distinct term (e.g., "spacecraft satellite," "orbital satellite," "GCC mining satellite") to disambiguate from celestial body satellites?

3. **No deployable taxonomy**. Units are "deployable entities" but no category exists for deployable craft or deployable infrastructure.
   - *Question for review*: Should "deployable asset" be a formal wiki category? If so, what entities belong in it?

4. **Rig/fitting/loadout terminology is absent** from the reorganized wiki. The satellite's mining output depends on "fitted computer/GPU components" and "loadout," but these terms are undefined.
   - *Question for review*: Should rig/recommended_fit documentation be restored to the reorganized wiki? If so, where does it belong (Manufacturing? Transportation? Craft?)?

5. **Mining rate discrepancy** between economy docs (1M GCC/cycle daily vs 24k GCC/day/satellite).
   - *Question for review*: Is "1M GCC/cycle" a system-wide total across multiple satellites, or is it a documentation error? What is the authoritative source?

6. **No craft operational state taxonomy**. No wiki documentation of craft states (under construction, under repair, refit, maintenance, normally operating).
   - *Question for review*: Should craft operational states be documented? Are they relevant to the satellite's GCC mining function?

7. **LDC minting authorization mechanism is undocumented**. The wiki states LDC is sole mint authority but doesn't explain HOW this is enforced (operational data config? code-level permission? bond structure?).
   - *Question for review*: What is the actual enforcement mechanism? Is it documented in operational data JSON, or does it require code evidence?

### Secondary Gaps

8. **Ship namespace ambiguity** (`Craft::Ship` vs root `Ship`) — identified as a conflict requiring investigation.
9. **Cycler-craft-station boundary** — cyclers described as "portable space stations" but no formal distinction from craft or station types.
10. **Physical capabilities of mining satellite** — power draw, compute hardware specs, orbital mechanics requirements are not documented in the wiki (only referenced via config file paths).

---

## G. Recommendation for Next Documentation Step

### Recommended: Option 3 — Evidence/Code/Data Extraction Before Any Wording Proposal

**Rationale**: Three critical gaps prevent accurate wiki prose for the LDC crypto-mining satellite:

1. **No CRAFT canonical page exists** — proposing satellite entry without a craft taxonomy is building on sand. The site map proposes CRAFT.md as canonical but it doesn't exist.
2. **Mining rate discrepancy** (Gap I in GAPS.md) between documentation and runtime behavior means any wiki statement about mining output would be unverified.
3. **Satellite = CelestialBody vs Satellite = Craft subtype ambiguity** must be resolved before the satellite can be authoritatively classified.

### Specific Extraction Tasks Needed Before Wording

1. **Verify code-level craft taxonomy**: Confirm `Craft::BaseCraft` subclasses and whether a `Craft::Satellite` or equivalent exists in the current codebase.
2. **Resolve mining rate discrepancy**: Check `economic_parameters.yml`, `gcc_sat_mining_deployment` task JSON, and `crypto_mining_satellite_data.json` for authoritative rates.
3. **Verify recalculate_stats vs mine_gcc runtime behavior**: Confirm whether the documented "base rate + fitted components" formula actually executes in `mine_gcc`.
4. **Check rig/recommended_fit documentation status**: Determine if any active wiki docs define these terms or if they were fully archived.
5. **Confirm satellite operational data JSON existence and content**: Verify `data/json-data/operational_data/crafts/space/satellites/crypto_mining_satellite_data.json` exists and contains the parameters cited in economy docs.

### Alternative (If Option 3 Is Deferred)

**Option 1 — Narrowly Scoped Satellite-Entry Alignment Proposal** could proceed IF limited to:
- Restating explicitly documented facts (LDC mint authority, GCC virtual nature, mining rate as documented)
- Marking all unverified claims as "requires code/data verification"
- Explicitly noting the satellite/CelestialBody ambiguity

But this approach would produce a wiki entry with significant caveats and would likely require revision after Option 3 is completed.

---

## Summary of Key Findings

| Finding | Status | Action Needed |
|---------|--------|---------------|
| GCC mining satellite terminology defined | Settled design intent | None — clear in economy docs |
| LDC sole mint authority | Settled design intent | Verify enforcement mechanism |
| GCC is virtual (not physical) | Settled design intent | None — explicitly stated |
| Mining rate documented | Settled design intent (with gap) | Resolve 1M vs 24k discrepancy |
| No CRAFT canonical page | Deferred future design | Create P1 page first |
| Satellite = CelestialBody ambiguity | Conflicting | Disambiguate terminology |
| Rig/recommended_fit absent from wiki | Absent | Restore or explicitly note absence |
| Deployable taxonomy absent | Absent | Define if needed for satellite |
| Craft operational states absent | Absent | Define if relevant |
| recalculate_stats vs mine_gcc gap | Unverified claim | Code/data verification required |

---

No repository files were modified.
No implementation task was dispatched.
No unresolved architecture was silently decided.
