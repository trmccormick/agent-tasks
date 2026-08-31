# GalaxyGame Asset & Frontend Work — Claude Handoff

**Purpose:** Current handoff for the asset-generation and frontend workstream, kept separate from Claude's primary backend-development work.

## 1. Current MVP Boundary

The current gameplay MVP is **Earth → Luna**.

We are not trying to solve the full multi-system visual economy yet. The immediate objective is to establish a finite, reusable asset catalog for:

- Luna-built technology
- Earth-imported technology

Earth-imported equipment currently has a **NASA/ESA-inspired industrial aesthetic as the preferred/proven default**, but this is a visual style choice, not a definition of Earth technology. Future Earth manufacturers or organizations could use different visual profiles (for example, a more commercial-spaceflight look) without changing the architecture.

Likewise, Luna's manufacturing capability should influence what it can build, its technology level, materials, and refinement—not force every Luna asset into a single visual style.

## 2. Critical Design Principle: Tech Capability ≠ Visual Style

Do not encode:

> Earth = one style  
> Luna = one style

Instead distinguish:

- **Origin/manufacturing context** — where/how an item can be produced
- **Technology/manufacturing capability** — what the technology is capable of producing
- **Visual style/profile** — the chosen industrial/design language
- **Design identity** — the specific visual architecture of an asset

Example:

A Mk1 3D-printed I-beam could be visually identical whether produced on Earth or Luna. It does not need duplicate assets merely because its origin differs.

Conversely, a Mars-developed ship class in the future could have a substantially different design identity while still using common reusable components.

The long-term universe may contain overlapping styles, convergence, independent development, collaboration, licensing, trade, and imported technology. **Do not build those systems now; preserve room for them.**

## 3. Asset Reuse Is Essential

GalaxyGame cannot afford an asset for every combination of:

- world
- manufacturer
- technology level
- origin
- variant
- gameplay context

We want **finite canonical designs with reuse**.

A new craft can reuse existing components while introducing a new visual asset for its overall design. Conversely, an existing component should be reused whenever it adequately represents the new design.

Future authoring should eventually be able to ask:

> Is there already a compatible design/asset that adequately represents this component?

If yes, reuse it.

If no, create a new visual definition and asset family.

This is especially important for common industrial components.

## 4. Asset Family / Catalog Philosophy

The RH-400 established an important pattern.

An asset is not just a sprite. A design may have an asset family such as:

- Catalog Render
- Engineering Blueprint
- Encyclopedia Detail Render
- Exploded View
- Inventory Icon
- Surface Sprite
- Construction/Deployment frames
- Wrecked/Damage states
- Other render representations as needed

The **catalog render and engineering blueprint are particularly valuable**.

The catalog should communicate the design's identity and useful technical information through the surrounding UI/page—not by baking game data into the image.

The engineering-blueprint-style image is a useful addition for the catalog/encyclopedia experience and should be preserved as a visual representation.

## 5. No Text Baked Into Generated Assets

Important UI/data principle:

**Do not put mutable gameplay/catalog values into generated images.**

The image can contain visual markings that are intrinsic to the physical design when appropriate, but catalog text such as:

- specifications
- capacity
- energy consumption
- production rate
- technology level
- manufacturer
- operational status
- prices
- availability
- descriptions

belongs in the UI/page.

This keeps assets reusable and prevents regeneration whenever a game value changes.

## 6. RH-400 Validation History

The RH-400 experiments established the current asset-generation methodology.

### Run 03 — Control

The monolithic prompt performed poorly.

Both generators dropped most recognition features, ignored important proportions, omitted hazard markings, and invented contradictory geometry.

This was initially tempting to classify as a renderer limitation, but prior I-beam testing and the RH-400 catalog render showed that the generators can produce detailed industrial assets.

### Run 04 — Profile Composition

Profile Composition was introduced:

- hierarchical feature prioritization
- visual proportion anchors
- independent style/manufacturing/technology profile resolution
- spatial/structural organization

Result: major improvement.

ChatGPT reached 6/6 recognition features and approximately 58/70; Gemini improved substantially as well.

### Run 05 — Targeted Refinements

Added:

- controlled hex color ranges
- stronger cylindrical-canister geometry constraints
- reinforced hazard-marking requirements

ChatGPT reached approximately 62/70.

Gemini regressed, including camera drift and an invented steering wheel, but the cylindrical canister issue was finally resolved.

### Run 06 — Safeguard Layer

Added renderer-neutral safeguards:

- camera precedence
- explicit autonomous-vehicle protection
- sensor-mast elevation

Result:

- ChatGPT ~62/70, no meaningful regression
- Gemini ~55/70
- both generators achieved 6/6 recognition features
- camera and autonomous-design errors were corrected
- cross-generator gap narrowed substantially

### Validated Pipeline

The current validated development workflow is:

```text
Canonical Data
      ↓
Profile Composition
      ↓
Targeted Refinements
      ↓
Safeguard Layer
      ↓
Frozen Prompt
      ↓
External Image Generator
      ↓
Asset QA
      ↓
Static Asset Registry
      ↓
Game Integration
```

