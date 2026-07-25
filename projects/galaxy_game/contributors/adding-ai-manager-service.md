# Adding an AI Manager Service

> **Purpose**: Guide for adding new services to the AI Manager subsystem consistently and correctly.
> **Scope**: All files under `app/services/ai_manager/` and related AI Manager domain services.

## 1. Where to Place New Service Files

### Primary Location: Flat Directory

All AI Manager services live in a **single flat directory**:

```
galaxy_game/app/services/ai_manager/<service_name>.rb
```

**Do NOT create subdirectories.** Despite documentation suggesting `super_mars/`, `luna/`, or other subdirectory structures, all 89 files exist at one depth level:

```
app/services/ai_manager/
├── operational_manager.rb
├── mission_planner_service.rb
├── terraforming_manager.rb
├── ... (86 more files)
└── ai_manager.rb          # Module entry point / require-bundler
```

### Related Domains (AI Manager Adjacent)

Services that conceptually belong to the AI Manager domain but live in separate namespaces:

| Namespace | Path | Count | Notes |
|---|---|---|---|
| Manufacturing | `app/services/manufacturing/` | 14 | ISRU production, construction |
| Manufacturing Construction | `app/services/manufacturing/construction/` | 9 | Dome/station building |
| Terraforming (imported) | `app/services/import/terrain_terraforming_service.rb` | 1 | Imported terraforming service |

**When in doubt**: If the service makes autonomous decisions about the game world, it belongs in `ai_manager/`.

## 2. Naming Conventions

### Class Names

Use **PascalCase** with descriptive suffixes:

| Suffix | When to Use | Example |
|---|---|---|
| `Service` | General-purpose service | `AtmosphericExtractionService` |
| `Manager` | Orchestrates multiple sub-components | `TerraformingManager`, `WormholeManager` |
| `Coordinator` | Coordinates across systems/entities | `LogisticsCoordinator` |
| `Evaluator` / `Optimizer` | Analytical/computational services | `ISREvaluator`, `ISROptimizer` |
| `Handler` | Event/response handling | (none currently, but use for future) |

### File Names

Use **snake_case** with the same suffix:

```ruby
# Good ✅
atmospheric_extraction_service.rb
terraforming_manager.rb
logistics_coordinator.rb
isru_evaluator.rb

# Bad ❌
AtmosphericExtraction.rb          # Missing _service suffix
terraforming_mgr.rb               # Abbreviated suffix
atmospheric_extractor_service.rb  # Wrong suffix (should be Extraction)
```

**Existing inconsistency**: Some files use `_manager.rb` and some use `_service.rb`. New services should follow the pattern above. When refactoring, prefer `_service.rb` for single-responsibility services and `_manager.rb` for orchestrators.

## 3. Required Module Inclusions

### Namespace

All AI Manager services MUST be wrapped in the `AIManager` module:

```ruby
module AIManager
  class MyNewService
    # ...
  end
end
```

### Common Module Inclusions

| Module | When to Include | Example |
|---|---|---|
| `ServiceBase` | If inheriting base service behavior | `include ServiceBase` |
| `LoggingConcern` | For structured logging | `include LoggingConcern` |

**Note**: Most services in the codebase do NOT include explicit modules — they rely on Rails' `Rails.logger` directly. Check existing patterns before adding module inclusions.

### Logger Usage

Use the convention `[ServiceName]` prefix for all log messages:

```ruby
Rails.logger.info "[MyNewService] Starting operation for #{target.inspect}"
Rails.logger.warn "[MyNewService] Condition not met, skipping step"
Rails.logger.error "[MyNewService] Failed to process: #{error.message}"
```

## 4. How to Wire Up the Service

### Loading the Service

Services are loaded in two ways:

**Method 1: Via `ai_manager.rb` require-bundler** (for core services):

```ruby
# In app/services/ai_manager.rb
require_relative 'ai_manager/my_new_service'
```

Add your `require_relative` line to this file. Keep the list alphabetically ordered where possible.

**Method 2: Direct require** (for on-demand loading):

```ruby
# In the file that needs it
require_relative 'ai_manager/my_new_service'
# or
require 'ai_manager/my_new_service'
```

### Instantiation Pattern

Most services follow one of these patterns:

**Class methods only** (stateless, utility-style):

```ruby
module AIManager
  class MyNewService
    def self.do_something(target)
      # No state needed
    end
  end
end
```

**Instance-based with initialization** (stateful):

```ruby
module AIManager
  class MyNewService
    attr_reader :target, :config

    def initialize(target:, config: {})
      @target = target
      @config = default_config.merge(config)
    end

    def execute
      # Main logic
    end
  end
end
```

**Class methods + private state** (hybrid):

```ruby
module AIManager
  class MyNewService
    def self.execute(target, config = {})
      new(target, config).execute
    end

    def initialize(target, config)
      @target = target
      @config = config
    end

    private

    def execute
      # Main logic
    end
  end
end
```

### Calling from Other Services

Use the fully qualified class name:

```ruby
result = AIManager::MyNewService.execute(target, config)
# or
service = AIManager::MyNewService.new(target, config)
result = service.execute
```

**Do NOT use `require` inside other services** if the service is already loaded via `ai_manager.rb`.

## 5. Test Placement Conventions

### Spec File Location

Tests go in `spec/services/ai_manager/` alongside the service:

```
spec/services/ai_manager/
├── my_new_service_spec.rb       # Tests for MyNewService
├── operational_manager_spec.rb
├── terraforming_manager_spec.rb
└── ... (many more)
```

### Spec File Structure

