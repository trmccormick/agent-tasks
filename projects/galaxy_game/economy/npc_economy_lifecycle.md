# NPC Economy Lifecycle

## Overview

The NPC economy is a core gameplay loop where AI-managed NPCs initialize pricing, create buy/sell orders, and players can accept contracts to keep the economy moving. The system spans multiple service namespaces: the **AI Manager** drives economic decisions (pricing, order creation, market stabilization), while **NPC Economy services** act as consumers of those decisions.

The lifecycle flows through five phases: NPC Initialization → Price Setting (AI Manager) → Order Creation → Player Contract Acceptance → Fallback Mechanisms. Each phase is implemented across multiple files — there is no single "economy service" that encapsulates everything.

---

## Lifecycle Phases

### 1. NPC Initialization

**How NPCs enter the economy:**

NPCs in the galaxy_game economy are represented as **Settlement::BaseSettlement** entities with associated organizations (corporations, factions). They are initialized through:

- **AI Manager initialization**: `AIManager::Manager` is instantiated per settlement entity, coordinating all economic behavior for that settlement.
  - File: `app/services/ai_manager/manager.rb`
  
- **Service coordination**: Each AI Manager registers a `ServiceCoordinator` that manages missions, resource acquisition, and scouting.
  - File: `app/services/ai_manager/service_coordinator.rb`

- **Colony management**: `AIManager::ColonyManager` tracks all NPC colonies and the player colony, delegating autonomous tasks to each.
  - File: `app/services/ai_manager/ai_colony_manager.rb`

**NPC Roles:**
- **Buyer of last resort**: Purchases unsold player goods at fair minimum price
- **Producer of last resort**: Manufactures essential items when player production lags
- **Importer of last resort**: Sources items from various locations during shortages
- **Market maker**: Provides continuous bid/ask quotes via `NpcPriceCalculator`

**Key behavior:**
```ruby
# AI Manager tick loop (called each game tick)
def advance_time
  @service_orchestrator.orchestrate_services
  @strategy_selector.evaluate_next_action(@target_entity)
end
```

---

### 2. Price Setting (AI Manager)

**How AI determines initial prices:**

Pricing is handled by `Market::NpcPriceCalculator` which supports two pricing modes:

#### Cost-Based Pricing (Bootstrap Markets — early game, no marketplace)
- Uses Earth import costs as the baseline
- Applies NPC sell markup (`EconomicConfig.npc_sell_markup`) for ask prices
- Applies NPC buy discount (`EconomicConfig.npc_buy_discount`) for bid prices
- Ensures minimum profit margin (3% default)

```ruby
# Ask price = max(import_cost * markup, import_cost * (1 + min_margin))
def cost_based_ask(settlement, resource_name, context)
  import_cost = calculate_import_cost(settlement, resource_name)
  markup = context[:markup] || EconomicConfig.npc_sell_markup(market_exists: false)
  minimum_margin = EconomicConfig.npc('cost_based.minimum_profit_margin') || 0.03
  [import_cost * markup, import_cost * (1 + minimum_margin)].max.round(2)
end

# Bid price = import_cost * discount
def cost_based_bid(settlement, resource_name, context)
  import_cost = calculate_import_cost(settlement, resource_name)
  discount = context[:discount] || EconomicConfig.npc_buy_discount(market_exists: false)
  (import_cost * adjusted_discount).round(2)
end
```

#### Market-Based Pricing (Late game — marketplace exists)
- Uses `Market::PriceHistory` to track historical trade prices
- Calculates bid/ask based on recent market conditions
- Maintains minimum spread between buy and sell prices

**Pricing factors:**
- Local production cost vs. Earth import cost (whichever applies)
- Market supply/demand (when marketplace exists)
- Settlement inventory levels (inventory adjustments to discounts)
- Urgency context flags

**Price lookup flow:**
```ruby
# Marketplace.get_price is used by controllers and specs
Marketplace.get_price(item, seller: settlement, demand: 1)
# → NpcPriceCalculator.calculate_ask(settlement, resource_name, supply: demand)
```

---

### 3. Order Creation

**How NPCs create buy/sell orders:**

Orders are created through two primary paths:

