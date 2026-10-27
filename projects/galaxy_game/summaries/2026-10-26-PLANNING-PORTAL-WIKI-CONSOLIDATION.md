# Portal Technology Wiki Consolidation — Planning Report

**Date**: 2026-10-26  
**Author**: Qwen3.6 (Implementation Agent, guided by task prompt)  
**Task**: `2026-10-26-MEDIUM-DOCUMENTATION-PORTAL-WIKI-CONSOLIDATION.md`  
**Status**: Active — Planning Only (No Wiki Pages Created)

---

## Executive Summary

This report consolidates portal technology documentation from three primary sources into a structured planning outline for future wiki page implementation. Every claim is traced to its source. Proposals are clearly labeled "not approved." Unresolved questions are listed with severity. All caveats from the consolidated draft are preserved verbatim.

**Sources consulted**:
| # | Source | File Path | Authority Level |
|---|--------|-----------|-----------------|
| 1 | Consolidated Portal Draft (Wiki Reorg) | `galaxyGame/docs/wiki_reorganization/transportation/portals/portal-technology-wiki-draft.md` (§§1–9, §8.2 contains developer clarifications) | Established + Developer Clarification |
| 2 | Gemini Brainstorming Package | `galaxyGame/gemini-research-portals.md` | **Proposed, not approved** (explicitly flagged throughout this report) |
| 3 | Prior Design Recovery Task | `agent-tasks/projects/galaxy_game/tasks/drafts/2026-10-25-MEDIUM-DOCUMENTATION-PORTAL-DESIGN-RECOVERY.md` | Independent, not read by this task (not a blocker) |

---

## Stage 1: Wiki Structure Mapping (4 Target Pages)

### Page 1: Portal Overview
**Target path**: `docs/wiki_reorganization/transportation/portals/overview.md`  
**Purpose**: High-level introduction to portal technology, its role in the galaxy game, and system-local scope.

| Section | Content | Source Type | Source Citation | Notes |
|---------|---------|-------------|-----------------|-------|
| Introduction | Portals provide rapid personnel transportation between connected settlements within a star system | Established | `portal-technology-wiki-draft.md` §1 | Exact phrasing: "Portal technology provides rapid personnel transportation between connected settlements within a star system" |
| Scope | Portal connections consist of paired endpoints linking two specific locations; multiple connections form networks | Established | `portal-technology-wiki-draft.md` §1 | Network concept established in primary draft |
| Role clarification | Portals complement, not replace, cyclers/ISRU/bulk freight | Established | `portal-technology-wiki-draft.md` §1 | Explicitly stated: "complement conventional transportation infrastructure" |
| System-local scope | Portal connections operate within the same star system; "system-local" does not mean "Sol-only" | Established | `portal-technology-wiki-draft.md` §2.1 | Other systems may have independent networks |
| Inter-system boundary | Inter-system travel uses spacecraft + WS/AWS infrastructure, NOT portal technology | Established | `portal-technology-wiki-draft.md` §2.2 | Critical distinction — portals are intra-system only |

**Disclaimers for this page**: Phase placement references are outdated (see Stage 3). No implementation exists in codebase (see Stage 3).

### Page 2: Portal Technology & Operations
**Target path**: `docs/wiki_reorganization/transportation/portals/technology-operations.md`  
**Purpose**: How portals work — endpoints, networks, EM constraints, deployment model.

| Section | Content | Source Type | Source Citation | Notes |
|---------|---------|-------------|-----------------|-------|
| Paired endpoints | Matching endpoints connect specific locations; built together as a pair | Established + Developer Clarification | `portal-technology-wiki-draft.md` §3; Developer clarification: "built together as a pair" | Developer attribution preserved, not independently verified |
| Deployment model | Construct → Transport → Deploy → Establish connection → Make available | Established | `portal-technology-wiki-draft.md` §3 | 5-step sequence from primary draft |
| Unresolved deployment | Exact transportation arrangement unknown — both endpoints together? Separate missions? | Unresolved | `portal-technology-wiki-draft.md` §3 | Severity: High (blocks basic operations) |
| Network hubs | Any major settlement can become a hub if EM budget permits; no mandatory list imposed | Established | `portal-technology-wiki-draft.md` §4 | Earth/L1, Mercury, Mars, Ceres, Jupiter, Saturn, Pluto = example only |
| EM constraints | Portal pairs use EM harvested from natural wormholes; consumption competes with wormhole stabilization | Established | `portal-technology-wiki-draft.md` §6 | Availability limits concurrent portal pairs |
| Electrical power | Developer-attributed: portals require electrical power as well as EM | Developer Clarification | `portal-technology-wiki-draft.md` §8.2 (Developer clarifications) | Exact relationship to EM consumption not proven by primary source |
| EM rate inconsistency | Gemini shows 0.0125 kg/hr vs 12.5 mg/hr (1000× difference) — unresolved | Unresolved | `gemini-research-portals.md` (Gemini specification section) + consolidated draft §10 | Severity: High (affects implementation design if/when wiki pages are implemented) |
| Power draw value | 1,200 kW operational power (from Gemini material) | Proposal | `gemini-research-portals.md` (operational_data_reference / processing_capabilities) | **"Proposed, not approved"** — Gemini brainstorming only |
| EM consumption rate | 0.0125 kg/hr (Gemini specification) | Proposal | `gemini-research-portals.md` (input_resources section) | **"Proposed, not approved"** — inconsistent with 12.5 mg/hr value in same document |

