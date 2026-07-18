---
title: "CanonicalMapService — Biome Alias Resolution & Canonical Tile Mapping"
priority: HIGH
status: active
phase: phase1
owner: Implementation Agent (Qwen)
type: feature
system_domain: TERRA_SIM | BIOME_RENDERING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md \
         projects/galaxy_game/tasks/active/2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-PHASE1-CANONICALMAPSERVICE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# Task: CanonicalMapService — Biome Alias Resolution & Canonical Tile Mapping

**Created**: 2026-07-17  
**Priority**: HIGH (Phase 1 Task 1)  
**Project**: galaxy_game  
**Role**: Implementation Agent (Qwen)  
**Status**: backlog  

---

## Objective

Implement `CanonicalMapService` — a Ruby service that normalizes biome names and resolves them to canonical tile mappings. This eliminates substring fallback bugs and provides the foundation for all future biome/terrain lookups.

**Design Source**: ChatGPT 10-session consolidation + Gemini integration spec (both reviewed and approved).

---

## Context: Why This Exists

### Current Problem
- Biome lookup uses substring fallbacks that create ambiguous matches
- No normalization layer — "Temperate Forest" ≠ "temperate_forest" → silent failures
- Alias resolution happens inside the lookup function instead of BEFORE it
- 30+ biome entries with duplicates (savanna/savannah, grassland/grasslands, etc.)

### Solution
- **CanonicalMapService** normalizes names BEFORE lookup
- **16 canonical tiles** compress 30+ entries via alias resolution
- **Layer ownership** is explicit: desert/tundra/ocean → Layer 0 (terrain), not Layer 1 (biome)
- **Hydrosphere model**: computed coverage (bathtub fill) + tileset visuals (ocean.png, methane_lake.png, etc.)

---

## Canonical Section Numbering (Phase 4 Reference)

```
1. Vision
2. Story
3. Universe Generation
4. Planetary Simulation
5. Game World Model
6. Simulation Engine
7. Economy
8. Manufacturing
9. Settlements
10. Transportation
11. AI Manager
12. Gameplay
13. Development
14. Reference
```

**Note**: Section 6 (Simulation Engine) is the wiki section that documents hydrosphere as a computed pipeline step, not a static terrain type. This task implements the service that feeds into that documentation.

---

## Architecture: Two-Layer Model

### Layer Ownership (Resolved 2026-07-17)

| Asset | Layer 0 (Terrain) | Layer 1 (Biome) | Notes |
|-------|-------------------|-----------------|-------|
| desert | ✅ | ❌ | Base geological substrate |
| tundra | ✅ | ❌ | Climate-controlled substrate |
| ocean | ✅ | ❌ | Opaque fluid base |
| mountains | ✅ | ❌ | Opaque geological base |

### Hydrosphere Model (Corrected)

**Two distinct questions for hydrosphere:**

1. **Which tiles are water?** → Computed dynamically (bathtub fill from elevation + `water_coverage` climate variable)
2. **What do those water tiles LOOK LIKE?** → Tileset lookup (ocean.png, methane_lake.png, frozen_ice.png, etc.)

**Key insight**: The *coverage* is dynamic/computed, but the *visual assets* are still tileset lookups — just like terrain and biomes. The difference is that **which tiles get the liquid treatment changes with climate**, not that liquid tiles don't exist as visual assets.

### Surface View Layer Architecture (from surface_view.js)

```javascript
layers: {
  elevation: null,   // Layer 0 — geological base (grayscale or earth-tone)
  liquid:    null,   // Layer 1 — computed hydrosphere (bathtub fill)
  biomes:    null,   // Layer 2 — ecological overlay (Earth-class only)
  resources: null    // Layer 3 — optional resource overlay
}
```

