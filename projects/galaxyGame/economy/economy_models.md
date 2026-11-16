# Economy Models Documentation

## Overview

This document describes all data models involved in the NPC economy lifecycle. Models span three namespaces: `Market`, `Logistics`, and top-level `PlayerContract`. All statements are backed by code evidence from models, migrations, and specs.

---

## Market Namespace Models

### Market::Marketplace

**Table**: `market_marketplaces`

**Purpose**: Represents a marketplace associated with a settlement. Acts as the entry point for order placement and trade matching.

**Associations:**
```ruby
belongs_to :settlement, class_name: 'Settlement::BaseSettlement'
has_many :market_conditions, class_name: 'Market::Condition', foreign_key: :market_marketplace_id, dependent: :destroy
has_many :prices, through: :market_conditions
has_many :orders, through: :market_conditions, source: :orders
```

**Key Methods:**
| Method | Return | Description |
|---|---|---|
| `get_price(item, seller:, demand:)` | Float | Static price lookup — delegates to `NpcPriceCalculator.calculate_ask` |
| `place_order(params)` | Market::Order or nil | Creates order, matches against counter-orders, returns unmatched portion |
| `execute_trades(sell_order, matching_orders)` | void | Delegates to `TradeExecutionService.execute!` |
| `find_matching_orders(new_order)` | Array[OpenStruct] | Finds NPC buy orders for sell orders only; returns [] for buy orders |
| `create_synthetic_npc_order(...)` | OpenStruct | Creates synthetic NPC counter-order for matching |

**Migration history:**
- `20250214213217_create_market_marketplaces.rb` — initial table (name, timestamps)
- `20260112003325_add_settlement_id_to_market_marketplaces.rb` — added settlement FK

---

### Market::Condition

**Table**: `market_conditions`

**Purpose**: Represents a resource's current state within a marketplace (price, supply, demand). Acts as the bridge between marketplaces and their orders/prices.

**Associations:**
```ruby
belongs_to :marketplace, class_name: 'Market::Marketplace', foreign_key: :market_marketplace_id
has_many :market_orders, class_name: 'Market::Order', foreign_key: :market_condition_id, dependent: :destroy
has_many :orders, class_name: 'Market::Order', foreign_key: :market_condition_id, dependent: :destroy
has_many :price_histories, class_name: 'Market::PriceHistory', foreign_key: :market_condition_id, dependent: :destroy
```

**Key Attributes** (inferred from usage):
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `market_marketplace_id` | bigint | FK to marketplace |
| `resource` | string | Resource name |
| `price` | decimal | Current price |
| `supply` | integer | Available supply |
| `demand` | integer | Current demand |
| `timestamps` | — | created_at, updated_at |

**Key Methods:**
| Method | Return | Description |
|---|---|---|
| `current_price` | Float | Most recent price from history, or 10 as default |

---

### Market::Order

**Table**: `market_orders`

**Purpose**: Represents a buy or sell order placed by an NPC (settlement/organization) or player. Orders are tied to a market condition and expire after 24 hours.

**Associations:**
```ruby
belongs_to :market_condition, class_name: 'Market::Condition', foreign_key: :market_condition_id
belongs_to :orderable, polymorphic: true
belongs_to :base_settlement, class_name: 'Settlement::BaseSettlement', foreign_key: :base_settlement_id
```

**Enums:**
```ruby
enum order_type: { buy: 0, sell: 1 }
# Note: status is computed (not stored), based on expiration
```

**Key Attributes:**
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `market_condition_id` | bigint | FK to market condition |
| `orderable_type` | string | Polymorphic type (Organization, Settlement, Player) |
| `orderable_id` | bigint | Polymorphic ID |
| `base_settlement_id` | bigint | FK to base settlement |
| `resource` | string | Resource name |
| `quantity` | integer | Order quantity |
| `order_type` | integer | buy (0) or sell (1) |
| `fulfilled_at` | datetime | Set when order is completed (added in migration 20251229004658) |
| `timestamps` | — | created_at, updated_at |

**Key Methods:**
| Method | Return | Description |
|---|---|---|
| `price_per_unit` | Float | Delegates to `NpcPriceCalculator.calculate_bid/calculate_ask` |
| `total_cost` | Float | `price_per_unit * quantity` |
| `expires_at` | Time | `created_at + 24.hours` |
| `expired?` | Boolean | `Time.current > expires_at` |
| `status` | String | Computed: `'expired'` or `'pending'` |
| `fulfill!` | Time | Sets `fulfilled_at` to current time |
| `fulfilled?` | Boolean | Checks if `fulfilled_at` is present |
| `buy?` / `sell?` | Boolean | Enum predicates |
| `resource_name` | String | Alias/accessor for resource |