The experiments support this architecture. Do not redesign it without new evidence.

## 7. Correct Runtime Boundary

This was recently clarified and is important.

### Development-Time Authoring

Runs outside the Rails game runtime.

It may read:

- `docs/`
- canonical `data/`
- visual specifications
- prompts
- asset-generation tests
- generated reference images
- QA/evaluation reports

Qwen or another development agent can use these files directly while developing.

### Game Runtime

Runs inside Rails/Docker.

It should consume:

- canonical operational JSON through the existing lookup/catalog architecture
- static generated assets
- eventual asset registry information

The production game **does not generate prompts or images**.

Do **not** mount `docs/` into Docker just to support asset generation.

If a source specification eventually needs to become runtime data, that should be deliberately materialized into an appropriate `data/`/configuration structure rather than treating documentation as runtime data.

## 8. Existing Rails Lookup Architecture

GalaxyGame already has established lookup services for canonical JSON data, including blueprints and operational data.

The existing architecture uses centralized path handling such as `GalaxyGame::Paths` and lookup/catalog services.

Do not duplicate this architecture or create documentation lookup services merely to make development-time asset generation work.

The asset-generation authoring tooling should remain outside Rails.

## 9. Cycler / Asset Decomposition Direction

The longer-term content workflow we have been discussing is:

```text
Game Design
    ↓
Craft / Structure Definition
    ↓
Blueprint
    ↓
Component / Unit Decomposition
    ↓
Verify required blueprints exist
    ↓
Verify active units have Operational Data
    ↓
Check for compatible existing visual designs
    ↓
Create only genuinely new visual assets
    ↓
Catalog / UI assets
    ↓
Game integration
```

For example, a cycler may require many components and units. Existing compatible components should be reused. A genuinely new ship design can receive a new catalog/blueprint/render family while reusing existing component assets.

## 10. Frontend Workstream

Asset work and frontend work can proceed as a **separate workstream from Claude's primary backend implementation**.

That is intentional.

The frontend/catalog work should eventually make strong use of the information represented by the asset family.

A catalog entry could combine:

- primary catalog render
- engineering blueprint view
- technical specifications
- operational characteristics
- manufacturing/origin information
- technology level
- component breakdown
- compatible systems
- status/availability
- related/variant designs

The UI should make the detailed information discovered during RH-400 concept development feel like part of the game rather than merely development documentation.

## 11. Surface Assets and Map Layers

The same underlying asset family should eventually provide representations usable by different game views.

In particular, surface sprites need to work over terrain rather than having terrain baked into them.

The intended surface/map ecosystem includes:

- Civ4/FreeCiv-style tile/surface layers
- TerrainForge's more SimCity-like surface view
- other monitor/surface representations as the game evolves

The surface sprite must therefore have a genuine transparent alpha background, with only appropriate ground-contact shadowing.

The RH-400 correction task specifically identified this issue: baked regolith backgrounds make sprites unusable over other terrain tiles.

## 12. Style Direction

The RH-400 catalog render demonstrated the quality bar we want:

- grounded industrial engineering
- substantial mechanical detail
- believable functional components
- clear mechanical hierarchy
- refined industrial surface treatment
- useful visual storytelling
- strong catalog/engineering presentation

The engineering-blueprint presentation is also valuable.

The **style should be composable**, not globally locked.

A future profile might represent:

```text
NASA/ESA-inspired industrial
Commercial-spaceflight industrial
Martian industrial
Lunar frontier industrial
etc.
```

These are examples of possible visual profiles, not a requirement to create all of them now.

## 13. What We Are NOT Doing Yet

Do not expand scope prematurely into:

- automatic image generation from the production game
- generator-specific production architectures
- every solar system
- elaborate faction/manufacturer style systems
- dynamic asset generation
- unlimited asset variants
- frontend implementation coupled tightly to unfinished backend systems
- replacing the existing Lookup/Catalog architecture

The immediate priority remains **getting the static assets and authoring workflow correct**, then building the catalog/frontend around those stable assets.

## 14. Current Immediate Priority

The next practical milestone is:

**Finish the development-time asset-generation tooling boundary and verify that it can reliably produce frozen prompts from canonical source material without requiring Rails/Docker runtime access to `docs/`.**

After that:

1. Continue generating/QAing the required asset families.
2. Establish the static asset registry.
3. Begin catalog/encyclopedia frontend work against stable asset representations.
4. Connect those assets to the game runtime once the backend contracts are ready.

## 15. Guiding Principle

The overall philosophy is:

> **Design the technology system first, create a finite catalog of meaningful visual designs, reuse assets aggressively where visual identity permits, and let the UI expose the depth of the underlying data without baking mutable information into images.**

Asset generation is a **development/content pipeline**.

The game is a **consumer of the resulting static assets and canonical runtime data**.

The two should be deliberately separated while still sharing well-defined canonical definitions.
