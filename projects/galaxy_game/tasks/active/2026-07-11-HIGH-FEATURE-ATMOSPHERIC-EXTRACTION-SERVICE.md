---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md \
         projects/galaxy_game/tasks/active/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# FEATURE: Create `AIManager::AtmosphericExtractionService` to Replace Mock Data

**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-11
**Last Updated**: 2026-07-11

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Research Synthesis**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-10-RESEARCH-ATMOSPHERIC-HARVESTER-MOCK-REPLACEMENT.md`
   - Design document with replacement service structure, ownership model, routing design
2. **Skimmer-Cycler Handshake**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-11-RESEARCH-SKIMMER-CYCLER-HANDSHAKE-REFACTOR.md`
   - Cargo transfer protocol, docking validation, cycler model details
3. **Current Mock Service**: `galaxy_game/app/services/ai_manager/atmospheric_harvester_service.rb`
   - Lines 1-70: `collect_nitrogen`, `collect_methane`, `titan_harvest`, `venus_harvest`
4. **Transfer Engine**: `galaxy_game/app/services/terra_sim/atmospheric_transfer_service.rb`
   - Lines 1-80: `transfer_atmosphere`, `TRANSFER_MODES`, raw transfer with proportional extraction
5. **Harvester Model**: `galaxy_game/app/models/craft/harvester.rb`
   - `harvest_atmosphere` method, `HasExtraction` concern
6. **Cycler Model**: `galaxy_game/app/models/craft/transport/cycler.rb`
   - `serialize :definition_data, JSON`, `docked_crafts` association

---

## Context

The current `AIManager::AtmosphericHarvesterService` returns hardcoded mock values (`collect_nitrogen(titan) = 200`, `collect_methane(titan) = 150`) that do not modify actual atmosphere state. This task creates a new service that delegates to the existing `TerraSim::AtmosphericTransferService` (which already implements correct physics: proportional extraction, Titan 5% limit, 98% transport efficiency).

Skimmers are AstroLift-owned assets (via `BaseCraft#owner` polymorphic association). They extract raw mixed atmospheric gas proportionally — they cannot selectively target individual gases. Separation happens later at the LDC Gas Separator on Luna.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Cycler cargo storage location**
- ❌ Wrong: `cycler.atmosphere` for cargo — atmosphere is for celestial body state, not craft cargo
- ✅ Right: `cycler.definition_data['cargo']` hash — JSON-serialized via `serialize :definition_data, JSON`
- Why: The cycler model serializes `definition_data` as JSON. Cargo goes in the `cargo` key of this hash, not in the atmosphere association.

⚠️ **GOTCHA 2: Transfer mode for skimmers**
- ❌ Wrong: `:selective` or `:processed` transfer mode — skimmers cannot target individual gases
- ✅ Right: `:raw` transfer mode only — proportional extraction of ALL gases by percentage
- Why: Skimmers extract raw mixed atmospheric gas. Selective separation happens at the LDC Gas Separator on Luna.

⚠️ **GOTCHA 3: No market integration in MVP**
- ❌ Wrong: Add gas commodity types, market listings, or inter-corporation pricing
- ✅ Right: Gases flow to cycler cargo → Luna settlement atmosphere only
- Why: Market integration is Phase 9+. MVP scope ends at delivery to Luna.

### Ruby Files Only

This task involves **Ruby code changes only**. No JSON data files, no migrations, no blueprint changes.

---

## Problem Statement

`AIManager::AtmosphericHarvesterService#collect_nitrogen` and `#collect_methane` return hardcoded mock values instead of performing real atmospheric extraction. The AI Manager's decision logic calls these methods expecting real data but receives static numbers.