**Validations:**
- `quantity` — presence
- `order_type` — presence
- `resource` — presence

---

### Market::Trade

**Table**: `market_trades`

**Purpose**: Records a completed trade between buyer and seller. Created by `TradeExecutionService.execute!`.

**Associations:**
```ruby
belongs_to :buyer, polymorphic: true
belongs_to :seller, polymorphic: true
belongs_to :buyer_settlement, class_name: 'Settlement::BaseSettlement'
belongs_to :seller_settlement, class_name: 'Settlement::BaseSettlement'
```

**Key Attributes:**
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `buyer_type` | string | Polymorphic type |
| `buyer_id` | bigint | Polymorphic ID |
| `seller_type` | string | Polymorphic type |
| `seller_id` | bigint | Polymorphic ID |
| `buyer_settlement_id` | bigint | FK to buyer's settlement |
| `seller_settlement_id` | bigint | FK to seller's settlement |
| `resource` | string | Resource traded |
| `quantity` | integer | Amount traded |
| `price` | decimal | Price per unit |
| `volume` | — | Alias for `quantity` (added via `alias_attribute`) |
| `timestamps` | — | created_at, updated_at |

**Note**: The `volume` alias exists because some specs/tests reference `volume` instead of `quantity`.

---

### Market::PriceHistory

**Table**: `market_price_histories`

**Purpose**: Records individual price points for resources over time. Used by market-based pricing to inform future quotes.

**Associations:**
```ruby
belongs_to :market_condition, class_name: 'Market::Condition', foreign_key: :market_condition_id
```

**Key Attributes:**
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `market_condition_id` | bigint | FK to market condition |
| `price` | decimal | Recorded price |
| `timestamps` | — | created_at, updated_at |

**Validations:**
- `price` — presence

---

### Market::SupplyChain

**Table**: `market_supply_chains`

**Purpose**: Records the supply chain generated when a trade is executed. Links orders to their source and destination.

**Associations:**
```ruby
belongs_to :market_order, class_name: 'Market::Order', foreign_key: :market_order_id
# Polymorphic associations for source/destination (set in TradeExecutionService)
```

**Key Attributes** (inferred from TradeExecutionService):
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `market_order_id` | bigint | FK to the order that generated this chain |
| `sourceable_type` | string | Polymorphic source type |
| `sourceable_id` | bigint | Polymorphic source ID |
| `destinationable_type` | string | Polymorphic destination type |
| `destinationable_id` | bigint | Polymorphic destination ID |
| `resource_name` | string | Resource name |
| `volume` | decimal | Trade volume |
| `status` | string | Default 'pending' |
| `timestamps` | — | created_at, updated_at |

**Scopes:**
- `active` — all orders not delivered or cancelled

---

### Market::TransactionFee

**Table**: `market_transaction_fees`

**Purpose**: Records transaction fees collected during trades (tax collection). Used by `Financial::TaxCollectionService`.

---

### Market::Setting

**Table**: `market_settings`

**Purpose**: Global marketplace configuration settings. Added in migration `20251123013047`.

---

## Logistics Namespace Models

### Logistics::Contract

**Table**: `logistics_contracts`

**Purpose**: Represents inter-settlement transport contracts. Used for moving goods between settlements via various transport methods.

**Associations:**
```ruby
belongs_to :provider, class_name: 'Logistics::Provider'
belongs_to :from_settlement, class_name: 'Settlement::BaseSettlement'
belongs_to :to_settlement, class_name: 'Settlement::BaseSettlement'
belongs_to :initiated_by, polymorphic: true, optional: true
```

**Enums:**
```ruby
enum status: { pending: 0, in_transit: 1, delivered: 2, failed: 3, cancelled: 4 }
enum transport_method: {
  orbital_transfer: 'orbital_transfer',
  surface_conveyance: 'surface_conveyance',
  drone_delivery: 'drone_delivery',
  direct_import: 'direct_import',
  contracted_harvesting: 'contracted_harvesting'
}
```

**Key Attributes:**
| Column | Type | Description |
|---|---|---|
| `id` | bigint | Primary key |
| `provider_id` | bigint | FK to provider |
| `from_settlement_id` | bigint | Source settlement |
| `to_settlement_id` | bigint | Destination settlement |
| `initiated_by_type` | string | Polymorphic initiator type |
| `initiated_by_id` | bigint | Polymorphic initiator ID |
| `material` | string | Resource/material being transported |
| `quantity` | integer | Amount to transport |
| `transport_method` | string | Method of transport |
| `shipping_cost` | decimal | Cost of transport |
| `emergency` | boolean | Whether this is an emergency order |
| `arrives_at` | datetime | Expected arrival time |
| `started_at` | datetime | When transport began |
| `completed_at` | datetime | When delivery completed |
| `failure_reason` | text | Reason for failure status |
| `timestamps` | — | created_at, updated_at |