#### Path A: Marketplace Order Placement (`Market::Marketplace#place_order`)
1. Prepare order params from input
2. Create the order in a transaction
3. Call `match_orders` to find matching counter-orders
4. If partial match, return the unmatched order; if fully filled, return nil

```ruby
def place_order(params)
  order_params = prepare_order_params(params)
  transaction do
    order = create_order(order_params)
    match_orders(order)        # Find and execute matching trades
    finalize_order_return(order)
  end
end
```

#### Path B: AI Manager Strategy-Driven Orders
The `AIManager::StrategySelector` evaluates settlement state and generates mission options:

```ruby
def evaluate_next_action(settlement)
  state_analysis = @state_analyzer.analyze_state(settlement)
  mission_options = generate_mission_options(settlement, state_analysis)
  scored_options = score_mission_options(mission_options, state_analysis)
  best_action = select_optimal_action_with_strategy(adjusted_options, ...)
end
```

**Order types:**
- `buy` (enum: 0): NPC wants to purchase a resource
- `sell` (enum: 1): NPC wants to sell a resource

**Order structure:**
- `resource`: The material/resource name
- `quantity`: Integer amount
- `order_type`: buy/sell enum
- `orderable`: Polymorphic association (who placed the order)
- `base_settlement`: Settlement of origin
- `market_condition`: Associated market condition

**Order lifecycle:**
1. Created with status `pending` (via `expired?` check: 24 hours from creation)
2. Matched against counter-orders via `Marketplace#match_orders`
3. If matched, `TradeExecutionService.execute!` handles the trade
4. Marked `fulfilled_at` when completed
5. Automatically expires after 24 hours if unfilled

---

### 4. Player Contract Acceptance

**How players interact with NPC orders:**

Players interact with the economy through two mechanisms:

#### A. Market Orders (Direct Trading)
- Players can place buy/sell orders on any marketplace
- `Marketplace#place_order` handles matching against NPC orders
- `TradeExecutionService.execute!` processes the trade:
  1. Collects sales tax via `Financial::TaxCollectionService`
  2. Transfers net funds between buyer and seller accounts
  3. Updates inventory on both sides
  4. Creates `Market::Trade` record
  5. Records price history

#### B. Player Contracts (`PlayerContract` model)
- Created by `AIManager::ContractCreationService`
- Two types: GCC-funded local contracts and USD-funded import orders
- Contract structure:
  - `contract_type`: item_exchange (0), courier (1)
  - `status`: open (0), accepted (1), completed (2), failed (3), cancelled (4)
  - `requirements`: JSON — what acceptor must provide
  - `reward`: JSON — what issuer provides
  - `collateral`: JSON — security deposit
  - `issuer`: Polymorphic (Player or Organization)
  - `acceptor`: Polymorphic (optional)
  - `location`: BaseLocation where contract is available

```ruby
# Contract creation flow
AIManager::ContractCreationService.create_player_contract(
  settlement,
  material: 'oxygen',
  amount: 100,
  payout_gcc: 5000
)
```

#### C. Logistics Contracts (`Logistics::Contract` model)
- For inter-settlement transport
- Statuses: pending → in_transit → delivered (or failed/cancelled)
- Transport methods: orbital_transfer, surface_conveyance, drone_delivery, direct_import, contracted_harvesting
- Transit times vary by route (e.g., Earth→Luna = 3 days)

---

### 5. Fallback Mechanisms

**What happens when things go wrong:**

Fallback logic is **distributed across multiple services**, not a single fallback service:

#### A. Market Stabilization (`AIManager::MarketStabilizationService`)
The primary fallback coordinator — handles five scenarios:

```ruby
def self.stabilize_market(settlement)
  results = []
  results << ensure_new_player_essentials(settlement)    # New player support
  results << handle_unsold_goods(settlement)             # Buyer of last resort
  results << handle_production_shortages(settlement)     # Producer of last resort
  results << handle_import_shortages(settlement)         # Importer of last resort
  results << coordinate_logistics(settlement)            # Logistics coordination
end
```

**Essential items always available:** `oxygen`, `water`, `basic_structural_panels`, `circuit_boards`, `basic_regolith_panels`, `life_support_filters`, `power_cells`

#### B. Price Correction (Emergent via NpcPriceCalculator)
- NPCs maintain minimum profit margins on all quotes
- Bid/ask spread prevents arbitrage exploitation
- Market-based pricing automatically adjusts to historical data

