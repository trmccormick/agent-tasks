# TASK: Wormhole Easter Egg Integration
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-06-01  
**Promote after**: Task 18 (Wormhole Model Validation) complete

---

## Agent Assignment
**Assigned To**: GPT-5 mini (0x) / Raptor mini (preview) (0x)
**Why This Agent**: Complex game mechanics, AI Manager patterns, RSpec confirmation  
**Supervision Level**: 🟢 Light — human reviews final design before implementation

---

## Context

Wormholes are rare discoveries created by the AI Manager WorldKnowledgeService during star system analysis. When a wormhole is discovered, it should trigger an easter egg activation that enhances the player experience with lore-rich narrative content and gameplay bonuses.

**Existing Infrastructure:**
- ✅ `Wormhole` model exists (galaxy_game/app/models/wormhole.rb)
- ✅ AI Manager WorldKnowledgeService pattern established
- ❌ Easter egg system not yet implemented
- ❌ Wormhole discovery triggers not connected to easter eggs

**Related Documentation:**
- docs/agent/archive/backlog_april_2026/wormhole_easter_egg_integration.md (archived design)
- docs/agent/archive/backlog_april_2026/easter_egg_system_cleanup.md (data separation requirements)

---

## Architecture Overview

### Core Components Needed

1. **Easter Egg Model** - Stores easter egg definitions and triggers
2. **Easter Egg Applicator Service** - Applies overlays to game state at runtime
3. **Wormhole Discovery Trigger** - Detects when AI Manager creates a new wormhole
4. **Player Notification System** - Notifies players of rare discoveries

### Data Separation Pattern (Critical)

Following the easter egg system cleanup requirements:
- Base astronomical data files contain ONLY scientific information
- Easter eggs stored in separate JSON files under `data/json-data/easter_eggs/`
- No `easter_egg` fields embedded in star system or geological feature files
- Easter eggs loaded separately at runtime by AI Manager

### Integration Points

```
AI Manager (WorldKnowledgeService)
    ↓ creates Wormhole via StarSystemAnalysisJob
WormhouseDiscoveryHandler (new)
    ↓ triggers easter egg lookup
Easter Egg Service (new)
    ↓ loads easter egg definition from data/json-data/easter_eggs/
    ↓ applies overlay to game state
Game State Overlay (Runtime only, not persisted)
    ↓ enhances player experience
```

---

## Required Changes

### 1. Create Easter Egg Model & Schema

**File**: `db/migrate/XXXX_create_easter_eggs.rb`

```ruby
class CreateEasterEggs < ActiveRecord::Migration[7.1]
  def change
    create_table :easter_eggs do |t|
      t.string :code, null: false, index: { unique: true }
      t.string :title, null: false
      t.text :description
      t.jsonb :triggers # { type: "wormhole_discovery", subtype: "rare_ancient" }
      t.jsonb :effects # { overlay_type: "narrative", content: {...} }
      t.boolean :active, default: true
      t.timestamps
    end

    add_index :easter_eggs, [:code, :active]
  end
end
```

**Seed Data**: `db/seeds/easter_eggs_seed.rb`

```ruby
# Example: Wormhole Discovery Easter Egg
EasterEgg.create!(
  code: 'WORMHOLE_ANCIENT_TECH',
  title: 'Ancient Technological Signature Detected',
  description: 'Analyze reveals an energy signature unlike any known civilization...',
  triggers: { type: 'wormhole_discovery', subtype: 'ancient_tech' },
  effects: { 
    overlay_type: 'narrative',
    content: {
      title: "Ancient Wormhole Network Discovered",
      message: "AI Manager has detected unusual quantum signatures suggesting this wormhole was artificially created by a civilization long extinct...",
      flavor_text: "The energy readings match patterns described in Dr. Chen's thesis on pre-collapse FTL networks.",
      bonus: { resource_credit_multiplier: 1.5, discovery_cooldown_reduction: -20 }
    },
    duration_hours: 72 # Easter egg effect duration
  }
)
```

### 2. Create Easter Egg Service

**File**: `app/services/easter_egg_service.rb`