**Key Methods:**
| Method | Return | Description |
|---|---|---|
| `mark_delivered!` | void | Sets status to delivered, completed_at to now |
| `mark_failed!(reason)` | void | Sets status to failed with reason |
| `mark_in_transit!` | void | Sets status to in_transit, started_at to now |

**Scopes:**
- `active` — pending or in_transit
- `completed` — delivered
- `arriving_soon` — in_transit/pending where arrives_at <= now
- `emergency_orders` — emergency: true

**Constants:**
- `EARTH_LUNA_TRANSIT_DAYS = 3`

**Validations:**
- `material`, `quantity`, `transport_method` — presence
- `quantity` — greater than 0
- `shipping_cost` — greater than or equal to 0 (allow nil)

---

## Top-Level Models

### PlayerContract

**Table**: `player_contracts`

**Purpose**: Represents contracts created by the AI Manager that players can accept. Used for GCC-funded local work and USD-funded import orders.

**Associations:**
```ruby
belongs_to :issuer, polymorphic: true   # Player or Organization
belongs_to :acceptor, polymorphic: true, optional: true
belongs_to :location, class_name: 'Location::BaseLocation', optional: true
has_one :insurance_policy, as: :covered_contract
```

**Enums:**
```ruby
enum contract_type: { item_exchange: 0, courier: 1 }
# Future: auction (2), loan (3)

enum status: { open: 0, accepted: 1, completed: 2, failed: 3, cancelled: 4 }
```

**Serialized Attributes (JSON):**
| Column | Description |
|---|---|
| `requirements` | What acceptor must provide |
| `reward` | What issuer provides |
| `collateral` | Security deposit |
| `security_terms` | Insurance/security terms |

**Key Methods:**
| Method | Return | Description |
|---|---|---|
| `value` | Float | Contract value for insurance purposes |
| `risk_factors` | Hash | Route risk, contractor reliability, cargo value, duration |

**Scopes:**
- `active` — open or accepted status
- `courier_contracts` — courier type only

**Validations:**
- `contract_type` — presence

---

### MissionContract

**Table**: `mission_contracts`

**Purpose**: Represents mission-related contracts (distinct from player-facing contracts). Used for AI Manager mission tracking.

**Note**: Minimal model definition — details are in the AI Manager service layer.

---

### Trade (top-level)

**File**: `app/models/trade.rb`

**Purpose**: A top-level Trade model (separate from `Market::Trade`). Appears to be an older or parallel implementation. The marketplace namespace version (`Market::Trade`) is the primary one used by the current economy system.

---

## Settlement & Organization Models (Economy-Relevant)

### Settlement::BaseSettlement

**Economy relevance:**
- NPCs are settlements with associated organizations
- Each settlement has an inventory (`settlement.inventory`)
- Each settlement has a marketplace (`settlement.marketplace`)
- `Marketplace.get_price` resolves settlement from seller
- `NpcPriceCalculator` methods take settlement as first parameter

### Organizations::BaseOrganization

**Economy relevance:**
- NPCs are organizations (corporations, factions)
- `owner.is_npc?` check determines NPC buyer routing in TradeExecutionService
- Organizations have accounts (`organization.account`) for financial transactions
- LDC and ASTROLIFT corporations are key economy participants

---

## Financial Models (Economy-Relevant)

### Financial::Account

**Purpose**: Represents monetary accounts for settlements and organizations. Used by `TradeExecutionService` for fund transfers.

**Key Methods:**
- `find_or_create_for_entity_and_currency(accountable_entity, currency)` — ensures account exists
- `transfer_funds(amount, target_account, description)` — moves funds via virtual ledger
- `decrement!(:balance, amount)` / `increment!(:balance, amount)` — balance updates

### Financial::VirtualLedgerService

**Purpose**: Records financial transactions between accounts. Used for tax collection and trade settlements.

**Key Methods:**
- `record_transfer(from_account, to_account, amount, currency, item, description)` — creates ledger entry

### Financial::TaxCollectionService

**Purpose**: Collects sales tax on trades. Called by `TradeExecutionService.execute!`.

**Key Methods:**
- `collect_sales_tax(seller_organization, gross_revenue, currency)` — returns `{ tax_paid: amount }`

### Financial::Currency

**Purpose**: Currency definitions. Currently GCC is the primary symbol.

---

## Model Relationship Diagram