**Rendering order**:
```
Elevation colour (static geological base)
        │ computed via bathtub fill from elevation + water_coverage %
Liquid (dynamic, depth-based rgba overlay)
        │ only on non-liquid cells (!isWet check)
Biome colour/sprite (ecological overlay)
        │ optional
Resources (yellow tint)
```

---

## 16 Canonical Biome Tiles

### Ice Group
| Alias | Canonical Tile |
|-------|---------------|
| arctic | smooth_ice_sheet_white |
| polar_ice | pack_ice |
| snow | smooth_ice |
| glacier | glacier_texture |

### Forest Group
| Alias | Canonical Tile |
|-------|---------------|
| boreal_forest | forest.png |
| temperate_forest | forest.png |
| forest | forest.png |
| boreal | forest.png |
| taiga | forest.png |
| tropical_rainforest | tropical_jungle.png |
| rainforest | tropical_jungle.png |
| tropical_forest | tropical_jungle.png |

### Wet Group
| Alias | Canonical Tile |
|-------|---------------|
| wetlands | swamp.png |
| marsh | swamp.png |
| bog | swamp.png |
| wetland | swamp.png |

### Grass Group
| Alias | Canonical Tile |
|-------|---------------|
| grassland | grasslands.png |
| grasslands | grasslands.png |
| temperate_grassland | grasslands.png |
| tropical_grassland | grasslands.png |
| steppe | plains.png |
| lowlands | plains.png |

### Mountain Group
| Alias | Canonical Tile |
|-------|---------------|
| alpine | mountains_snow_covered.png |
| montane | mountains.png |
| highlands | rocky_plains.png |

### Other (Direct Mapping)
| Alias | Canonical Tile |
|-------|---------------|
| desert | desert.png |
| savanna | savanna.png |
| savannah | savanna.png |
| tundra | tundra.png |
| agricultural | agricultural_fields.png |

---

## Implementation Requirements

### 1. CanonicalMapService Class Structure