```ruby
class EasterEggService
  def self.activate_for_event(event_type, event_data = {})
    candidate_eggs = EasterEgg.active.where("triggers->>'type' = ?", event_type)
    
    # Filter by subtype if provided
    if event_data['subtype'].present?
      candidate_eggs = candidate_eggs.where("triggers->>'subtype' = ?", event_data['subtype'])
    end
    
    return nil if candidate_eggs.empty?
    
    # Select one randomly (or use weighted selection later)
    easter_egg = candidate_eggs.order('RANDOM').first
    apply_effects(easter_egg, event_data)
    easter_egg
  rescue => e
    Rails.logger.error("EasterEggService: Failed to activate for #{event_type} - #{e.message}")
    nil
  end
  
  private
  
  def self.apply_effects(easter_egg, event_data)
    # Effects are applied as runtime overlays, not persisted
    # This allows easter eggs to be temporary and replayable
    
    case easter_egg.effects['overlay_type']
    when 'narrative'
      NarrativeOverlayService.apply(easter_egg.effects['content'])
    when 'gameplay_bonus'
      GameplayBonusOverlayService.apply(easter_egg.effects['bonus'], event_data)
    when 'visual_effect'
      VisualEffectOverlayService.apply(easter_egg.effects['visuals'])
    end
    
    Rails.logger.info("EasterEggService: Applied #{easter_egg.code} overlay")
  end
end
```

### 3. Create Wormhole Discovery Handler

**File**: `app/services/wormhole_discovery_handler.rb`

```ruby
class WormhouseDiscoveryHandler
  def self.handle(wormhouse, ai_manager_context = {})
    # Determine easter egg subtype based on wormhouse properties
    subtype = determine_subtype(wormhouse)
    
    # Attempt to activate matching easter egg
    activated_egg = EasterEggService.activate_for_event('wormhole_discovery', {
      subtype: subtype,
      wormhole_id: wormhouse.id,
      ai_manager_context: ai_manager_context
    })
    
    if activated_egg
      # Log the activation for player notification
      PlayerNotificationService.create({
        type: 'rare_discovery',
        title: activated_egg.title,
        message: activated_egg.description,
        easter_egg_code: activated_egg.code,
        timestamp: Time.current
      })
      
      Rails.logger.info("WormhouseDiscoveryHandler: Activated easter egg #{activated_egg.code} for wormhouse #{wormhouse.id}")
    end
    
    { activated: !!activated_egg, code: activated_egg&.code }
  end
  
  private
  
  def self.determine_subtype(wormhouse)
    # Use wormhouse properties to determine easter egg subtype
    if wormhouse.properties['ancient_tech_signature']
      'ancient_tech'
    elsif wormhouse.properties['quantum_anomaly']
      'quantum_anomaly'
    else
      'standard'
    end
  end
end
```

### 4. Integrate with Star System Analysis Job

**File**: `app/jobs/star_system_analysis_job.rb` (modification)

```ruby
# Inside the job where wormhole is created:
if create_wormhouse(star_system, analysis_result)
  # Trigger easter egg activation
  WormhouseDiscoveryHandler.handle(wormhouse, {
    star_system_id: star_system.id,
    analysis_type: 'ai_manager_discovery',
    context: analysis_result
  })
end
```

### 5. Create Player Notification Service (if not exists)

**File**: `app/services/player_notification_service.rb`

```ruby
class PlayerNotificationService
  def self.create(notification_data)
    # Store notification in player's notification queue
    # This could be a separate model or embedded in player session
    
    Notification.create!(notification_data.merge(
      player_id: determine_player_from_context,
      unread: true,
      created_at: Time.current
    ))
    
    # Trigger real-time push if supported
    ActionCable.server.broadcast("player_#{determine_player_from_context}", {
      type: 'new_notification',
      notification: notification_data
    })
  end
  
  private
  
  def self.determine_player_from_context
    # Logic to determine which player/session to notify
    # Could come from AI Manager context or session data
    Player.current.id
  end
end
```

### 6. Create Data Files for Easter Eggs

**Directory**: `data/json-data/easter_eggs/`

**Example File**: `data/json-data/easter_eggs/wormhole_ancient_tech.json`

```json
{
  "code": "WORMHOLE_ANCIENT_TECH",
  "title": "Ancient Technological Signature Detected",
  "description": "Analyze reveals an energy signature unlike any known civilization...",
  "triggers": {
    "type": "wormhole_discovery",
    "subtype": "ancient_tech"
  },
  "effects": {
    "overlay_type": "narrative",
    "content": {
      "title": "Ancient Wormhole Network Discovered",
      "message": "AI Manager has detected unusual quantum signatures suggesting this wormhole was artificially created by a civilization long extinct...",
      "flavor_text": "The energy readings match patterns described in Dr. Chen's thesis on pre-collapse FTL networks.",
      "bonus": {
        "resource_credit_multiplier": 1.5,
        "discovery_cooldown_reduction": -20
      }
    },
    "duration_hours": 72
  },
  "lore_references": [
    "Dr. Chen's Thesis on Pre-Collapse FTL Networks",
    "Archaeological Survey of Sector 7G"
  ]
}
```