```
┌─────────────────────┐         ┌──────────────────────┐
│ Settlement::        │         │ Market::Marketplace   │
│ BaseSettlement      │1───1   │                       │
│                     │         │  - name              │
│  - inventory ───────┼────────▶│  - settlement (FK)   │
│  - marketplace ─────┼────────▶└──────────┬───────────┘
└─────────────────────┘                    │
                                           ▼
                                  ┌──────────────────────┐
                                  │ Market::Condition     │
                                  │                       │
                                  │  - marketplace (FK)   │
                                  │  - resource           │
                                  │  - price              │
                                  │  - supply/demand      │
                                  └───────┬──────────────┘
                                          │
                    ┌─────────────────────┼─────────────────────┐
                    ▼                     ▼                     ▼
            ┌───────────────┐    ┌───────────────┐    ┌───────────────┐
            │ Market::Order │    │Market::Price  │    │Market::Supply │
            │               │    │   History     │    │   Chain       │
            │  - condition  │    │  - condition  │    │  - order      │
            │  - orderable  │    │  - price      │    │  - source     │
            │  - settlement │    │  - timestamp  │    │  - dest       │
            │  - resource   │    └───────────────┘    │  - volume     │
            │  - quantity   │                          └───────────────┘
            │  - order_type │
            │  - fulfilled  │
            └───────┬───────┘
                    │
                    ▼
            ┌───────────────┐         ┌──────────────────────┐
            │ Market::Trade │◀────────│ TradeExecution       │
            │               │         │   Service            │
            │  - buyer      │  execute│   .execute!          │
            │  - seller     │────────▶│   → tax collection   │
            │  - quantity   │         │   → fund transfer    │
            │  - price      │         │   → inventory update │
            └───────────────┘         │   → trade record     │
                                      └──────────────────────┘

┌─────────────────────┐    ┌──────────────────────┐    ┌──────────────────────┐
│ AIManager::          │    │ PlayerContract       │    │ Logistics::          │
│ ContractCreation     │───▶│                      │    │ Contract             │
│   Service            │    │  - issuer (poly)     │    │                      │
│                      │    │  - acceptor (poly)   │    │  - provider          │
│ create_player_contract│   │  - location          │    │  - from_settlement   │
│ create_import_order  │    │  - contract_type     │    │  - to_settlement     │
└─────────────────────┘    │  - requirements      │    │  - initiated_by      │
                           │  - reward            │    │  - material          │
                           │  - collateral        │    │  - quantity          │
                           │  - status            │    │  - transport_method  │
                           └──────────────────────┘    │  - shipping_cost     │
                                                       └──────────────────────┘
```

---

## Migration Timeline

| Date | Migration | Model Affected |
|---|---|---|
| 2025-02-14 | `create_market_marketplaces.rb` | Marketplace table created |
| 2025-02-14 | `create_market_conditions.rb` | Condition table created |
| 2025-02-14 | `create_market_price_histories.rb` | PriceHistory table created |
| 2025-02-14 | `create_market_orders.rb` | Order table created |
| 2025-02-14 | `create_market_trades.rb` | Trade table created |
| 2025-02-14 | `create_market_supply_chains.rb` | SupplyChain table created |
| 2025-02-14 | `create_market_transaction_fees.rb` | TransactionFee table created |
| 2025-11-23 | `create_market_settings.rb` | Setting model added |
| 2025-12-29 | `add_fulfilled_at_to_market_orders.rb` | Order.fulfilled_at column |
| 2025-12-31 | `create_logistics_contracts.rb` | Logistics::Contract table |
| 2026-01-01 | `add_logistics_fields_to_logistics_contracts.rb` | Contract additional fields |
| 2026-01-01 | `add_provider_to_logistics_contracts.rb` | Contract.provider association |
| 2026-01-12 | `add_settlement_id_to_market_marketplaces.rb` | Marketplace.settlement FK |
| 2026-01-12 | `add_columns_to_market_supply_chains.rb` | SupplyChain columns |
| 2026-01-12 | `create_player_contracts.rb` | PlayerContract table |
| 2026-04-23 | `add_logistics_contract_fields.rb` | Contract additional fields |

---

## Known Model Issues

1. **Logistics::Contract has syntax issues** — `belongs_to :initiated_by` and `validates :arrives_at` are not properly separated by newlines in the source file
2. **Marketplace table missing settlement FK initially** — added in 2026-01-12 migration
3. **Order status is computed, not stored** — relies on `expires_at` calculation (created_at + 24h)
4. **PlayerContract contract_type enum has commented-out types** — auction and loan are planned but not active
5. **Trade.volume alias** — exists because some specs reference `volume` instead of `quantity`
6. **Market::Condition has duplicate order associations** — both `market_orders` and `orders` point to the same `Market::Order` records