**Current behavior**: `titan_harvest(titan)` returns `{ nitrogen: 200, methane: 150 }` regardless of actual atmosphere state.
**Expected behavior**: Extraction should use `TerraSim::AtmosphericTransferService` to perform proportional gas extraction from the source body's atmosphere, with results tracked in cycler cargo storage.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb` | **NEW** — replacement service | `#execute_extraction`, `#dock_and_transfer_to_cycler` |
| `galaxy_game/app/services/ai_manager/skimmer_cycler_handshake_service.rb` | **EXISTING** — update to use new service | Replace mock calls with real extraction |
| `spec/services/ai_manager/atmospheric_extraction_service_spec.rb` | **NEW** — test specs | Ownership validation, raw transfer, cycler cargo |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/terra_sim/atmospheric_transfer_service.rb` | Correct transfer engine to delegate to |
| `galaxy_game/app/models/craft/harvester.rb` | Skimmer model, `harvest_atmosphere` method |
| `galaxy_game/app/models/craft/transport/cycler.rb` | Cycler model, `definition_data['cargo']` hash |
| `galaxy_game/app/models/craft/base_craft.rb` | Ownership model (`belongs_to :owner, polymorphic: true`) |
| `galaxy_game/app/services/ai_manager/corporate_roles.rb` | AstroLift corporate assignments |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md \
       projects/galaxy_game/tasks/active/2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md
```

Then open the moved file and change: `status: backlog → status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-07-11-HIGH-FEATURE-ATMOSPHERIC-EXTRACTION-SERVICE.md"
```

**Paste the output in chat before proceeding.** Expected: exactly one result at `active/`.

---

### Step 1 — Create `AIManager::AtmosphericExtractionService`

Create `galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb`:

```ruby
# app/services/ai_manager/atmospheric_extraction_service.rb
module AIManager
  class AtmosphericExtractionService
    attr_reader :skimmer, :source_body, :target_body, :owner_corporation

    def initialize(skimmer, source_body, target_body: nil)
      @skimmer = skimmer
      @source_body = source_body
      @target_body = target_body || resolve_target_body
      @owner_corporation = resolve_owner_corporation(skimmer)
    end

    # Primary public API — called by AI Manager decision logic
    def execute_extraction(transfer_params = {})
      validate_skimmer_ownership!
      validate_source_atmosphere!

      # Skimmers ALWAYS extract raw (proportional) — they cannot selectively target gases
      transfer_mode = :raw
      capacity = transfer_params[:capacity] || default_skimmer_capacity

      TerraSim::AtmosphericTransferService
        .new(source_body, target_body, mode: transfer_mode, logger: Rails.logger)
        .transfer_atmosphere({ capacity: capacity })
    end

    # Skimmer docks with Cycler to offload cargo
    def dock_and_transfer_to_cycler(cycler, max_capacity = nil)
      return false unless can_dock?(cycler)

      cargo_amount = [
        skimmer.atmosphere&.total_atmospheric_mass || 0,
        (max_capacity || cycler_available_storage(cycler))
      ].min

      transfer_gases_to_cycler(cycler, cargo_amount)
    end

    private

    def validate_skimmer_ownership!
      return if owner_corporation
      raise ArgumentError, "Skimmer #{skimmer.id} has no owner — cannot extract"
    end

    def validate_source_atmosphere!
      unless source_body.atmosphere&.present?
        raise ArgumentError, "Source body #{source_body.name} has no atmosphere"
      end
    end

    def resolve_owner_corporation(skimmer)
      return nil unless skimmer.owner
      skimmer.owner.is_a?(Corporation) ? skimmer.owner : skimmer.owner&.account&.corporation
    end

    def resolve_target_body
      # Default target is Luna for MVP (LDC settlement)
      CelestialBodies::CelestialBody.find_by(name: 'Luna')
    end

    def can_dock?(cycler)
      skimmer.has_available_docking_port? && cycler.has_available_docking_port?
    end

    def transfer_gases_to_cycler(cycler, amount)
      # CARGO GOES IN definition_data['cargo'] HASH — NOT atmosphere
      gases = skimmer.atmosphere&.gases || []
      cycler_cargo = (cycler.definition_data || {})['cargo'] ||= {}

      gases.each do |gas|
        gas_mass = (amount * (gas.percentage / 100.0)).to_i
        cycler_cargo[gas.name] = (cycler_cargo[gas.name] || 0) + gas_mass
      end

      # Update cycler's serialized definition_data
      cycler.definition_data ||= {}
      cycler.definition_data['cargo'] = cycler_cargo
      culer.save! if culer.changed?

      # Remove from skimmer atmosphere
      skimmer.atmosphere&.remove_gas('all', amount) if skimmer.atmosphere
    end

    def cycler_available_storage(cycler)
      # Use cycler's storage capacity or available docking port constraint
      cycler.base_units.sum do |unit|
        unit.operational_data&.dig('storage', 'capacity') || 0
      end - (cycler.base_units.sum do |unit|
        unit.operational_data&.dig('current_load') || 0
      end)
    end

    def default_skimmer_capacity
      # Use skimmer's atmosphere total mass as default extraction amount
      skimmer.atmosphere&.total_atmospheric_mass || 5000
    end
  end
end
```