---

## Acceptance Criteria

- [ ] Easter Egg model created with proper schema
- [ ] Seed data populated for initial easter eggs
- [ ] Easter Egg Service can activate and apply effects
- [ ] WormhouseDiscoveryHandler integrated into StarSystemAnalysisJob
- [ ] Player notifications triggered on rare discoveries
- [ ] Data separation maintained (no easter egg fields in astronomical data files)
- [ ] Effects are runtime-only overlays, not persisted to game state
- [ ] RSpec tests created for easter egg activation logic
- [ ] No regressions in existing wormhole or AI Manager functionality

---

## Testing Strategy

### Unit Tests
```ruby
# spec/services/easter_egg_service_spec.rb
describe EasterEggService do
  describe '.activate_for_event' do
    it 'activates matching easter egg for wormhole discovery' do
      # Test logic
    end
    
    it 'returns nil when no matching easter egg found' do
      # Test logic
    end
  end
end

# spec/services/wormhole_discovery_handler_spec.rb
describe WormhouseDiscoveryHandler do
  describe '.handle' do
    it 'triggers easter egg activation on wormhole creation' do
      # Test logic
    end
    
    it 'notifies player of rare discovery' do
      # Test logic
    end
  end
end
```

### Integration Tests
```ruby
# spec/requests/wormhole_easter_egg_integration_spec.rb
describe 'Wormhole Easter Egg Integration' do
  it 'creates wormhole, activates easter egg, notifies player' do
    # Full flow test
  end
end
```

---

## Commit Instructions

### Phase 1: Database & Models
```bash
git add db/migrate/XXXX_create_easter_eggs.rb db/seeds/easter_eggs_seed.rb
git commit -m "feat: Add Easter Egg model and seed data"
```

### Phase 2: Services
```bash
git add app/services/easter_egg_service.rb \
        app/services/wormhole_discovery_handler.rb \
        app/services/player_notification_service.rb
git commit -m "feat: Create easter egg activation services"
```

### Phase 3: Integration
```bash
git add app/jobs/star_system_analysis_job.rb
git commit -m "feat: Integrate wormhole discovery with easter eggs"
```

### Phase 4: Data Files
```bash
git add data/json-data/easter_eggs/
git commit -m "feat: Add initial easter egg JSON definitions"
```

### Phase 5: Tests
```bash
git add spec/services/ spec/requests/
git commit -m "test: Add specs for wormhole easter egg integration"
```

---

## Progress Notes

**As of 2026-06-XX**: Task created but not yet started. Blocked by Task 18 (Wormhole Model Validation) completion to ensure the `Wormhouse` model is stable before adding discovery triggers.

**Next Steps**:
1. Wait for Task 18 completion and validation of Wormhouse model
2. Implement database schema and seed data
3. Build service layer for easter egg activation
4. Integrate with existing AI Manager wormhole creation logic
5. Add comprehensive test coverage

---

## Related Tasks

- **Task 18**: Wormhouse Model Validation (BLOCKING)
- **Task 20+**: AI Manager WorldKnowledgeService enhancements (related)
- **Future**: Easter egg expansion system (separate task)

---

## Design Notes

**Critical Constraints:**
- Easter eggs must NOT modify persisted game state permanently
- All effects are runtime overlays that expire after duration
- Data separation is mandatory: astronomical data files remain pure scientific data
- Player experience should feel magical but not exploit-prone

**Future Enhancements:**
- Weighted random selection for multiple matching easter eggs
- Difficulty scaling based on player progress
- Achievement integration for completing all easter eggs
- Seasonal/limited-time easter egg events

---

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Easter eggs feel too game-breaking | Limit effects to temporary buffs and narrative content |
| Player exploitation of easter egg triggers | No persistence means no replayable exploits |
| Data file contamination with easter eggs | Enforce strict separation via CI/CD linting |
| Performance impact from random selection | Cache active easter eggs, pre-filter in database query |

---

## Completion Report
*Filled in after completion*

**Completed by**:  
**Completion date**:  

### What was written
### Gaps found  
### Follow-up tasks needed