# Task 3: Dashboard Implementation (5-Panel Layout with Real Data)

**Priority**: HIGH  
**Complexity**: Medium-High  
**Estimated Time**: 6-8 hours  
**Status**: Ready for Implementation  
**Date Created**: 2026-10-22  

---

## Overview

Implement consolidated player dashboard with 5 main panels using real Galaxy Game data:
1. **Panel H** (Location Header): Current system, character info, balance
2. **Panel 1** (Bay Inventory): Resources, materials, items
3. **Panel 2** (Fleet Status): Ships, condition, fuel, location
4. **Panel 3** (Production Queue): Manufacturing, research projects
5. **Panel 4** (Wormhole Quick View): Available connections from current system
6. **Panel 5** (Market/Orders): Quick trade view (optional for MVP)

Uses EVE Dashboard CSS layout (topbar + main-inner + panels) + Galaxy Game data.

---

## Current State

- Dashboard partially exists (`app/views/dashboard/index.html.erb`)
- Panel H + Panel 1 partially implemented (with broken styling)
- Panels 2-5 incomplete
- No integration with MockDataHelper yet
- No wormhole or production queue data

---

## Requirements

### 1. Topbar (Navigation)

**Menu Items** (EVE Dashboard style):
```
HOME | LOCATION | FLEET | INVENTORY | PRODUCTION | MARKET | MESSAGES | SETTINGS
```

**Right Side**:
- System selector dropdown (Sol/Eden/System B)
- Player name/status
- Logout button

**Code**:
```erb
<div class="topbar">
  <div class="topbar-left">
    <a href="<%= dashboard_path %>" class="topbar-logo">Galaxy</a>
    <nav class="topbar-nav">
      <%= link_to "HOME", dashboard_path, class: "topbar-link active" %>
      <%= link_to "LOCATION", "#", class: "topbar-link" %>
      <%= link_to "FLEET", "#", class: "topbar-link" %>
      <%= link_to "INVENTORY", "#", class: "topbar-link" %>
      <%= link_to "PRODUCTION", "#", class: "topbar-link" %>
      <%= link_to "MARKET", "#", class: "topbar-link" %>
      <%= link_to "MESSAGES", "#", class: "topbar-link" %>
      <%= link_to "SETTINGS", "#", class: "topbar-link" %>
    </nav>
  </div>
  
  <div class="topbar-right">
    <%= render "system_selector" %>
    <span class="topbar-player"><%= @player.name %></span>
    <%= link_to "Logout", session_path, method: :delete, class: "btn sm" %>
  </div>
</div>
```

### 2. Panel H (Location Header)

**Data**:
```ruby
{
  player_name: "Commander Name",
  current_system: "Sol",
  current_settlement: "Luna Base",
  balance: 50000,
  currency: "Credits",
  status: "Connected",
  online_time: "2h 34m"
}
```

**Display**:
- Character name + avatar placeholder
- Current location (System + Settlement)
- Balance/resources
- Connection status indicator (green dot = online)

**Code**:
```erb
<div class="panel-h location-header">
  <div class="header-left">
    <div class="player-avatar">🚀</div>
    <div class="player-info">
      <h2><%= @player.name %></h2>
      <p class="location-text">
        <%= @current_system %> / <%= @current_settlement %>
      </p>
    </div>
  </div>
  
  <div class="header-right">
    <div class="balance-card">
      <span class="label">Balance</span>
      <span class="amount"><%= number_to_currency(@player.balance) %></span>
    </div>
    <div class="status-indicator">
      <span class="dot online"></span>
      Connected
    </div>
  </div>
</div>
```

### 3. Panel 1 (Bay Inventory)

**Data Source**: `MockDataHelper.bay_inventory()`

**Display**:
- Grid/list of items with quantities
- Filter by material type (metals, gases, processed, etc.)
- Item icons/thumbnails (from asset map)
- Total storage capacity