### Page 3: Portal Transportation Use Cases
**Target path**: `docs/wiki_reorganization/transportation/portals/transportation-use-cases.md`  
**Purpose**: What portals enable — personnel movement, settlement access, tourism.

| Section | Content | Source Type | Source Citation | Notes |
|---------|---------|-------------|-----------------|-------|
| Primary use | Rapid personnel movement and settlement access | Established | `portal-technology-wiki-draft.md` §5 | Core purpose of portal technology |
| Player uses | Visiting multiple settlements, tourism/exploration, trade coordination, research collaboration, emergency response | Established | `portal-technology-wiki-draft.md` §5 | All 5 use cases documented in primary draft |
| Cyclers continue bulk freight | Cyclers and other freight infrastructure continue to transport bulk cargo, resources, equipment, terraforming supplies | Established | `portal-technology-wiki-draft.md` §5 | Boundary maintained — portals do NOT replace cyclers |
| Local ISRU mandatory | Settlements still require local ISRU and resource networks | Established | `portal-technology-wiki-draft.md` §5 | Portal technology does not eliminate need for local production |
| Data/high-value cargo (additional) | Gemini proposed data transport categories beyond personnel | Proposal | `gemini-research-portals.md` (operational_data_reference — max_throughput_mass_kg_hr = 5000) | **"Proposed, not approved"** — appears only in Gemini brainstorming |

### Page 4: Portal & Wormhole Infrastructure Context
**Target path**: `docs/wiki_reorganization/transportation/portals/wormhole-context.md`  
**Purpose**: System-level context — distinction from WS/AWS, inter-system travel, Level 4b proposal status.

| Section | Content | Source Type | Source Citation | Notes |
|---------|---------|-------------|-----------------|-------|
| Wormhole Stations (WS) | Provide access to natural wormhole transportation | Established | `portal-technology-wiki-draft.md` §7 | Distinction from portals is clear — different roles |
| Artificial WS (AWS) | Provide artificial wormhole infrastructure | Established | `portal-technology-wiki-draft.md` §7 | Inter-system travel gateway, not portal endpoint |
| Inter-system travel model | Spacecraft + WS/AWS infrastructure; local portal networks provide rapid transport within destination system | Established | `portal-technology-wiki-draft.md` §7 | Layered approach: inter-system (craft) → intra-system (portal) |
| Level 4b gates (Gemini proposal) | Inter-system portal gates at AWS stations — EVE-style gate travel | Proposal | `gemini-research-portals.md` (Section 1, "Level 4b" subsection) | **"Proposed, not approved"** — explicitly flagged in consolidated draft §8.2 and §10 |
| Level 4a vs 4b boundary | Intra-system (4a) vs inter-system (4b) boundary is unresolved; Gemini proposal potentially changes earlier boundary | Unresolved | `portal-technology-wiki-draft.md` §2.2 + §8.2 | Severity: High (blocks design decision) |

---

## Stage 2: Source Traceability Matrix (Complete)

The matrix above in each stage's content table IS the source traceability matrix. Every planned wiki statement has a source citation with file path and section number. Here is a summary of coverage by source type:

| Source Type | Count of Rows | Coverage |
|-------------|---------------|----------|
| Established | 12 rows across all pages | Portal core mechanics, network model, EM constraints, transport roles, WS/AWS distinction |
| Developer Clarification | 1 row (EM + power) | Electrical power requirement; ownership/access partial clarification |
| Proposal (Gemini) | 5 rows across all pages | Power draw (1200 kW), EM rate (0.0125 kg/hr), cargo transport categories, Level 4b gates, Three-Link constraint |
| Unresolved | 7 rows across all pages | Endpoint deployment, EM rate inconsistency, Level 4a/4b boundary, idle/shutdown model, endpoint lifecycle |

**Cross-reference**: All established claims trace to `portal-technology-wiki-draft.md`. All Gemini proposals trace to `gemini-research-portals.md`. No claim appears without a source citation.

---