```ruby
require 'rails_helper'

RSpec.describe AIManager::MyNewService, type: :service do
  describe '#execute' do
    let(:target) { create(:settlement) }  # Use factories where possible
    subject { described_class.new(target) }

    context 'when condition is met' do
      it 'returns success' do
        expect(subject.execute).to eq({ status: :success })
      end
    end

    context 'when condition is not met' do
      it 'returns failure with reason' do
        allow(subject).to receive(:condition_met?).and_return(false)
        expect(subject.execute).to eq({ status: :failed, reason: :condition_not_met })
      end
    end
  end
end
```

### Factory Usage

If your service depends on models (e.g., `Settlement`, `CelestialBody`), use factories from `spec/factories/`. Check existing specs for available factories:

```bash
grep -r "factory :" spec/factories/ | grep settlement
```

## 6. Documentation Requirements

### Service Inventory Entry

Every new service MUST be added to the [AI Manager Service Inventory](./ai_manager_service_inventory.md):

1. Add a row to the appropriate table (Core Services or Supporting Services)
2. Include: Service name, file path, one-line responsibility, key methods, MVP phase
3. Update the dependency graph if the service introduces new relationships

### Inline Documentation

At minimum, document:

```ruby
module AIManager
  # MyNewService — [One-sentence description of what this service does]
  class MyNewService
    # Execute the primary operation
    # @param target [Object] The target entity to operate on
    # @return [Hash] Result with :status and optional :data keys
    def execute(target)
      # ...
    end
  end
end
```

### What Goes Where

| Documentation | Location |
|---|---|
| Service responsibility, key methods | Service inventory (this doc's sibling) |
| Public API method signatures | Inline comments on the method |
| Complex algorithms | Inline comments or separate `docs/` file |
| Architectural decisions | `docs/new_agent/rules/DECISIONS.md` |

## 7. Common Pitfalls

### ❌ Creating Subdirectories

```
# WRONG — Do NOT do this:
app/services/ai_manager/super_mars/my_service.rb

# RIGHT — Keep it flat:
app/services/ai_manager/super_mars_settlement_service.rb
```

### ❌ Using Wrong Module Namespace

```ruby
# WRONG
module Terraforming
  class MyService; end
end

# RIGHT
module AIManager
  class TerraformingMyService; end
end
```

### ❌ Circular Dependencies

If Service A needs Service B and Service B needs Service A, break the cycle:

1. Extract shared logic into a third service (e.g., `SharedHelper`)
2. Or make one direction lazy (require inside the method)

### ❌ Placeholder Implementations

Several existing services have placeholder methods that return `true` or mock data:

```ruby
# BAD — Don't commit placeholders
def has_materials?
  true  # Placeholder
end

# GOOD — Either implement fully or add TODO with context
def has_materials?
  # TODO: Implement material check — depends on settlement.inventory
  # Blocked by: inventory API design
  false
end
```

### ❌ Forgetting to Update `ai_manager.rb`

If your service is a core service (used frequently by other AI Manager services), add it to the require-bundler in `app/services/ai_manager.rb`. If it's a supporting/phase-deferred service, direct `require` is acceptable.

### ❌ Naming Conflicts with Models

Some file names may conflict with model names. Check for conflicts:

```bash
# Check for naming conflicts
grep -r "class.*Settlement" galaxy_game/app/models/ | head -5
grep -r "class.*Mission" galaxy_game/app/models/ | head -5
```

If a conflict exists, use a more specific name:

```ruby
# Instead of SettlementManager (conflicts with model)
# Use:
AIManager::SettlementStrategyManager
```

## 8. Checklist for Adding a New Service

- [ ] File placed in `app/services/ai_manager/<name>.rb` (flat directory, no subdirs)
- [ ] Class wrapped in `module AIManager`
- [ ] File named with snake_case and appropriate suffix (`_service.rb`, `_manager.rb`, etc.)
- [ ] Logger messages use `[ServiceName]` prefix convention
- [ ] Service added to `ai_manager.rb` require-bundler (if core service)
- [ ] Test file created at `spec/services/ai_manager/<name>_spec.rb`
- [ ] Service inventory updated with new entry
- [ ] Dependency graph updated (if applicable)
- [ ] Inline documentation on public methods
- [ ] No placeholder implementations committed
- [ ] No naming conflicts with existing models

## 9. Examples

### Example: Adding a New Service

```ruby
# app/services/ai_manager/commodity_trading_service.rb
module AIManager
  # CommodityTradingService — Manages NPC commodity trading between settlements
  class CommodityTradingService
    attr_reader :source_settlement, :target_settlement

    def initialize(source:, target:)
      @source_settlement = source
      @target_settlement = target
    end

    # Execute a commodity trade between settlements
    # @return [Hash] { status: Symbol, traded_items: Array, total_cost: Integer }
    def execute
      Rails.logger.info "[CommodityTradingService] Trading from #{source_settlement.name} to #{target_settlement.name}"
      
      available = source_settlement.inventory.available_items
      return { status: :failed, reason: :no_available_items } if available.empty?

      # Trade logic here...
      { status: :success, traded_items: [], total_cost: 0 }
    end
  end
end
```

Then update `ai_manager.rb`:

```ruby
require_relative 'ai_manager/commodity_trading_service'
```

And create the spec:

```ruby
# spec/services/ai_manager/commodity_trading_service_spec.rb
require 'rails_helper'

RSpec.describe AIManager::CommodityTradingService, type: :service do
  describe '#execute' do
    let(:source) { create(:settlement) }
    let(:target) { create(:settlement) }
    subject { described_class.new(source: source, target: target) }

    it 'returns success when items available' do
      allow(source.inventory).to receive(:available_items).and_return([double(amount: 10)])
      expect(subject.execute[:status]).to eq(:success)
    end

    it 'returns failed when no items available' do
      allow(source.inventory).to receive(:available_items).and_return([])
      expect(subject.execute[:status]).to eq(:failed)
    end
  end
end
```
