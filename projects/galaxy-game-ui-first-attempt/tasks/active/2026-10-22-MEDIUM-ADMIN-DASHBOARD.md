# Task 7: Admin Dashboard (Post-Snap Infrastructure, AI Manager Control, Hammer Protocol)

**Priority**: MEDIUM | **Complexity**: High | **Time**: 6-8 hours | **Status**: Ready for Qwen Handoff

---

## Overview

Comprehensive admin interface for post-Snap Sol system showing:
1. **System Status Cards** (Sol, System B, Eden) with post-Snap context
2. **Wormhole Monitoring** (natural + artificial with physics display)
3. **EM Economy Dashboard** (reserves, harvest rate, consumption, days to critical)
4. **AI Manager Control Panel** (directives, priorities, status)
5. **Hammer Protocol Interface** (snap trigger eligibility, coordinates, orphan risk)
6. **Economic Indices Display** (PvE index, energy trade, EAP, EM trade value)

This interface is admin-only and handles both monitoring (read-mostly) and control (snap triggers, AI directives).

---

## Current State

Admin interface exists but is placeholder only:
- `app/controllers/admin_controller.rb` loads mock data
- `app/views/admin/index.html.erb` is empty ("Admin feature — Views will be populated in next task")
- No integration with MockDataHelper admin methods
- No wormhole physics, EM tracking, or AI Manager display

---

## Requirements

### 1. System Overview Cards (3 Systems)

**Data Structure**:
```ruby
{
  system_name: "Sol",
  identifier: "SOL",
  system_mass: 500000,
  stability_index: 0.95,
  celestial_bodies_count: 8,
  active_settlements_count: 3,
  wormhole_connections: 2,  # Natural WH + Artificial AWS link
  known_data_level: "complete",
  post_snap_status: {
    snap_event_date: "2026-10-01T14:32:00Z",
    aws_reconstruction_date: "2026-10-02T09:15:00Z"
  }
}
```

**Display per Card**:
- System name + identifier
- System mass (tons)
- Stability index with visual bar
- Celestial bodies count + major planets/moons list
- Active settlements count
- Wormhole connections (count + types: natural/artificial)
- Post-Snap context (if applicable)
- Organization logo (if system has civilian presence)

### 2. Wormhole Monitoring Table (All Connections)

**Columns**:
| Connection | Type | Stability | Mass Limit | EM Surplus | Harvest Rate | Satellites | Risk Level | AWS Status | Actions |
|---|---|---|---|---|---|---|---|---|---|
| Sol ↔ System B | Natural | 78% | 37 tons | 8500 | +150/1000T | 3 | ⚠️ Caution | — | Details |
| Sol ↔ Eden | Artificial | 95% | 63 tons | 15000 | — | 0 | ✅ Safe | Operational | Details |

**Data Included**:
- **Connection**: System A ↔ System B
- **Type**: Natural or Artificial (AWS)
- **Stability**: % with color bar (green 80+, orange 50-80, red <50)
- **Mass Limit**: Calculated from formula (System_Mass × Stability) / EM_Surplus
- **EM Surplus**: Current EM reserves (recharges at harvest_rate per 1000 tons)
- **Harvest Rate**: EM/1000T transited (natural WH only, 0 for AWS)
- **Satellites**: Recapture satellites active (natural WH only)
- **Risk Level**: safe/caution/danger based on (current_transit_mass / mass_limit)
- **AWS Status**: Operational/Damaged/Offline (artificial WH only)
- **Actions**: "Details", "Boost Stability", "Trigger Snap" (if eligible)

### 3. EM Economy Dashboard

**Display**:
- Total EM Reserves: X across all systems
- Daily Harvest Rate: +Y EM (from natural WH recapture)
- Daily Consumption: -Z EM (maintenance + AWS upkeep + travel)
- Net Daily Flow: ±W EM/day
- Days Until Critical: N days (when reserves hit 0)
- Visual warning bar (yellow at 50%, red at <30%)

**Data Source**: `MockDataHelper.em_status()`

### 4. AI Manager Control Panel

**Directives Display**:
```
Directive: Rebuild Eden Infrastructure
├─ Status: IN_PROGRESS
├─ Progress: 65%
├─ Target: Construct 3 Orbital Stations
├─ Est. Completion: 2026-10-25 18:30 UTC
└─ Resources Allocated: 5000 credits, 250 tons materials

Directive: Harvest System B EM
├─ Status: ACTIVE
├─ Progress: Collecting EM via recapture satellites
├─ Daily Yield: +150 EM
├─ Next Satellite Check: 2026-10-23 06:00 UTC
└─ Risk Assessment: STABLE
```