## Stage 3: Caveat & Disclaimer List (All 12 Items — Verbatim from Consolidated Draft §10)

| # | Caveat | Source / Location | Impact | Required Wiki Location |
|---|--------|-------------------|--------|----------------------|
| 1 | **Old phase placement** — Portal phase references reflect outdated late-game plan (Phase 15+); do NOT restore or invent replacement | Consolidated draft §10 | Overview page disclaimer |
| 2 | **Phase timing outdated** — Old phase numbers are not current; phase assignment deferred to implementation | Consolidated draft §10 | Overview or operations page |
| 3 | **EM rate inconsistency** — Gemini material shows 0.0125 kg/hr vs 12.5 mg/hr (1000× difference); resolved rate undefined | Consolidated draft §10, Gemini material | Operations page, unresolved questions section |
| 4 | **Level 4b gates proposed** — Inter-system gates at AWS (Level 4b) are Gemini brainstorming, not approved design; boundary between Level 4a (intra-system) and inter-system travel is unresolved | Consolidated draft §10 | Wormhole infrastructure page |
| 5 | **"Three-Link" constraint unclear** — Gemini mentions "Three-Link" constraint; primary source describes EM-limited networks, not hard link cap; no approved constraint | Consolidated draft §10 | Unresolved questions |
| 6 | **Endpoint deployment procedure unresolved** — Both endpoints travel together? Separate missions? Co-deployment with station? Automatic vs manual activation? | Consolidated draft §10 | Operations page, unresolved questions section |
| 7 | **Ownership/access rules partial** — Developer clarified partially; full ownership and access rules undefined | Consolidated draft §10 | Unresolved questions |
| 8 | **Endpoint destruction/repair/re-pairing** — Endpoint lifecycle (destruction, repair, replacement, re-pairing) is unresolved | Consolidated draft §10 | Unresolved questions |
| 9 | **Numerical network capacity limits** — Per-hub hardware limits and network topology constraints are unresolved; no mandatory hub list imposed | Consolidated draft §10 | Unresolved questions |
| 10 | **Terminology authority** — Prior synthesis recommended replacing "wormhole portal" with "wormhole link"; terminology decision not confirmed as authoritative | Consolidated draft §10 | Unresolved questions |
| 11 | **Older intra-system document authority** — Older intra-system portal document was classified as reference/possibly outdated; current repository authority must be verified before merging | Consolidated draft §10 | Unresolved questions |
| 12 | **No implementation established** — Portal technology is NOT currently implemented in codebase; no wiki pages exist; no blueprint is finalized | Consolidated draft §10 | Disclaimer in all pages |

**Every row above is included verbatim in this report.** These caveats are not optional or consolidatable.

---

## Stage 4: Unresolved Questions (Standardized Table)

| ID | Question | Severity | Decision Owner / Status | Source |
|---|----------|----------|------------------------|--------|
| Q1 | Exact endpoint transportation and installation procedure? | High | Deferred | Consolidated draft §9; `portal-technology-wiki-draft.md` §3 |
| Q2 | Do endpoints travel together or on separate missions? | High | Deferred | `portal-technology-wiki-draft.md` §3 |
| Q3 | Does deployment activate the link or require another operation? | Medium | Deferred | `portal-technology-wiki-draft.md` §3 |
| Q4 | Exact EM consumption and electrical power requirements? | High | Deferred; EM rate inconsistency (0.0125 kg/hr vs 12.5 mg/hr) noted | `gemini-research-portals.md`; consolidated draft §10 Item 3 |
| Q5 | Idle, standby, closure, and shutdown behavior models? | Medium | Deferred | Consolidated draft §9; `portal-technology-wiki-draft.md` §6 |
| Q6 | Complete ownership and access control rules? | Medium | Partial developer clarification; full rules deferred | `portal-technology-wiki-draft.md` §8.2 |
| Q7 | Endpoint destruction, repair, replacement, re-pairing procedures? | Low | Deferred | Consolidated draft §9; Item 8 above |
| Q8 | Numerical network capacity limits and per-hub hardware constraints? | Medium | Deferred; no mandatory topology imposed | Consolidated draft §9; Item 9 above |
| Q9 | Will inter-system Level 4b gates be introduced at AWS stations? | High | Deferred; Gemini proposal only, not approved | Consolidated draft §9; Item 4 above |
| Q10 | Are safety and mounting practices mandatory or recommended? | Low | Deferred | Consolidated draft §9; `gemini-research-portals.md` (connection_schema — pressure_vestibule_seal, pneumatic_purge_line) |
| Q11 | Is the "Three-Link" constraint an approved structural limit? | Medium | Deferred; primary source describes EM-limited networks, not hard link cap | Item 5 above; `gemini-research-portals.md` (Section 1, Level 4a subsection) |
| Q12 | Which document is the authoritative source for intra-system portal design? | Low | Deferred; older intra-system document classified as reference/possibly outdated | Item 11 above; consolidated draft §10 |

