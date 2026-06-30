---
name: "Implement Market Decoupling for Orphaned Systems"
priority: HIGH
phase: 8
created: 2026-06-21
status: backlog
type: feature
---

# TASK: Implement Market Decoupling for Orphaned Systems

**Priority**: HIGH  
**Phase**: 8  
**Type**: feature  
**Created**: 2026-06-21  

## Problem Statement

Orphaned systems disconnected from wormhole network should have independent markets with local supply/demand curves instead of relying on global GCC price feed.

**Current**: All systems use global GCC prices regardless of connection status  
**Expected**: Orphaned systems decouple to local markets with scarcity multipliers

## Acceptance Criteria

- [ ] Market decoupling check in MarketService
- [ ] Local supply/demand curve for disconnected systems
- [ ] Scarcity multiplier for high-tech items
- [ ] Inventory locking for AWS construction materials
- [ ] AI prioritizes local forge building over waiting for trade
- [ ] Prices fluctuate independently until wormhole restored

## Implementation Steps

1. Add connected? check to MarketService
2. Bypass GCC feed for orphaned systems
3. Implement scarcity multiplier for high-tech tags
4. Create reserved_for_construction flag on StationMaterial
5. Update market pricing logic for local calculation
6. Add AI preference for local manufacturing
7. Test market independence and reconnection scenarios

## Commit Instructions

```bash
git add galaxy_game/app/services/market/price_calculator.rb galaxy_game/app/services/ai_manager/
git commit -m "feat: decouple orphaned system markets with local supply/demand and scarcity"
```