**Priority Queue Display**:
```
1. [CRITICAL] Rebuild Eden AWS (24h to complete)
2. [HIGH] Expand EM reserves (ongoing harvest)
3. [MEDIUM] Survey System B for settlement opportunities
4. [LOW] Optimize cycler logistics (next phase)
```

**Next Tick Display**:
```
Next AI Decision Tick: 2026-10-23 06:00 UTC
Actions Queued: 
  - Check EM recapture satellite status
  - Evaluate Eden infrastructure progress
  - Re-assess System B exploration priority
```

**Data Source**: `MockDataHelper.ai_manager_status()`

### 5. Hammer Protocol Interface (Snap Trigger Controls)

**Section** (ADMIN ONLY, red styling):
```
🔨 HAMMER PROTOCOL — EMERGENCY WORMHOLE CONTROL

System B Natural WH Status:
├─ Current Mass Limit: 37 tons
├─ Current Transit Mass: 12 tons
├─ Snap Trigger Threshold: 35 tons (94% capacity)
├─ Snap Ready: NO (need +23 tons to trigger)
├─ Projected New Exit: [78.3°, 45.6°, 12.1 depth]
├─ Orphan Risk Systems: [Eden]
└─ Estimated EM Field Value: 2,500 EM (harvestable post-Snap)

[⚠️ TRIGGER SNAP EVENT] (disabled until threshold reached)
```

**Interactive Elements**:
- Snap trigger button (disabled/enabled based on threshold)
- Confirmation modal with consequences listing
- Real-time monitoring of transit mass progress toward threshold

**Data Source**: `MockDataHelper.hammer_protocol_status()`

### 6. Economic Indices Display

**Metrics**:
```
Economic Indices (Last Updated: 2026-10-22 14:32 UTC)

PvE Index: 0.87 (Stable)
├─ NPC Activity: 85%
├─ Market Volatility: 12%
└─ Resource Scarcity: 8%

Energy Trade Index: 0.92 (Strong)
├─ Energy Supply: 8,500 EM reserves
├─ Demand Level: 6,500 EM/day
└─ Margin: +2,000 EM/day

EAP Index: 0.76 (Moderate)
├─ Exploration Activity: High
├─ Alien Presence: Low
└─ Player Engagement: Moderate

EM Trade Value: 15.2 Credits/EM (Up 0.3)
├─ Historical Range: 14.5 - 16.8
└─ Trend: Increasing (shortage likely)
```

**Data Source**: `MockDataHelper.economic_indices()`

---

## File Modifications (Specification)

### `app/controllers/admin_controller.rb` — Update Index Action

**Current State**: Loads mock data but no rendering context

**Required Changes**:
1. Load wormhole network: `@network = MockDataHelper.wormhole_network`
2. Load EM status: `@em_status = MockDataHelper.em_status`
3. Load AI Manager status: `@ai_manager = MockDataHelper.ai_manager_status`
4. Load economic indices: `@economic_indices = MockDataHelper.economic_indices`
5. Load Hammer Protocol status: `@hammer_protocol = MockDataHelper.hammer_protocol_status`
6. Calculate quick stats (systems count, wormholes count, avg stability, etc.)
7. Verify admin authentication (before_action)

**Controller Specification**:
```ruby
class AdminController < ApplicationController
  before_action :require_admin_access
  
  def index
    # Load all admin data from MockDataHelper
    @network = MockDataHelper.wormhole_network
    @systems = @network[:systems]
    @wormholes = @network[:wormholes]
    @post_snap_context = @network[:post_snap_status]
    
    @em_status = MockDataHelper.em_status
    @ai_manager = MockDataHelper.ai_manager_status
    @economic_indices = MockDataHelper.economic_indices
    @hammer_protocol = MockDataHelper.hammer_protocol_status
    
    # Calculate derived stats
    @stats = {
      systems_known: @systems.count,
      active_wormholes: @wormholes.count,
      avg_stability: (@wormholes.map { |w| w[:stability_index] }.sum / @wormholes.count.to_f * 100).round,
      total_em_reserves: @em_status[:total_em_reserves],
      network_health: calculate_network_health(@wormholes, @em_status)
    }
  end
  
  private
  
  def require_admin_access
    # Mock admin check (session[:admin] = true for now)
    redirect_to root_path unless session[:admin]
  end
  
  def calculate_network_health(wormholes, em_status)
    # Network health = (avg_stability * 60%) + (em_reserves_healthy * 40%)
    avg_stability = wormholes.map { |w| w[:stability_index] }.sum / wormholes.count.to_f
    em_healthy = em_status[:days_until_critical] > 60 ? 1.0 : em_status[:days_until_critical] / 60.0
    ((avg_stability * 0.6 + em_healthy * 0.4) * 100).round
  end
end
```