**Code**:
```erb
<div class="panel panel-1 bay-inventory">
  <div class="panel-header">
    <h3>Bay Inventory</h3>
    <div class="panel-controls">
      <%= select_tag :filter_type, 
          options_for_select([
            ["All Items", ""],
            ["Metals", "metal"],
            ["Gases", "gas"],
            ["Processed", "processed_material"]
          ]), 
          class: "filter-select" %>
    </div>
  </div>
  
  <div class="panel-body">
    <div class="inventory-grid">
      <% @inventory_items.each do |item| %>
        <div class="inventory-item">
          <div class="item-image">
            <% if item[:image_path] %>
              <%= image_tag item[:image_path] %>
            <% else %>
              <div class="placeholder">No Image</div>
            <% end %>
          </div>
          <div class="item-info">
            <p class="item-name"><%= item[:name] %></p>
            <p class="item-amount"><%= item[:amount] %> units</p>
            <p class="item-type"><%= item[:material_type] %></p>
          </div>
        </div>
      <% end %>
    </div>
    
    <div class="storage-bar">
      <div class="storage-info">
        <span>Storage Used:</span>
        <span><%= @storage_used %> / <%= @storage_total %> m³</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" style="width: <%= (@storage_used.to_f / @storage_total) * 100 %>%"></div>
      </div>
    </div>
  </div>
</div>
```

### 4. Panel 2 (Fleet Status)

**Data Source**: `MockDataHelper.player_ships()`

**Display**:
- List of spacecraft/ships
- Condition/health bar
- Fuel status
- Current location
- Crew count

**Code**:
```erb
<div class="panel panel-2 fleet-status">
  <div class="panel-header">
    <h3>Fleet Status</h3>
    <span class="ship-count"><%= @ships.count %> Ships</span>
  </div>
  
  <div class="panel-body">
    <div class="ships-list">
      <% @ships.each do |ship| %>
        <div class="ship-row">
          <div class="ship-icon">
            <% if ship[:image_path] %>
              <%= image_tag ship[:image_path], class: "craft-icon" %>
            <% else %>
              <div class="craft-placeholder">🛸</div>
            <% end %>
          </div>
          
          <div class="ship-details">
            <p class="ship-name"><%= ship[:name] %></p>
            <p class="ship-type"><%= ship[:craft_type] %></p>
          </div>
          
          <div class="ship-metrics">
            <div class="metric">
              <span class="label">Health</span>
              <div class="progress-bar small">
                <div class="progress-fill" style="width: <%= ship[:condition] %>%"></div>
              </div>
            </div>
            
            <div class="metric">
              <span class="label">Fuel</span>
              <div class="progress-bar small">
                <div class="progress-fill" style="width: <%= (ship[:current_fuel].to_f / ship[:fuel_capacity]) * 100 %>%"></div>
              </div>
            </div>
          </div>
          
          <div class="ship-status">
            <span class="location"><%= ship[:location] %></span>
          </div>
        </div>
      <% end %>
    </div>
  </div>
</div>
```

### 5. Panel 3 (Production Queue)

**Data Source**: Active manufacturing + research from database (or MockDataHelper)

**Display**:
- Active manufacturing projects
- Research projects with progress
- Time to completion
- Cancel button

**Code**:
```erb
<div class="panel panel-3 production-queue">
  <div class="panel-header">
    <h3>Production Queue</h3>
    <span class="queue-count"><%= @production_queue.count %> Active</span>
  </div>
  
  <div class="panel-body">
    <% if @production_queue.any? %>
      <div class="queue-list">
        <% @production_queue.each do |job| %>
          <div class="queue-item">
            <div class="job-header">
              <p class="job-name"><%= job[:name] %></p>
              <span class="job-type"><%= job[:type] %></span>
            </div>
            
            <div class="job-progress">
              <div class="progress-bar">
                <div class="progress-fill" style="width: <%= job[:progress] %>%"></div>
              </div>
              <span class="progress-text"><%= job[:progress] %>%</span>
            </div>
            
            <div class="job-time">
              <span class="label">Time Remaining</span>
              <span class="time"><%= job[:time_remaining] %></span>
            </div>
          </div>
        <% end %>
      </div>
    <% else %>
      <div class="empty-state">
        <p>No active production jobs</p>
        <%= link_to "Start Manufacturing", "#", class: "btn sm" %>
      </div>
    <% end %>
  </div>
</div>
```