```ruby
# app/services/canonical_map_service.rb
class CanonicalMapService
  # Freeze all constants — never mutated at runtime
  CANONICAL_TILE_MAP = {
    # Ice Group
    'arctic' => { canonical: 'smooth_ice_sheet_white', layer: :terrain },
    'polar_ice' => { canonical: 'pack_ice', layer: :terrain },
    'snow' => { canonical: 'smooth_ice', layer: :terrain },
    'glacier' => { canonical: 'glacier_texture', layer: :terrain },

    # Forest Group
    'boreal_forest' => { canonical: 'forest.png', layer: :biome },
    'temperate_forest' => { canonical: 'forest.png', layer: :biome },
    'forest' => { canonical: 'forest.png', layer: :biome },
    'boreal' => { canonical: 'forest.png', layer: :biome },
    'taiga' => { canonical: 'forest.png', layer: :biome },
    'tropical_rainforest' => { canonical: 'tropical_jungle.png', layer: :biome },
    'rainforest' => { canonical: 'tropical_jungle.png', layer: :biome },
    'tropical_forest' => { canonical: 'tropical_jungle.png', layer: :biome },

    # Wet Group
    'wetlands' => { canonical: 'swamp.png', layer: :biome },
    'marsh' => { canonical: 'swamp.png', layer: :biome },
    'bog' => { canonical: 'swamp.png', layer: :biome },
    'wetland' => { canonical: 'swamp.png', layer: :biome },

    # Grass Group
    'grassland' => { canonical: 'grasslands.png', layer: :biome },
    'grasslands' => { canonical: 'grasslands.png', layer: :biome },
    'temperate_grassland' => { canonical: 'grasslands.png', layer: :biome },
    'tropical_grassland' => { canonical: 'grasslands.png', layer: :biome },
    'steppe' => { canonical: 'plains.png', layer: :biome },
    'lowlands' => { canonical: 'plains.png', layer: :biome },

    # Mountain Group
    'alpine' => { canonical: 'mountains_snow_covered.png', layer: :biome },
    'montane' => { canonical: 'mountains.png', layer: :biome },
    'highlands' => { canonical: 'rocky_plains.png', layer: :biome },

    # Other (Direct Mapping)
    'desert' => { canonical: 'desert.png', layer: :terrain },
    'savanna' => { canonical: 'savanna.png', layer: :biome },
    'savannah' => { canonical: 'savanna.png', layer: :biome },
    'tundra' => { canonical: 'tundra.png', layer: :terrain },
    'agricultural' => { canonical: 'agricultural_fields.png', layer: :biome }
  }.freeze

  # Layer 0 terrain assets (resolved via CanonicalMapService)
  TERRAIN_ASSETS = {
    'desert' => { canonical: 'desert.png', layer: :terrain },
    'tundra' => { canonical: 'tundra.png', layer: :terrain },
    'ocean' => { canonical: 'ocean.png', layer: :terrain },
    'mountains' => { canonical: 'mountains.png', layer: :terrain }
  }.freeze

  # Hydrosphere tileset mappings (computed coverage + tileset visuals)
  HYDROSPHERE_TILES = {
    'H2O' => { liquid: 'ocean.png', frozen: 'smooth_ice_sheet_white.png' },
    'CH4' => { liquid: 'methane_lake.png', frozen: 'frozen_methane.png' },
    'C2H6' => { liquid: 'ethane_lake.png', frozen: 'frozen_ethane.png' },
    'N2' => { liquid: 'nitrogen_liquid.png', frozen: 'frozen_nitrogen.png' },
    'NH3' => { liquid: 'ammonia_liquid.png', frozen: 'frozen_ammonia.png' }
  }.freeze

  class << self
    # Normalize biome name to canonical form
    def normalize(biome_name)
      return nil if biome_name.blank?

      key = biome_name.to_s.downcase.strip.gsub(' ', '_')

      # Direct lookup first
      return CANONICAL_TILE_MAP[key] if CANONICAL_TILE_MAP.key?(key)

      # Alias resolution (savannah → savanna, etc.)
      resolved = resolve_alias(key)
      return CANONICAL_TILE_MAP[resolved] if resolved && CANONICAL_TILE_MAP.key?(resolved)

      nil  # Unknown biome — caller handles fallback
    end

    # Resolve known aliases to canonical forms
    def resolve_alias(key)
      ALIAS_MAP = {
        'savannah' => 'savanna',
        'rainforest' => 'tropical_rainforest',
        'boreal' => 'boreal_forest',
        'taiga' => 'boreal_forest',
        'wetland' => 'wetlands',
        'grasslands' => 'grassland',
        'tropical_grassland' => 'grassland',
        'temperate_grassland' => 'grassland',
        'montane' => 'mountains',
        'highlands' => 'rocky_plains',
        'alpine' => 'mountains_snow_covered',
        'polar_ice' => 'pack_ice',
        'snow' => 'smooth_ice',
        'glacier' => 'glacier_texture',
        'marsh' => 'wetlands',
        'bog' => 'wetlands',
        'lowlands' => 'plains',
        'steppe' => 'plains',
        'tropical_forest' => 'tropical_rainforest',
        'boreal_forest' => 'boreal_forest',
        'temperate_forest' => 'temperate_forest',
        'tropical_rainforest' => 'tropical_rainforest',
        'rainforest' => 'tropical_rainforest',
        'jungle' => 'tropical_jungle',
        'hot_desert' => 'desert',
        'cold_desert' => 'desert',
        'polar_desert' => 'desert'
      }.freeze

      ALIAS_MAP[key]
    end

    # Get layer assignment for a biome/terrain name
    def get_layer(biome_name)
      normalized = normalize(biome_name)
      normalized&.dig(:layer) || :unknown
    end

    # Check if a name is Layer 0 (terrain) vs Layer 1 (biome)
    def is_terrain?(biome_name)
      get_layer(biome_name) == :terrain
    end

    # Get all canonical tiles for a layer
    def tiles_for_layer(layer)
      CANONICAL_TILE_MAP.values
        .concat(TERRAIN_ASSETS.values)
        .concat(HYDROSPHERE_TILES.values.flatten.map { |v| v.is_a?(Hash) ? v.values : v })
        .uniq
        .select { |t| t.is_a?(String) }
    end

    # Validate that a biome name resolves to a known canonical tile
    def valid?(biome_name)
      !normalize(biome_name).nil?
    end
  end
end
```