### `app/views/admin/index.html.erb` — Complete Admin Dashboard Layout

**Required Sections** (in order):

1. **Header Section**:
   - Title: "System Administration Dashboard"
   - Post-Snap context note (if applicable)
   - Quick stats bar (4 cards: Systems Known, Active Wormholes, Avg Stability, Network Health)

2. **EM Economy Dashboard**:
   - Total reserves + visual bar
   - Daily harvest/consumption/net flow
   - Days until critical (red warning if <30)

3. **AI Manager Control Panel**:
   - Current directives list (status, progress, ETA)
   - Priority queue (5 items)
   - Next tick time + queued actions

4. **System Overview Cards Grid** (3 cards):
   - One card per system (Sol, System B, Eden)
   - Display: name, identifier, mass, stability, celestial bodies, settlements, wormhole connections
   - Post-Snap metadata if present (orphan status, AWS reconstruction date)

5. **Wormhole Monitoring Table**:
   - All wormholes with complete physics
   - Color-coded by type (natural = green, artificial = cyan) and stability
   - Actions: Details, Boost Stability, Trigger Snap (if eligible)

6. **Hammer Protocol Panel** (Red styling, admin-only):
   - Natural WH snap readiness
   - Current mass limit vs transit mass
   - Snap trigger threshold
   - Projected coordinates + orphan risk
   - EM field value
   - Trigger button (enabled/disabled based on threshold)

7. **Economic Indices Display**:
   - PvE Index with sub-metrics
   - Energy Trade Index
   - EAP Index
   - EM Trade Value (with trend)

**View Template Structure** (pseudo-code):
```erb
<div class="admin-dashboard">
  <!-- Header -->
  <div class="admin-header">
    <h1>System Administration Dashboard</h1>
    <%= render "post_snap_context", context: @post_snap_context %>
  </div>
  
  <!-- Quick Stats -->
  <div class="stats-row">
    <%= render "stat_cards", stats: @stats %>
  </div>
  
  <!-- EM Economy -->
  <div class="em-economy-panel">
    <%= render "em_status_panel", em_status: @em_status %>
  </div>
  
  <!-- AI Manager -->
  <div class="ai-manager-panel">
    <%= render "ai_manager_control", ai_manager: @ai_manager %>
  </div>
  
  <!-- System Cards -->
  <div class="systems-overview">
    <h2>System Overview</h2>
    <div class="systems-grid">
      <% @systems.each do |system| %>
        <%= render "system_card", system: system, wormholes: @wormholes %>
      <% end %>
    </div>
  </div>
  
  <!-- Wormhole Monitoring Table -->
  <div class="wormhole-monitoring">
    <%= render "wormhole_table", wormholes: @wormholes %>
  </div>
  
  <!-- Hammer Protocol -->
  <% if session[:admin] %>
    <div class="hammer-protocol-panel">
      <%= render "hammer_protocol_interface", hammer_protocol: @hammer_protocol %>
    </div>
  <% end %>
  
  <!-- Economic Indices -->
  <div class="economic-indices-panel">
    <%= render "economic_indices_display", indices: @economic_indices %>
  </div>
</div>
```

### Create Partials

Implement the following view partials:

1. **`_post_snap_context.html.erb`**
   - Display snap event date + duration since
   - AWS reconstruction date + duration
   - Eden status (orphaned/reconnected)

2. **`_stat_cards.html.erb`**
   - 4 stat cards: systems_known, active_wormholes, avg_stability, network_health
   - Color-coded by value (green if healthy, orange if caution, red if danger)

3. **`_em_status_panel.html.erb`**
   - Reserves + bar chart
   - Daily harvest/consumption/net flow display
   - Days until critical (red if <30)

4. **`_ai_manager_control.html.erb`**
   - List of directives with status bars
   - Priority queue (next 5 actions)
   - Next tick countdown timer

5. **`_system_card.html.erb`**
   - System name, identifier, mass, stability
   - Celestial bodies list (major only)
   - Settlement count
   - Wormhole connection count + types
   - Post-Snap metadata (if orphaned/reconnected)