### 6. Panel 4 (Wormhole Quick View)

**Data Source**: `MockDataHelper.wormhole_network()` filtered for current system

**Display**:
- Available wormholes from current location
- Destination system
- Stability indicator (color bar: green/orange/red)
- Mass limit status
- Quick navigate button

**Code**:
```erb
<div class="panel panel-4 wormhole-quick">
  <div class="panel-header">
    <h3>Wormhole Connections</h3>
  </div>
  
  <div class="panel-body">
    <% if @available_wormholes.any? %>
      <div class="wormholes-list">
        <% @available_wormholes.each do |wh| %>
          <div class="wormhole-item">
            <div class="wh-header">
              <p class="wh-destination"><%= wh[:destination_system] %></p>
              <span class="wh-type">
                <%= wh[:natural] ? "Natural" : "Artificial" %>
              </span>
            </div>
            
            <div class="stability-bar">
              <div class="stability-fill" style="width: <%= wh[:stability_index] * 100 %>%; background: <%= stability_color(wh[:stability_index]) %>"></div>
            </div>
            
            <div class="wh-metrics">
              <span class="metric">
                Stability: <%= (wh[:stability_index] * 100).round %>%
              </span>
              <span class="metric">
                Mass Limit: <%= wh[:mass_limit] %> tons
              </span>
            </div>
            
            <% if wh[:safe_for_travel] %>
              <%= link_to "Navigate", wormholes_path(destination: wh[:destination]), class: "btn sm" %>
            <% else %>
              <span class="btn sm danger disabled">Cannot Traverse</span>
            <% end %>
          </div>
        <% end %>
      </div>
    <% else %>
      <div class="empty-state">
        <p>No wormholes available from this location</p>
      </div>
    <% end %>
  </div>
</div>
```

---

## File Modifications

### 1. Update: `app/controllers/dashboard_controller.rb`

```ruby
class DashboardController < ApplicationController
  def index
    @player = current_player
    @current_system = session[:current_system_id] || "SOL"
    @current_settlement = MockDataHelper.system_status(@current_system)[:settlement]
    
    # Get all data from MockDataHelper
    @system_status = MockDataHelper.system_status(@current_system)
    @inventory_items = MockDataHelper.bay_inventory
    @ships = MockDataHelper.player_ships
    @wormhole_data = MockDataHelper.wormhole_network
    @available_wormholes = filter_wormholes_by_location(@current_system)
    @production_queue = MockDataHelper.active_productions || []
    
    # Storage calculations
    @storage_used = @inventory_items.sum { |i| i[:amount] * 0.5 }  # simplified
    @storage_total = 1000
  end
  
  private
  
  def filter_wormholes_by_location(system_id)
    @wormhole_data[:wormholes].select do |wh|
      wh[:system_a] == system_id || wh[:system_b] == system_id
    end
  end
end
```

### 2. Update: `app/views/dashboard/index.html.erb`

Replace with full 5-panel layout (see code above).

### 3. Create: `app/assets/stylesheets/dashboard.scss`