### 2. JSON Configuration File

Create `config/canonical_biomes.json` with the full mapping (for reference/documentation):

```json
{
  "version": "1.0",
  "description": "Canonical biome tile mappings — all aliases resolve to these tiles",
  "canonical_tiles": {
    "Ice Group": {
      "arctic": "smooth_ice_sheet_white",
      "polar_ice": "pack_ice",
      "snow": "smooth_ice",
      "glacier": "glacier_texture"
    },
    "Forest Group": {
      "boreal_forest": "forest.png",
      "temperate_forest": "forest.png",
      "forest": "forest.png",
      "boreal": "forest.png",
      "taiga": "forest.png",
      "tropical_rainforest": "tropical_jungle.png",
      "rainforest": "tropical_jungle.png",
      "tropical_forest": "tropical_jungle.png"
    },
    "Wet Group": {
      "wetlands": "swamp.png",
      "marsh": "swamp.png",
      "bog": "swamp.png",
      "wetland": "swamp.png"
    },
    "Grass Group": {
      "grassland": "grasslands.png",
      "grasslands": "grasslands.png",
      "temperate_grassland": "grasslands.png",
      "tropical_grassland": "grasslands.png",
      "steppe": "plains.png",
      "lowlands": "plains.png"
    },
    "Mountain Group": {
      "alpine": "mountains_snow_covered.png",
      "montane": "mountains.png",
      "highlands": "rocky_plains.png"
    },
    "Other": {
      "desert": "desert.png",
      "savanna": "savanna.png",
      "savannah": "savanna.png",
      "tundra": "tundra.png",
      "agricultural": "agricultural_fields.png"
    }
  },
  "layer_0_terrain": {
    "desert": "desert.png",
    "tundra": "tundra.png",
    "ocean": "ocean.png",
    "mountains": "mountains.png"
  },
  "hydrosphere_tiles": {
    "H2O": { "liquid": "ocean.png", "frozen": "smooth_ice_sheet_white.png" },
    "CH4": { "liquid": "methane_lake.png", "frozen": "frozen_methane.png" },
    "C2H6": { "liquid": "ethane_lake.png", "frozen": "frozen_ethane.png" },
    "N2": { "liquid": "nitrogen_liquid.png", "frozen": "frozen_nitrogen.png" },
    "NH3": { "liquid": "ammonia_liquid.png", "frozen": "frozen_ammonia.png" }
  }
}
```

### 3. Integration Points

**surface_view.js** — Replace `_biomeTileKey()` substring fallbacks with CanonicalMapService calls:
- Before lookup: `CanonicalMapService.normalize(biome_name)`
- After normalization: use resolved canonical key for tile lookup
- Layer check: `CanonicalMapService.is_terrain?(biome_name)` to determine Layer 0 vs Layer 1

**BiomeRenderer** — Update `BIOME_NAMES` constant to match canonical tiles (16 entries, not 30+).

**Terrain pipeline** — `_selectTerrainFamily()` and `_selectTerrainType()` should use CanonicalMapService for layer-aware lookups.

---

## Testing Strategy

### RSpec Tests (spec/services/canonical_map_service_spec.rb)

