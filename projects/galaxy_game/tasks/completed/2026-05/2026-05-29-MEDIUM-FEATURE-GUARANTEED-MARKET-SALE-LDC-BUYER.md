---
status: completed
priority: MEDIUM
type: feature
system_domain: MARKETPLACE
assigned_to: GPT-4.1
mvp_alignment: MARKET_STABILIZATION
autonomy_level: full
completion_date: 2026-05-30
---

# TASK: GuaranteedMarketSale — LDC Buyer of Last Resort Implementation

**Status**: READY FOR IMPLEMENTATION  
**Priority**: MEDIUM  
**Type**: Feature  
**Created**: 2026-05-29  
**Assigned To**: GPT-4.1 (Autonomous Implementation)

---

## Executive Summary

Implement `Marketplace::GuaranteedMarketSale` service — the LDC/NPC "buyer of last resort" that purchases unsold player goods at floor prices to stabilize the market and provide liquidity for new players.

**Why This Matters:**  
- Prevents market collapse when player goods go unsold
- Provides guaranteed revenue floor for player labor
- Prevents cold-start problem for new players with no GCC
- Integrates with Financial::TransactionManager for real GCC transfers

**Autonomy Level**: FULL — GPT-4.1 can implement independently. No intermediate reviews needed. Deliver synthesis report when done.

---

## Scope

### What You're Building

Create a new service `Marketplace::GuaranteedMarketSale` that:

1. **Receives** unsold player goods (resource + volume + bid price)
2. **Validates** LDC account has sufficient GCC reserves
3. **Executes** atomic GCC transfer from LDC account → player account via `Financial::TransactionManager.create_transfer`
4. **Returns** transaction result or error
5. **Gates** on reserve availability (no infinite GCC faucet)

### What Already Exists

| File | Status | Your Use |
|---|---|---|
| `spec/transactions/guaranteed_market_sale_spec.rb` | ✅ Exists | Follow test expectations |
| `app/services/financial/transaction_manager.rb` | ✅ Exists | Call this for transfers |
| `app/models/financial/account.rb` | ✅ Exists | Understand account model |
| `app/models/financial/transaction.rb` | ✅ Exists | Understand transaction record |
| `app/services/marketplace/` | ✅ Directory exists | Create new file here |

### What You Will Create

| File | Purpose |
|---|---|
| `app/services/marketplace/guaranteed_market_sale.rb` | Core implementation |

---

## Technical Specification

### 1. Method Signature (Confirmed)

```ruby
# File: app/services/marketplace/guaranteed_market_sale.rb
module Marketplace
  class GuaranteedMarketSale
    def self.execute(
      player_settlement:,      # Settlement/Organization with account
      resource:,               # String ('LOX', 'Regolith', etc.)
      volume:,                 # Integer/Float volume to buy
      bid_price:,              # GCC price per unit
      ldc_settlement:          # NPC Settlement (buyer of last resort)
    )
      # Implementation here
    end
  end
end
```

### 2. Financial::TransactionManager.create_transfer (Confirmed)

**Exact Signature:**
```ruby
Financial::TransactionManager.create_transfer(
  from: account_object,      # Financial::Account (sender, type-checked)
  to: recipient_object,      # Polymorphic: Settlement, Organization, Player, etc.
  amount: numeric,           # GCC amount
  currency: :gcc,            # Symbol (:gcc or :usd)
  description: string        # Transaction note
)
```

**Returns:**  
- `Financial::Transaction` instance on success