6. **`_wormhole_table.html.erb`**
   - Full table with 10 columns (Connection, Type, Stability %, Mass Limit, EM Surplus, Harvest Rate, Satellites, Risk Level, AWS Status, Actions)
   - Row color-coding by type (natural = green accent, artificial = cyan accent)
   - Stability bar visualization
   - Action buttons (Details, Boost Stability, Trigger Snap)

7. **`_hammer_protocol_interface.html.erb`**
   - Red warning styling
   - Current mass limit + transit mass gauge
   - Snap trigger threshold indicator
   - Projected exit coordinates + orphan risk
   - Trigger Snap button (disabled until threshold reached)

8. **`_economic_indices_display.html.erb`**
   - PvE Index with 3 sub-metrics
   - Energy Trade Index with 3 sub-metrics
   - EAP Index with 3 sub-metrics
   - EM Trade Value with trend indicator

### Create: `app/assets/stylesheets/admin_dashboard.scss`

**Required Styling Classes**:
```scss
// Layout
.admin-dashboard { ... }
.admin-header { ... }

// Stats
.stats-row { ... }
.stat-card { ... }
.stat-card.danger { ... }
.stat-card.caution { ... }
.stat-card.healthy { ... }

// Panels
.em-economy-panel { ... }
.ai-manager-panel { ... }
.systems-overview { ... }
.systems-grid { ... }
.system-card { ... }

.wormhole-monitoring { ... }
.monitoring-table { ... }
.wh-row { ... }
.wh-row.natural { ... }
.wh-row.artificial { ... }

.hammer-protocol-panel { ... }
.hammer-stats { ... }
.snap-trigger-btn { ... }
.snap-trigger-btn:disabled { ... }

.economic-indices-panel { ... }
.index-metric { ... }

// Visualizations
.stability-bar { ... }
.em-bar { ... }
.progress-bar { ... }

// Responsive
@media (max-width: 1200px) { ... }
@media (max-width: 768px) { ... }
```