```ruby
require 'rails_helper'

RSpec.describe CanonicalMapService do
  describe '.normalize' do
    it 'resolves direct canonical names' do
      expect(described_class.normalize('boreal_forest')).to eq(canonical: 'forest.png', layer: :biome)
      expect(des_class.normalize('tropical_rainforest')).to eq(canonical: 'tropical_jungle.png', layer: :biome)
    end

    it 'resolves aliases to canonical forms' do
      expect(described_class.normalize('savannah')).to eq(canonical: 'savanna.png', layer: :biome)
      expect(described_class.normalize('taiga')).to eq(canonical: 'forest.png', layer: :biome)
      expect(described_class.normalize('wetland')).to eq(canonical: 'swamp.png', layer: :biome)
    end

    it 'handles case and whitespace variations' do
      expect(described_class.normalize('Temperate Forest')).to eq(canonical: 'forest.png', layer: :biome)
      expect(described_class.normalize('  TROPICAL_RAINFOREST  ')).to eq(canonical: 'tropical_jungle.png', layer: :biome)
    end

    it 'returns nil for unknown biomes' do
      expect(described_class.normalize('unknown_biome_xyz')).to be_nil
    end

    it 'handles blank inputs gracefully' do
      expect(described_class.normalize(nil)).to be_nil
      expect(described_class.normalize('')).to be_nil
      expect(described_class.normalize('   ')).to be_nil
    end
  end

  describe '.resolve_alias' do
    it 'resolves all known aliases' do
      # Test all alias mappings from ALIAS_MAP
    end
  end

  describe '.get_layer' do
    it 'returns :terrain for Layer 0 assets' do
      expect(described_class.get_layer('desert')).to eq(:terrain)
      expect(described_class.get_layer('tundra')).to eq(:terrain)
      expect(described_class.get_layer('ocean')).to eq(:terrain)
    end

    it 'returns :biome for Layer 1 assets' do
      expect(described_class.get_layer('boreal_forest')).to eq(:biome)
      expect(described_class.get_layer('grassland')).to eq(:biome)
    end
  end

  describe '.is_terrain?' do
    it 'returns true for Layer 0' do
      expect(described_class.is_terrain?('desert')).to be true
      expect(described_class.is_terrain?('tundra')).to be true
    end

    it 'returns false for Layer 1' do
      expect(described_class.is_terrain?('boreal_forest')).to be false
    end
  end

  describe '.valid?' do
    it 'returns true for known biomes' do
      expect(described_class.valid?('grassland')).to be true
    end

    it 'returns false for unknown biomes' do
      expect(described_class.valid?('nonexistent_biome')).to be false
    end
  end

  describe 'integration with surface_view.js' do
    it 'can replace substring fallbacks in _biomeTileKey' do
      # Test that normalized biome names resolve correctly for tile lookup
    end
  end
end
```

---

## Acceptance Criteria

- [ ] `CanonicalMapService` class exists at `app/services/canonical_map_service.rb`
- [ ] All 16 canonical tiles are mapped with correct aliases
- [ ] Layer ownership is explicit: desert/tundra/ocean/mountains → :terrain, all others → :biome
- [ ] Hydrosphere tile mappings included (H2O, CH4, C2H6, N2, NH3)
- [ ] `normalize()` handles case/whitespace variations
- [ ] `normalize()` returns nil for unknown biomes (no substring fallbacks)
- [ ] `resolve_alias()` resolves all known aliases
- [ ] `is_terrain?()` correctly identifies Layer 0 vs Layer 1 assets
- [ ] JSON config file created at `config/canonical_biomes.json`
- [ ] RSpec tests: 20+ examples, 0 failures
- [ ] No substring fallbacks remain in any lookup function

---

## Stop Conditions

- Report to chat when complete with exact commit message ready
- Wait for explicit approval before running `git commit` or `git push`

---

## Return Format

When complete, provide:
1. Brief summary of what was done
2. Exact commit message (do NOT execute it yet)
3. `git diff --stat` output (do NOT execute git add/commit/push yet)
4. Wait for explicit approval before committing