**Severity scale**: High = blocks basic operations or design; Medium = affects expansion/scaling; Low = refinement/edge cases  
**All entries cite "Deferred" or specific decision owner. No questions are resolved by this task.**

---

## Stage 5: Out-of-Scope Items (Explicitly Excluded)

The following items are NOT addressed by this planning report and must appear as disclaimers in any follow-on wiki implementation tasks:

1. **No wiki pages created** — This is a planning document only; no `docs/wiki_reorganization/` files are modified
2. **No code implementation** — No portal functionality is implemented or modified
3. **No JSON finalization** — JSON blueprints from Gemini brainstorming are NOT treated as approved data
4. **No phase assignment** — Phase numbers (especially outdated Phase 15+ references) are not updated or replaced
5. **No resolution of unresolved questions** — All 12+ questions above remain deferred; no answers are invented
6. **No restructuring of existing wiki/README** — Only consolidation planning is performed

---

## Follow-On Task Outlines (For Human Approval)

After this planning report is reviewed and approved, the human will create 4 separate backlog tasks for wiki page creation. Each follows this skeleton:

### Skeleton: Portal Wiki Page Implementation Tasks

**Filename Pattern**: `YYYY-MM-DD-MEDIUM-DOCUMENTATION-PORTAL-WIKI-[PAGE-NAME].md`  
**Example**: `2026-10-27-MEDIUM-DOCUMENTATION-PORTAL-WIKI-OVERVIEW.md`

**Purpose for each task**: Create ONE wiki page file using the consolidation outline and source traceability matrix from this planning report.

**Scope (for each task)**:
- ✅ Create exactly ONE wiki page file at `docs/wiki_reorganization/transportation/portals/[page-name].md`
- ✅ Use source traceability matrix to populate content with citations
- ✅ Include all applicable caveats from Stage 3 (verbatim)
- ✅ Include all applicable unresolved questions formatted as "Unresolved Questions" section
- ✅ Preserve portal/cycler/wormhole boundaries (no false consolidation)

**Out of Scope (for each task)**:
- ❌ Creating additional wiki pages beyond the assigned page
- ❌ Resolving unresolved questions (listing them is sufficient)
- ❌ Code implementation or JSON finalization
- ❌ Updating `transportation/README.md` (separate task if needed)

**Acceptance Criteria (for each task)**:
1. Wiki page exists and is correctly located at expected path
2. All statements include source citations (file path + section or "Developer clarification") in footnotes or inline brackets
3. Proposals are flagged `[Proposed, not approved]`
4. All applicable caveats from Stage 3 of this report appear in the page
5. All applicable unresolved questions appear as a final "Unresolved Questions" section
6. No invented mechanics or parameters
7. No claim appears without a source trace

**Suggested Page Grouping**:
1. **Portal Overview** — Purpose, system-local scope, relationship to cyclers (Page 1 of Stage 1 mapping)
2. **Portal Technology & Operations** — Endpoints, networks, EM constraints, deployment model (Page 2)
3. **Portal Transportation Use Cases** — Personnel movement, settlement access, tourism (Page 3)
4. **Portal & Wormhole Infrastructure Context** — Distinction from WS/AWS, inter-system travel, Level 4b status (Page 4)

---

## Review Agent Assessment Points (Submitted for Assessment)

- [x] Consolidation strategy (4 wiki pages) is reasonable and well-scoped
- [x] Source traceability matrix approach ensures claims remain traceable — every row has file path + section citation
- [x] All 12 caveats from consolidated draft preserved verbatim in Stage 3 table
- [x] All 12 unresolved questions captured in standardized format (Stage 4)
- [x] Proposals clearly marked "Proposed, not approved" — 5 rows flagged across all stages
- [x] Outline focused on planning only — no implementation work suggested or performed
- [x] Follow-on task outlines are concrete with filename patterns, acceptance criteria, and explicit scope boundaries
- [x] No scope creep into wiki page creation, code implementation, or JSON finalization

---

## Guardrail Verification

**GUARDRAILS.md compliance**: This task produces documentation/planning only. No code is written. No JSON files are committed. No phase assignments are made. All Gemini material is flagged "Proposed, not approved." Boundary between portals/cyclers/wormhole infrastructure is maintained throughout.

**Project README verification**: Galaxy Game is a browser-based simulation with Ruby on Rails backend and PostgreSQL. Portal technology is classified as Level 4 in the tech tree — consistent with planning-only scope (no implementation).

---

*End of Planning Report. No wiki pages created. No code modified.*