---

### Step 2 — Update AI Manager Decision Logic

In `galaxy_game/app/services/ai_manager/skimmer_cycler_handshake_service.rb` (or the relevant decision service), replace mock calls:

**Before:**
```ruby
# OLD — mock data, do not use
harvester = AIManager::AtmosphericHarvesterService.new
result = harvester.titan_harvest(titan)  # Returns { nitrogen: 200, methane: 150 }
```

**After:**
```ruby
# NEW — real extraction via AtmosphericExtractionService
extraction = AIManager::AtmosphericExtractionService.new(skimmer, titan)
result = extraction.execute_extraction(capacity: skimmer.atmosphere&.total_atmospheric_mass || 5000)

# Offload to cycler after extraction
if result[:success] && skimmer.docked_at.is_a?(Craft::Transport::Cycler)
  extraction.dock_and_transfer_to_cycler(skimmer.docked_at)
end
```

---

### Step 3 — Write RSpec Specs

Create `spec/services/ai_manager/atmospheric_extraction_service_spec.rb`:

```ruby
require 'rails_helper'

RSpec.describe AIManager::AtmosphericExtractionService, type: :service do
  let(:astro_lift) { create(:corporation, name: 'AstroLift') }
  let(:skimmer) { create(:craft_harvester, owner: astro_lift) }
  let(:titan) { create(:celestial_body_titan) }
  let(:luna) { create(:celestial_body_luna) }
  let(:cycler) { create(:craft_cycler) }

  describe '#initialize' do
    it 'resolves owner corporation from skimmer' do
      service = described_class.new(skimmer, titan)
      expect(service.owner_corporation).to eq(astro_lift)
    end

    it 'defaults target body to Luna' do
      service = described_class.new(skimmer, titan)
      expect(service.target_body).to eq(luna)
    end
  end

  describe '#execute_extraction' do
    context 'when skimmer has no owner' do
      let(:skimmer) { create(:craft_harvester, owner: nil) }

      it 'raises ArgumentError' do
        service = described_class.new(skimmer, titan)
        expect { service.execute_extraction }.to raise_error(ArgumentError, /has no owner/)
      end
    end

    context 'when source has no atmosphere' do
      let(:titan) { create(:celestial_body, atmosphere: nil) }

      it 'raises ArgumentError' do
        service = described_class.new(skimmer, titan)
        expect { service.execute_extraction }.to raise_error(ArgumentError, /has no atmosphere/)
      end
    end

    context 'with valid skimmer and atmosphere' do
      it 'delegates to TerraSim::AtmosphericTransferService with :raw mode' do
        service = described_class.new(skimmer, titan)
        
        expect(TerraSim::AtmosphericTransferService).to receive(:new)
          .with(titan, luna, hash_including(mode: :raw))
          .and_call_original
        
        result = service.execute_extraction(capacity: 1000)
        expect(result[:success]).to be true
        expect(result[:gases_extracted].class).to eq(Hash)
      end

      it 'extracts gases proportionally by percentage' do
        service = described_class.new(skimmer, titan)
        result = service.execute_extraction(capacity: 1000)
        
        # Verify proportional extraction (not selective targeting)
        expect(result[:gases_extracted]).to be_a(Hash)
        expect(result[:gases_delivered]).to be_a(Hash)
      end
    end
  end

  describe '#dock_and_transfer_to_cycler' do
    before do
      # Set up skimmer atmosphere with gases
      skimmer.atmosphere&.add_gas('N2', 1000)
      skimmer.atmosphere&.add_gas('CH4', 500)
    end

    it 'transfers cargo to cycler.definition_data[cargo] hash' do
      service = described_class.new(skimmer, titan)
      
      # Ensure cycler has definition_data with cargo key
      cycler.definition_data ||= {}
      culer.save!
      
      service.dock_and_transfer_to_cycler(cycler)
      
      expect(cycler.definition_data['cargo']['N2']).to be > 0
      expect(cycler.definition_data['cargo']['CH4']).to be > 0
    end

    it 'does NOT use cycler.atmosphere for cargo' do
      service = described_class.new(skimmer, titan)
      service.dock_and_transfer_to_cycler(cycler)
      
      # Cargo should be in definition_data hash, not atmosphere
      expect(culer.definition_data['cargo']).to be_a(Hash)
    end

    it 'returns false when docking is not possible' do
      allow(skimmer).to receive(:has_available_docking_port?).and_return(false)
      
      service = described_class.new(skimmer, titan)
      expect(service.dock_and_transfer_to_cycler(cycler)).to be false
    end
  end
end
```

