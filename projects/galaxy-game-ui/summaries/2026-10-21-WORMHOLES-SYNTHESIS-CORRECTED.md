# Wormholes / Transit Synthesis Report — Corrected for Real MockDataHelper Methods

**Date**: 2026-10-21 (created from audit findings)  
**Purpose**: Wormhole network UI data mappings — did not previously exist, created from codebase audit

## Controller Mapping

```ruby
class WormholesController < ApplicationController
  def index
    @network = MockDataHelper.wormhole_network   # ✅ Real method
    @routes  = MockDataHelper.astrolift_routes   # ✅ Real method (astrolift_routes)
    # Note: transit_routes is an alias for astrolift_routes
  end
end
```

## Method Inventory — Verified Against Code

| Data Source | Real Method? | Returns |
|-------------|--------------|---------|
| `MockDataHelper.wormhole_network` | ✅ Yes | Array of {system, type, stability, decay_timer, em_potential} objects |
| `MockDataHelper.astrolift_routes` | ✅ Yes | Array of {route, hops, time, price, capacity} objects |
| `MockDataHelper.transit_routes` | ✅ Yes | Alias for astrolift_routes (same data) |

## Method Details

### wormhole_network (3 settlements)
```ruby
[
  { system: "Luna Settlement Alpha", type: "Natural", stability: "95%", decay_timer: "22 hours", em_potential: "High" },
  { system: "Mars Cycler Station",   type: "Artificial", stability: "100%", decay_timer: "N/A", em_potential: "Medium" },
  { system: "Luna Station Prime",    type: "Artificial", stability: "100%", decay_timer: "N/A", em_potential: "None" }
]
```

### astrolift_routes (2 transit routes)
```ruby
[
  { route: "Luna Settlement Alpha → Mars Cycler Station → Luna Station Prime", hops: 2, time: "4.5h", price: 50_000, capacity: "5000 m³" },
  { route: "Luna Settlement Alpha → Luna Station Prime (direct)",              hops: 1, time: "2h",   price: 85_000, capacity: "3000 m³" }
]
```

## Wormholes View Structure (Recommended)

```
Wormholes View (GET /wormholes — public access)
├── Settlement Network Map
│   └── @network = MockDataHelper.wormhole_network
│       ├── system name + type (Natural/Artificial)
│       ├── stability percentage
│       ├── decay timer (N/A for permanent gates)
│       └── EM potential (High/Medium/None)
├── Transit Routes Table
│   └── @routes = MockDataHelper.astrolift_routes
│       ├── hop count + time
│       ├── pricing
│       └── capacity (m³)
├── NPC Corporation Status (context)
│   └── @corps = MockDataHelper.npc_corporations
│       → shows which corps operate at each node
└── Buy Order Context (optional)
    └── @orders = MockDataHelper.buy_order_status
        → fulfillment status at connected nodes
```

## Data Terminology (Galaxy Game Only)

- **Settlements**: Luna Settlement Alpha, Mars Cycler Station, Luna Station Prime
- **Wormhole Types**: Natural (auto-decaying), Artificial (permanent gates)
- **Gateway Names**: Enedore JUMP gateway (referenced in sim events)
- **Transit Pricing**: In GCC (50,000 / 85,000 per route)
- **Capacity**: Cubic meters for freight calculations

## No Incorrect Claims

This synthesis was created from scratch based on codebase audit — no corrections needed. All three methods (`wormhole_network`, `astrolift_routes`, `transit_routes`) exist and are correctly named.

---

**Created by**: Qwen planning agent (audit synthesis)  
**Based on**: MockDataHelper codebase audit 2026-10-21