#### C. Order Expiration (Automatic)
- Orders expire after 24 hours (`expires_at = created_at + 24.hours`)
- Expired orders are marked with status `expired`
- No automatic cancellation — they simply become unmatchable

#### D. Scheduled Trade Fallback (`Economy::ScheduledTradeService`)
- Monitors LDC Buy Orders on Luna Market for N2 and CO
- If unfilled after scheduled interval (1 hour), executes VirtualLedger transaction
- Deducts from L1 depot, adds to Luna inventory, transfers GCC based on market price

```ruby
# Scheduled delivery flow
unfilled_orders = luna_market.orders.where(
  order_type: :buy,
  orderable: ldc_org,
  resource: ['N2', 'CO'],
  fulfilled_at: nil
)
# → execute_scheduled_delivery(order) for each unfilled order
```

#### E. Economic Forecasting (`AIManager::EconomicForecasterService`)
- Forecasts resource demand curves and identifies bottlenecks
- Analyzes GCC flow (DC expenditure vs. player earnings)
- Identifies economic opportunities and risk assessments
- Suggests cost optimizations

---

## Key Services

| Service | Role in Lifecycle | File Path |
|---|---|---|
| `AIManager::Manager` | Main AI tick loop, delegates to orchestrator/strategy selector | `app/services/ai_manager/manager.rb` |
| `AIManager::ServiceCoordinator` | Mission coordination, resource acquisition, scouting | `app/services/ai_manager/service_coordinator.rb` |
| `AIManager::StrategySelector` | Evaluates settlement state, generates/scoring mission options | `app/services/ai_manager/strategy_selector.rb` |
| `AIManager::StateAnalyzer` | Analyzes current state: inventory, power, cost analysis | `app/services/ai_manager/state_analyzer.rb` |
| `Market::NpcPriceCalculator` | Bid/ask price calculation (cost-based + market-based) | `app/services/market/npc_price_calculator.rb` |
| `Marketplace::Marketplace` | Order placement, matching, trade execution orchestration | `app/models/market/marketplace.rb` |
| `Market::TradeExecutionService` | Executes trades: tax collection, fund transfer, inventory update | `app/services/market/trade_execution_service.rb` |
| `AIManager::MarketStabilizationService` | Fallback: buyer/producer/importer of last resort | `app/services/ai_manager/market_stabilization_service.rb` |
| `AIManager::EconomicForecasterService` | Demand forecasting, GCC flow analysis, bottleneck detection | `app/services/ai_manager/economic_forecaster_service.rb` |
| `AIManager::ContractCreationService` | Creates player contracts (GCC-funded) and import orders (USD) | `app/services/ai_manager/contract_creation_service.rb` |
| `Economy::ScheduledTradeService` | Monitors and executes scheduled LDC deliveries | `app/services/economy/scheduled_trade_service.rb` |
| `AIManager::LogisticsCoordinator` | Optimizes/schedules inter-settlement transfers | `app/services/ai_manager/logistics_coordinator.rb` |
| `AIManager::ColonyManager` | Tracks all NPC colonies + player colony, delegates autonomous tasks | `app/services/ai_manager/ai_colony_manager.rb` |
| `AIManager::FinancialService` | Debt repayment, settlement fund management | `app/services/ai_manager/financial_service.rb` |

---

## Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        GAME TICK LOOP                               │
│                                                                     │
│  ┌──────────────┐    ┌──────────────────┐    ┌──────────────────┐  │
│  │ AIManager::   │───▶│ StrategySelector │───▶│ ServiceCoordinator│ │
│  │ Manager       │    │ (evaluate_next_  │    │ (missions,       │  │
│  │ (tick)        │    │  action)         │    │ resources,       │  │
│  └──────────────┘    └──────────────────┘    │ scouting)        │  │
│                                               └──────────────────┘  │
│                                                                     │
│  ┌──────────────────┐    ┌──────────────────┐                      │
│  │ StateAnalyzer    │◀───│ NpcPriceCalculator│                      │
│  │ (analyze_state)  │    │ (bid/ask prices) │                      │
│  └──────────────────┘    └──────────────────┘                      │
│                                                                     │
│  ┌──────────────────────────────────────────────────┐              │
│  │         MARKET STABILIZATION SERVICE             │              │
│  │  1. New player essentials                        │              │
│  │  2. Buyer of last resort                         │              │
│  │  3. Producer of last resort                      │              │
│  │  4. Importer of last resort                      │              │
│  │  5. Logistics coordination                       │              │
│  └──────────────────────────────────────────────────┘              │
└─────────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        ORDER LIFECYCLE                              │
│                                                                     │
│  ┌──────────────┐    ┌──────────────────┐    ┌──────────────────┐  │
│  │ Marketplace   │───▶│ TradeExecution   │───▶│ Financial::      │  │
│  │ #place_order  │    │ Service.execute! │    │ VirtualLedger    │  │
│  └──────────────┘    └──────────────────┘    │ (tax, transfer)  │  │
│       │                                      └──────────────────┘  │
│       ▼                                                              │
│  ┌──────────────┐    ┌──────────────────┐                          │
│  │ Market::Order │◀───│ PriceHistory     │                          │
│  │ (buy/sell)   │    │ (tracks trades)  │                          │
│  └──────────────┘    └──────────────────┘                          │
│                                                                     │
│  ┌──────────────────┐    ┌──────────────────┐                      │
│  │ PlayerContract   │◀───│ ContractCreation │                      │
│  │ (open→accepted)  │    │ Service          │                      │
│  └──────────────────┘    └──────────────────┘                      │
│                                                                     │
│  ┌──────────────────┐    ┌──────────────────┐                      │
│  │ Logistics::      │◀───│ Logistics        │                      │
│  │ Contract         │    │ Coordinator      │                      │
│  │ (pending→deliv)  │    │ (optimize/sched) │                      │
│  └──────────────────┘    └──────────────────┘                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Edge Cases

### What happens when an NPC dies/leaves?
- **No explicit death mechanism** exists in the current codebase. NPCs are persistent settlements with ongoing AI Manager instances.
- If a settlement is destroyed, its marketplace and orders become orphaned (no cascade cleanup implemented).
- The `ColonyManager` tracks all colonies but does not handle colony dissolution.

### What happens when prices diverge from market?
- **Market-based pricing** automatically adjusts via `PriceHistory` — historical trades influence future quotes.
- **Minimum profit margin** (3%) prevents NPCs from quoting below cost.
- **MarketStabilizationService** acts as buyer/producer of last resort, preventing complete market collapse.
- The `EconomicForecasterService` identifies bottlenecks and suggests corrections.

### What happens when no players accept orders?
- Orders expire after 24 hours automatically (status becomes `expired`).
- **MarketStabilizationService** handles unsold goods: purchases them at fair minimum price (buyer of last resort).
- **ScheduledTradeService** monitors LDC buy orders and auto-executes via VirtualLedger if unfilled.
- The AI Manager's strategy selector may generate new mission options based on updated state analysis.

### What happens when inventory is insufficient?
- `NpcPriceCalculator` checks settlement inventory levels before quoting (inventory adjustments to discounts).
- `MarketStabilizationService#handle_production_shortages` produces items when supply is low and infrastructure exists.
- `LogisticsCoordinator` can schedule inter-settlement transfers to fill gaps.

### What happens during bootstrap (no marketplace)?
- All pricing uses **cost-based** mode (import costs + markup/discount).
- No price history tracking until marketplace is established.
- NPC colonies still function via `ColonyManager` and `ServiceCoordinator`.

### What happens with currency fluctuations?
- The system currently uses a single currency (GCC) for most transactions.
- USD-funded import orders exist (`ContractCreationService.create_import_order`) but are not fully implemented.
- `Financial::Currency` model exists for multi-currency support.

---

## Known Limitations

1. **No explicit NPC death/retirement** — NPCs are persistent settlements
2. **Order expiration is automatic but not cleaned up** — expired orders remain in DB
3. **USD funding path is incomplete** — `create_import_order` logs but doesn't create records
4. **Market stabilization actions are partially stubbed** — `handle_unsold_goods`, `handle_production_shortages`, `handle_import_shortages` return placeholder results
5. **No multi-currency exchange rate system** — GCC is the primary currency
6. **Price history is minimal** — only tracks price, not volume or market condition context