```scss
// Dashboard layout
.dashboard-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background: var(--bg);
}

.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 64px;
  background: var(--panel);
  border-bottom: 1px solid var(--line);
  padding: 0 20px;
  flex-shrink: 0;
  
  .topbar-nav {
    display: flex;
    gap: 20px;
    margin-left: 30px;
    
    .topbar-link {
      color: var(--muted);
      text-decoration: none;
      font-size: 12px;
      transition: color 0.2s;
      
      &:hover, &.active {
        color: var(--accent);
      }
    }
  }
  
  .topbar-right {
    display: flex;
    gap: 20px;
    align-items: center;
  }
}

.main-inner {
  flex: 1;
  display: flex;
  overflow: hidden;
  gap: 10px;
  padding: 10px;
}

// Panels
.panel {
  background: var(--panel);
  border: 1px solid var(--line);
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  min-width: 300px;
  
  .panel-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px;
    border-bottom: 1px solid var(--line);
    flex-shrink: 0;
    
    h3 {
      margin: 0;
      color: var(--text);
      font-size: 16px;
    }
  }
  
  .panel-body {
    flex: 1;
    overflow-y: auto;
    padding: 15px;
  }
}

// Panel H (Location Header) - full width
.panel-h {
  grid-column: 1 / -1;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  margin-bottom: 10px;
  
  .header-left {
    display: flex;
    gap: 20px;
    align-items: center;
    
    .player-avatar {
      font-size: 48px;
    }
    
    .player-info h2 {
      margin: 0;
      color: var(--text);
    }
    
    .location-text {
      margin: 5px 0 0;
      color: var(--muted);
      font-size: 14px;
    }
  }
  
  .header-right {
    display: flex;
    gap: 30px;
    
    .balance-card {
      text-align: right;
      
      .label {
        display: block;
        color: var(--muted);
        font-size: 12px;
        margin-bottom: 5px;
      }
      
      .amount {
        display: block;
        color: var(--accent);
        font-size: 20px;
        font-weight: bold;
      }
    }
    
    .status-indicator {
      display: flex;
      align-items: center;
      gap: 10px;
      color: var(--text);
      
      .dot {
        width: 12px;
        height: 12px;
        border-radius: 50%;
        
        &.online {
          background: var(--green);
          box-shadow: 0 0 8px var(--green);
        }
      }
    }
  }
}

// Inventory grid
.inventory-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 10px;
  margin-bottom: 15px;
  
  .inventory-item {
    background: var(--panel2);
    border: 1px solid var(--line);
    padding: 10px;
    border-radius: 4px;
    text-align: center;
    
    .item-image {
      width: 100%;
      height: 80px;
      background: var(--bg);
      border-radius: 3px;
      margin-bottom: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      
      img {
        max-width: 100%;
        max-height: 100%;
      }
      
      .placeholder {
        color: var(--muted);
        font-size: 11px;
      }
    }
    
    .item-name {
      margin: 5px 0 2px;
      color: var(--text);
      font-size: 12px;
      font-weight: bold;
    }
    
    .item-amount {
      margin: 2px 0;
      color: var(--accent);
      font-size: 14px;
    }
    
    .item-type {
      margin: 2px 0 0;
      color: var(--muted);
      font-size: 11px;
    }
  }
}

// Storage bar
.storage-bar {
  padding-top: 15px;
  border-top: 1px solid var(--line);
  
  .storage-info {
    display: flex;
    justify-content: space-between;
    color: var(--muted);
    font-size: 12px;
    margin-bottom: 8px;
  }
  
  .progress-bar {
    height: 6px;
    background: var(--bg);
    border-radius: 3px;
    overflow: hidden;
    
    .progress-fill {
      height: 100%;
      background: linear-gradient(90deg, var(--accent), var(--green));
      transition: width 0.3s;
    }
  }
}

// Ships list
.ships-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  
  .ship-row {
    display: grid;
    grid-template-columns: 50px 1fr auto 1fr;
    gap: 15px;
    align-items: center;
    padding: 12px;
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 4px;
    
    .ship-icon {
      width: 50px;
      height: 50px;
      background: var(--bg);
      border-radius: 3px;
      display: flex;
      align-items: center;
      justify-content: center;
      
      img {
        max-width: 100%;
        max-height: 100%;
      }
      
      .craft-placeholder {
        font-size: 24px;
      }
    }
    
    .ship-details {
      .ship-name {
        margin: 0;
        color: var(--text);
        font-weight: bold;
      }
      
      .ship-type {
        margin: 3px 0 0;
        color: var(--muted);
        font-size: 12px;
      }
    }
    
    .ship-metrics {
      display: flex;
      flex-direction: column;
      gap: 8px;
      
      .metric {
        display: flex;
        flex-direction: column;
        gap: 3px;
        
        .label {
          color: var(--muted);
          font-size: 11px;
        }
        
        .progress-bar.small {
          height: 4px;
          width: 100px;
        }
      }
    }
    
    .ship-status {
      text-align: right;
      
      .location {
        color: var(--accent);
        font-size: 12px;
      }
    }
  }
}

// Production queue
.queue-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  
  .queue-item {
    padding: 12px;
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 4px;
    
    .job-header {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      
      .job-name {
        margin: 0;
        color: var(--text);
        font-weight: bold;
      }
      
      .job-type {
        background: rgba(0, 255, 100, 0.2);
        color: var(--green);
        padding: 2px 8px;
        border-radius: 2px;
        font-size: 11px;
      }
    }
    
    .job-progress {
      display: flex;
      gap: 10px;
      align-items: center;
      margin-bottom: 8px;
      
      .progress-bar {
        flex: 1;
      }
      
      .progress-text {
        color: var(--text);
        font-size: 12px;
        min-width: 40px;
        text-align: right;
      }
    }
    
    .job-time {
      display: flex;
      justify-content: space-between;
      color: var(--muted);
      font-size: 12px;
      
      .time {
        color: var(--accent);
      }
    }
  }
}

// Wormhole quick view
.wormholes-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  
  .wormhole-item {
    padding: 12px;
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 4px;
    
    .wh-header {
      display: flex;
      justify-content: space-between;
      margin-bottom: 10px;
      
      .wh-destination {
        margin: 0;
        color: var(--text);
        font-weight: bold;
      }
      
      .wh-type {
        color: var(--muted);
        font-size: 11px;
      }
    }
    
    .stability-bar {
      height: 6px;
      background: var(--bg);
      border-radius: 3px;
      margin-bottom: 8px;
      overflow: hidden;
      
      .stability-fill {
        height: 100%;
      }
    }
    
    .wh-metrics {
      display: flex;
      justify-content: space-between;
      font-size: 11px;
      color: var(--muted);
      margin-bottom: 10px;
    }
    
    .btn {
      width: 100%;
      padding: 6px 12px;
      text-align: center;
    }
  }
}

// Responsive layout
@media (max-width: 1400px) {
  .main-inner {
    flex-wrap: wrap;
  }
  
  .panel {
    flex: 1 1 calc(50% - 5px);
    min-width: 250px;
  }
}

@media (max-width: 768px) {
  .topbar {
    flex-direction: column;
    height: auto;
    gap: 10px;
    padding: 10px;
    
    .topbar-nav {
      margin-left: 0;
      gap: 10px;
      order: 2;
      width: 100%;
      flex-wrap: wrap;
    }
  }
  
  .main-inner {
    flex-direction: column;
  }
  
  .panel {
    flex: 1 1 auto;
  }
}
```

