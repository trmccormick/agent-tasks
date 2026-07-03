---
name: "Add Economic Parameters Market Fees"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: data
---

# TASK: Economic Parameters — Market Fee Defaults and Order Durations

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: data  
**Created**: 2026-06-21  

## Problem Statement

`config/economic_parameters.yml` defines all economic defaults for the game. The docking transaction system requires default broker fee, transaction fee, and valid order duration options. These do not exist yet and must be added before `DockingTransactionService` can resolve defaults.

**Current**: No market fee defaults or order duration config in `economic_parameters.yml`  
**Expected**: New `market` section added with all required defaults

## Evidence of Incompleteness

```bash
grep -n "broker_fee\|transaction_fee\|order_duration" config/economic_parameters.yml 2>/dev/null | head -5
# Likely returns no results — market section missing
```

## Files to Edit

| File | Purpose | Key Section |
|------|---------|-------------|
| `config/economic_parameters.yml` | Economic defaults | Add market section under economy |

## Acceptance Criteria

- [ ] New `market` section added to economic_parameters.yml under `economy:`
- [ ] Broker fee default defined (percentage or fixed amount)
- [ ] Transaction fee default defined
- [ ] Valid order duration options specified (min/max/step)
- [ ] DockingTransactionService can resolve defaults from config

## Implementation Steps

1. Add market section to `config/economic_parameters.yml` under `economy:` after existing `market_dynamics:` section
2. Define broker_fee, transaction_fee, and order_duration options
3. Verify DockingTransactionService reads these defaults correctly
4. Test with sample docking transactions

## Commit Instructions

```bash
git add config/economic_parameters.yml
git commit -m "feat: add market fee defaults and order durations to economic parameters"
```