---

### Step 4 — Verify

Run the new specs in isolation:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/services/ai_manager/atmospheric_extraction_service_spec.rb --format documentation 2>&1 | tail -30
```

Expected result: All examples pass, 0 failures.

Then run related existing specs to verify no regressions:

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/services/ai_manager/skimmer_cycler_handshake_service_spec.rb --format documentation 2>&1 | tail -30
```

---

## Acceptance Criteria

- [ ] `AIManager::AtmosphericExtractionService` created at `app/services/ai_manager/atmospheric_extraction_service.rb`
- [ ] Service delegates to `TerraSim::AtmosphericTransferService` with `:raw` mode
- [ ] Ownership validation raises `ArgumentError` for unowned skimmers
- [ ] Source atmosphere validation raises `ArgumentError` for bodies without atmosphere
- [ ] Cycler cargo transfer writes to `cycler.definition_data['cargo']` hash (NOT `cycler.atmosphere`)
- [ ] RSpec specs pass in isolation: ownership validation, raw transfer mode, cycler cargo transfer
- [ ] No regressions in existing skimmer/cycler handshake specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required (e.g., cycler model structure change)
- Fix requires changing more files than the task specifies

---

## Commit Instructions

Run git commands on **host only** — never inside the Docker container:

```bash
git add galaxy_game/app/services/ai_manager/atmospheric_extraction_service.rb
git add galaxy_game/app/services/ai_manager/skimmer_cycler_handshake_service.rb
git add spec/services/ai_manager/atmospheric_extraction_service_spec.rb
git commit -m "feat(ai_manager): create AtmosphericExtractionService to replace mock harvester data

- New service delegates to TerraSim::AtmosphericTransferService with :raw mode
- Ownership validation for AstroLift-owned skimmers
- Cycler cargo transfer via definition_data['cargo'] hash (not atmosphere)
- RSpec specs: ownership, raw transfer, cycler cargo integration"
git push
```

---

## Documentation

- [ ] No doc changes needed
- [ ] Update `docs/new_agent/projects/galaxy_game/status.md` — add entry for this feature completion
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies

**Blocked by**: `2026-07-10-HIGH-RESEARCH-ATMOSPHERIC-HARVESTER-MOCK-REPLACEMENT.md` (research complete)
**Blocks**: Market integration tasks (Phase 9+)
**Related tasks**: Skimmer-Cycler Handshake Refactor

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