---

## Acceptance Criteria

- ✅ Dashboard loads for authenticated users only
- ✅ Topbar renders with 8 menu items + system selector
- ✅ All 5 panels display (Panel H full-width, Panels 1-4 grid)
- ✅ Real data loads from MockDataHelper methods
- ✅ Inventory grid shows items with fallback placeholders
- ✅ Fleet ships display with health/fuel progress bars
- ✅ Production queue shows active jobs or empty state
- ✅ Wormhole quick view shows connections with stability colors (green/orange/red)
- ✅ Storage bar displays correctly
- ✅ Responsive layout on mobile (single column)
- ✅ EVE Dashboard CSS colors/styling applied consistently
- ✅ No errors, all data renders correctly

---

## References

- **MockDataHelper**: All 6 methods (system_status, bay_inventory, player_ships, npc_buy_orders, active_missions, wormhole_network)
- **EVE CSS**: --bg, --panel, --panel2, --text, --muted, --accent, --green, --red
- **Wormhole Physics**: Mass Limit = (System_Mass × Stability_Index) / EM_Surplus
- **Current System**: Stored in session[:current_system_id]

---

**Status**: ✅ Ready for implementation  
**Assigned To**: Implementation Agent  
**Review Gate**: Verify all panels load, responsive design works, no missing data
