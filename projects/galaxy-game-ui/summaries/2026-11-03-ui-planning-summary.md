# GalaxyGame UI Planning — Session Summary

**Date:** 2026-11-03  
**Project location:** `agent-tasks/projects/galaxy-game-ui/summaries/`  
**Purpose:** Preserve the decisions from this session before beginning the UI wiki recovery task.

## 1. Overall direction

Wiki and UI development should be coordinated as one effort.

- The wiki is the authoritative home for durable UI design intent.
- The UI implementation provides practical feedback and may expose missing or outdated assumptions.
- Do not treat wiki work and UI work as unrelated projects.
- Do not make every UI change wait for complete documentation. Recover the relevant intent, then proceed with focused implementation.
- When implementation exposes a discrepancy or unresolved design question, discuss it, update the wiki as appropriate, and align implementation with the clarified intent.
- Implementation tasks should be focused work orders that link to the wiki rather than duplicate its design documentation.

## 2. Immediate next step: source-first UI wiki recovery

Create one focused, documentation-only Qwen task to build out the UI wiki sections:

1. **UI architecture and navigation:** application structure, Command Center, navigation between game environments, shared UI conventions.
2. **Landing page and Command Center:** existing implementation, intended purpose, player overview, account information, activity, operational information.
3. **Planetary View:** existing Planetary Monitor, interactive map and selectable layers, sphere data, GeoTIFF-derived terrain, relationship to TerraSim and Digital Twin.
4. **Surface View:** regional terrain, tile rendering, zoom, layers, location-level interaction, existing versus planned functionality.
5. **Settlement View / TerrainForge:** settlement layout, buildings, infrastructure, utilities, terrain, and expansion.
6. **Digital Twin:** relationship to TerraSim, isolated simulations and projections, AI Manager decision support.
7. **UI design references:** EVE Dashboard, EVE Online, Civilization, SimEarth, SimCity, and other relevant references; explain what each contributes and distinguish inspiration from GalaxyGame requirements.

Keep these related sections in one task rather than creating many separate tasks without a demonstrated need.

## 3. Mandatory source-first approach

The central rule is: **recover existing design intent before proposing or making changes.**

Qwen should:
1. Read the existing wiki structure and relevant current documentation.
2. Review architecture, planning, frozen, archived, and older documentation, plus relevant task history.
3. Inspect the current UI codebase to establish what is actually implemented and how it works.
4. Compare documentation with implementation.
5. Consolidate supported design intent into the UI wiki, preserving existing terminology and decisions.
6. Record contradictions, gaps, and unresolved questions for later discussion.

Constraints:
- Rely heavily on existing documentation and code before working with the user on adjustments.
- Do not invent a new UI design from this session's discussion.
- Do not make design decisions on the user's behalf.
- Do not silently reconcile contradictory documents.
- Distinguish implemented, partially implemented, planned, proposed, obsolete, and unresolved functionality.
- Unfinished code does not prove design intent is missing; older/frozen documentation and task history may explain intended behavior not yet implemented.
- Extract durable intent from task files without duplicating task history in the wiki.
- Link useful source documents and code where appropriate.
- Do not implement UI changes, refactor code, or delete/archive source documentation during this task.

**Deliverables:** coherent UI wiki pages, an index connecting them, and a concise report identifying unresolved questions, conflicting documentation, and potentially obsolete material.

## 4. Existing Planetary Monitor: port first, refine later

The existing Planetary Monitor already has an interactive planetary visualization with selectable layers controlled through the UI/JavaScript, and uses GeoTIFF-derived surface terrain data.

Decision: **Copy/port the existing Planetary Monitor and its current layer implementation into the new UI as-is initially. Do not redesign or rebuild it first.**

Initial approach:
- Preserve map rendering, layer controls, terrain data handling, and existing interactions.
- Preserve current data connections during the initial port.
- Make only changes required for integration (routing, component structure, styling compatibility, dependencies).
- Test the port against the existing implementation to identify lost or changed behavior.
- Refine data sources, calculations, layer accuracy, and presentation after the copied version is running and can be evaluated.

Copying the implementation does not certify that every layer or data source is correct. It preserves a baseline so the implementation and its data can be assessed without losing existing functionality.

Apply the same general principle to other useful existing views: reuse/port first where appropriate, evaluate in the new UI, then refine.

## 5. Planetary visualization and sphere analysis

Preserve two complementary capabilities:

- **Planetary visualization:** interactive map with selectable/combinable data layers such as elevation, biomes, hydrosphere, resources, and other supported data.
- **Sphere analysis:** detailed properties, measurements, trends, and simulation results for Atmosphere, Hydrosphere, Geosphere, and Biosphere.

Data layers and planetary spheres are not one-to-one. Elevation is a geosphere property, for example, while hydrosphere and biosphere information can be rendered over the same geographic map.

Do not replace the existing layered visualization system with four conventional sphere screens. Detailed sphere analysis can complement the map and layer system.

The implementation review should establish:
- Which layers are implemented versus placeholders.
- Which layers use GeoTIFF-derived data versus calculated/generated data.
- How layers are enabled, disabled, combined, and rendered.
- How sphere services supply data to visualizations.
- Whether the same rendering system can display Digital Twin projections without modifying live planetary data.

## 6. UI environments and their purposes

- **Planetary View:** What is happening to the world? Global planetary view and environmental layers.
- **Surface View:** What is happening at a particular location? Regional terrain and location-level detail.
- **Settlement View / TerrainForge:** How is the settlement laid out and operating? Detailed settlement terrain, buildings, infrastructure, utilities, and expansion.
- **Digital Twin:** How might the world develop under different choices, and what guidance/intervention is appropriate for the AI Manager? Isolated scenarios and projections, not a replacement live simulation or god-mode editor.
- **Command Center:** Player overview and entry point into deeper game environments.

Existing implementations and older designs are starting points, not proof that the current form is final. Reuse useful work, identify what is incomplete or unsuitable, and refine after inspecting it in the new UI.

## 7. Reference products and design authority

Reference projects can inform presentation patterns, information architecture, dashboard density, navigation, charts, empty states, and interactions.

References discussed include EVE Dashboard, EVE Online, Civilization, SimEarth, SimCity, and other relevant examples.

These are inspiration, not templates or authorities for GalaxyGame requirements. GalaxyGame retains its own identity, palette, and layout foundations. GalaxyGame's own wiki and documented mechanics remain authoritative for its behavior.

## 8. Workflow after wiki recovery

1. Qwen completes the source-first wiki recovery task.
2. Review the resulting pages and report together.
3. Discuss important gaps, contradictions, and possible adjustments.
4. Update the wiki to reflect decisions.
5. Create focused UI implementation tasks that reference the wiki.
6. Port/integrate existing functionality where useful, test it, and refine based on observed results.
7. Update the wiki when implementation reveals a durable design clarification.

The wiki should not block all progress, but UI implementation should not quietly establish design decisions that never make it into the wiki.

## 9. Immediate task boundary

The immediate Qwen task is documentation recovery and consolidation only. It is not authorization to redesign the UI, implement screens, refactor application code, or discard older source material.

The user will work with Qwen on adjustments after the existing documentation and codebase have been reviewed and the recovered intent is available for discussion.