**Color Scheme**:
- Post-Snap context: gold/orange (#ffaa00)
- Natural WH: green (--green)
- Artificial WH: cyan (--accent)
- Hammer Protocol: red (#ff3333) with warning styling
- Health indicators: green healthy, orange caution, red danger

.systems-overview {
  margin-bottom: 40px;
  
  h2 {
    margin-bottom: 20px;
    font-size: 20px;
  }
  
  .systems-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 20px;
    
    .system-overview-card {
      background: var(--panel);
      border: 1px solid var(--line);
      border-radius: 4px;
      overflow: hidden;
      
      .card-header {
        background: var(--panel2);
        padding: 15px;
        border-bottom: 1px solid var(--line);
        
        h3 {
          margin: 0;
          font-size: 18px;
        }
        
        .identifier {
          color: var(--muted);
          font-size: 12px;
          display: block;
          margin-top: 5px;
        }
      }
      
      .card-body {
        padding: 15px;
        
        .stat {
          margin: 8px 0;
          font-size: 14px;
          color: var(--text);
          
          strong {
            color: var(--muted);
          }
        }
      }
      
      .card-footer {
        padding: 10px 15px;
        border-top: 1px solid var(--line);
        background: var(--panel2);
      }
    }
  }
}

.wormhole-monitoring {
  h2 {
    margin-bottom: 20px;
    font-size: 20px;
  }
  
  .monitoring-table {
    width: 100%;
    border-collapse: collapse;
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 4px;
    overflow: hidden;
    
    thead {
      background: var(--panel2);
      border-bottom: 2px solid var(--line);
      
      th {
        padding: 12px;
        text-align: left;
        color: var(--muted);
        font-size: 12px;
        font-weight: bold;
      }
    }
    
    tbody tr {
      border-bottom: 1px solid var(--line);
      
      &:hover {
        background: var(--panel2);
      }
      
      td {
        padding: 12px;
        color: var(--text);
        font-size: 13px;
        
        .stability-bar {
          height: 4px;
          background: var(--bg);
          border-radius: 2px;
          margin-bottom: 3px;
          
          .stability-fill {
            height: 100%;
          }
        }
      }
    }
  }
}
```

---

## Acceptance Criteria

### Core Functionality
- ✅ Dashboard loads for admin users only (requires session[:admin] = true)
- ✅ All data loads from MockDataHelper without errors
- ✅ Page renders without console errors or broken styling
- ✅ Authentication check prevents non-admin access

### System Overview Cards
- ✅ 3 cards displayed (Sol, System B, Eden)
- ✅ Each card shows: name, identifier, system_mass, stability_index, celestial_bodies_count, settlement_count, wormhole_connections
- ✅ Post-Snap context displayed where applicable (orphan status, AWS reconstruction date)
- ✅ Organization logos display correctly (or fallback to placeholder)
- ✅ Color-coding by stability (green 80+, orange 50-80, red <50)

### EM Economy Dashboard
- ✅ Total EM reserves displays with visual bar
- ✅ Daily harvest rate shows +Y EM/day (accurate calculation)
- ✅ Daily consumption shows -Z EM/day (accurate calculation)
- ✅ Net daily flow calculates correctly (harvest - consumption)
- ✅ Days until critical calculates correctly (reserves / deficit)
- ✅ Visual warning bar color-codes: green >60 days, orange 30-60 days, red <30 days
- ✅ All values update if MockDataHelper data changes

### Wormhole Monitoring Table
- ✅ All wormholes display in table format
- ✅ Columns: Connection, Type, Stability %, Mass Limit, EM Surplus, Harvest Rate, Satellites, Risk Level, AWS Status, Actions
- ✅ Mass Limit calculated correctly using formula: (System_Mass × Stability) / EM_Surplus
- ✅ Natural WH rows show harvest rate + satellite count (AWS WH show 0/--)
- ✅ Risk level color-coded: green (safe), orange (caution), red (danger)
- ✅ AWS Status shows "Operational", "Damaged", or "Offline"
- ✅ Natural WH rows styled with green accent, Artificial WH with cyan accent
- ✅ Action buttons functional (Details, Boost Stability, Trigger Snap if eligible)
- ✅ Stability bar visualizes stability_index as horizontal bar with color gradient

### AI Manager Control Panel
- ✅ All directives display with status, progress %, and ETA
- ✅ Progress bars show completion percentage
- ✅ Directives sorted by priority (CRITICAL → HIGH → MEDIUM → LOW)
- ✅ Priority queue displays top 5 actions in order
- ✅ Next tick countdown displays correct time until next AI decision
- ✅ Queued actions list displays accurately

### Hammer Protocol Interface
- ✅ Section displays only if session[:admin] = true
- ✅ Section styled with red/warning color (#ff3333)
- ✅ Current snap readiness status accurate
- ✅ Mass limit vs transit mass displayed with visual gauge
- ✅ Snap trigger threshold shows 94% of mass limit
- ✅ Projected exit coordinates display
- ✅ Orphan risk systems listed (e.g., [Eden])
- ✅ Estimated EM field value shows accurate calculation
- ✅ Trigger Snap button disabled until threshold reached
- ✅ Trigger button shows confirmation modal with consequences

### Economic Indices Display
- ✅ PvE Index displays with 3 sub-metrics (NPC Activity, Market Volatility, Resource Scarcity)
- ✅ Energy Trade Index displays with 3 sub-metrics (Supply, Demand, Margin)
- ✅ EAP Index displays with 3 sub-metrics (Exploration Activity, Alien Presence, Player Engagement)
- ✅ EM Trade Value displays current rate + historical range + trend indicator
- ✅ All values update from MockDataHelper.economic_indices

### Visual Design & Responsiveness
- ✅ All EVE Dashboard CSS variables applied consistently
- ✅ Color scheme: --bg, --panel, --panel2, --text, --muted, --accent, --green, --red, --line
- ✅ Layout responsive: grid → stacked on tablet/mobile
- ✅ Stability bars render correctly on all screen sizes
- ✅ Tables have horizontal scroll on mobile (not text wrap)
- ✅ Section headers clear and readable
- ✅ Buttons accessible and clearly labeled

### Integration & Data Flow
- ✅ MockDataHelper methods called without errors:
  - `wormhole_network()` → systems, wormholes, post_snap_status
  - `em_status()` → reserves, harvest, consumption, net_flow, days_to_critical
  - `ai_manager_status()` → directives, priorities, next_tick
  - `hammer_protocol_status()` → snap readiness, trigger threshold, orphan_risk, em_field_value
  - `economic_indices()` → pve_index, energy_trade_index, eap_index, em_trade_value
- ✅ All data types correct (integers, floats, dates, arrays, hashes)
- ✅ No nil/undefined values displayed
- ✅ Post-Snap context from wormhole_network[:post_snap_status] displayed in header

### Security & Access Control
- ✅ Non-admin users redirected to root_path
- ✅ Hammer Protocol visible only to admins
- ✅ No sensitive data leaked in HTML comments or console
- ✅ Admin session persists across page loads
- ✅ Logout removes admin flag

---

**Status**: ✅ Ready for Qwen Handoff | **Review Gate**: All MockDataHelper methods integrated, admin access secured, Hammer Protocol visible to admins only
