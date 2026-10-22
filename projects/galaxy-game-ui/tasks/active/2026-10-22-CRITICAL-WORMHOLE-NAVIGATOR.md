# Task 6: Wormhole Navigator UI (Post-Snap Network, EM Physics, Hammer Protocol)

**Priority**: CRITICAL | **Complexity**: High | **Time**: 8-10 hours | **Status**: Ready

---

## Overview

Interactive wormhole network visualization for post-Snap Sol system showing:
1. **Natural Wormhole** (Sol ↔ System B): Unstable but free EM harvesting, Snap risk
2. **Artificial Wormhole** (Sol ↔ Eden): AWS-powered, stable, post-Snap reconstruction

Players inherit this network state and experience real wormhole physics: EM harvesting, mass limit mechanics, Snap risk assessment, and (admin-only) Hammer Protocol controls for intentional snap triggers.

---

## Requirements

### 1. Network Visualization (SVG)
- 3 system nodes (Sol, System B, Eden) in triangular layout
- **Natural WH** (Sol-System B): Pulsing dashed line (EM harvesting), color-coded by stability
- **Artificial WH** (Sol-Eden): Solid line with AWS icon, bright cyan (stable)
- Color coding:
  - Green (0.8+): Stable
  - Orange (0.5-0.8): Caution
  - Red (<0.5): Danger
  - Cyan: Artificial (AWS) — always stable
- Animated EM flow visualization on natural WH line (shows harvesting)
- Recapture satellite count badge on natural WH
- Responsive to window resize, draggable nodes

### 2. Wormhole Details Panel (Per Connection)
**Natural WH (Sol-System B)**:
- Stability Index with decay indicator (shows 0.1-1.0 bar)
- Mass Limit formula: (System_Mass × Stability_Index) / EM_Surplus = X tons safe
- EM Reserves current level + recharge rate (e.g., 8500/12000 EM, +150/day)
- EM Harvest Rate: X tons transited → Y EM captured
- Maintenance cost: Z EM/day to prevent collapse
- Recapture Satellites: N active (collecting EM)
- Mass Transferred: Cumulative tons transited (capacity remaining)
- Snap Risk Level: safe/caution/danger badge
- EM Potential: high/medium/low (natural richness)

**Artificial WH (Sol-Eden)**:
- Stability Index (always high, 0.9+)
- Mass Limit (doubled by AWS): (System_Mass × Stability_Index) / EM_Surplus × 2.0
- AWS Status: Operational
- AWS Location: L3 Lagrange Point
- Stabilizer Count: 2 (dual-link)
- Counterbalance Quality: 0.95 (gravitational anchor strength)
- EM Maintenance: Z EM/day
- Safe for Travel: Yes (no snap risk)

### 3. EM Economy Display
Global EM status bar:
- Total Reserves: X EM across all systems
- Daily Harvest: +150 EM (from natural WH recapture)
- Daily Consumption: -650 EM (all maintenance + AWS upkeep)
- Net Flow: -500 EM/day (deficit)
- Days Until Critical: 47 days (when EM reserves hit 0)
- Visual warning if deficit exceeds sustainable rate

### 4. Traversal & Pathfinding
- Select source + destination dropdowns
- Shows available routes (direct or via intermediate systems)
- Highlight path on SVG
- "Navigate" button for each wormhole
- Confirm travel with risk assessment (e.g., "Mass limit 37 tons available")
- Update session location after successful traverse

### 5. Hammer Protocol (Admin Only)
**Hidden by default, admin UI only**:
- "Hammer Protocol" section (admin-only visibility toggle)
- Eligible wormholes for intentional Snap trigger
- Current snap readiness for natural WH:
  - Current mass limit: X tons
  - Current transited mass: Y tons
  - Snap trigger threshold: Z tons (94% of limit)
  - Snap ready: Yes/No
  - Projected new exit coordinates: [lat, lon, depth]
  - Orphan risk systems: [list of systems that disconnect]
  - Estimated EM field value: Z EM (harvestable post-Snap)
- "Trigger Snap" button (requires confirmation, shows consequences)

---

## File Modifications

### `app/controllers/wormholes_controller.rb`
```ruby
class WormholesController < ApplicationController
  skip_before_action :require_authentication  # Mock data for now
  
  def index
    @wormhole_network = MockDataHelper.wormhole_network
    @em_status = MockDataHelper.em_status
    @current_location = session[:current_system_id] || "SOL"
    @systems = @wormhole_network[:systems]
    @wormholes = @wormhole_network[:wormholes]
    @post_snap_context = @wormhole_network[:post_snap_status]
    
    # Admin only
    @is_admin = session[:admin] || false
    @hammer_protocol = MockDataHelper.hammer_protocol_status if @is_admin
  end
  
  def show
    @wormhole = MockDataHelper.wormhole_network[:wormholes].find { |w| w[:id] == params[:id].to_i }
  end
  
  def pathfind
    source = params[:source]
    destination = params[:destination]
    network = MockDataHelper.wormhole_network
    
    # Simple pathfinding (direct or via Sol)
    @path = find_route(source, destination, network)
    @route_wormholes = build_route_details(@path, network[:wormholes])
    
    if @path.empty?
      render json: { error: "No route found" }, status: 404
    else
      render json: { 
        path: @path, 
        wormholes: @route_wormholes,
        route_mass_limit: @route_wormholes.map { |w| w[:mass_limit] }.min
      }
    end
  end
  
  def traverse
    destination = params[:destination]
    network = MockDataHelper.wormhole_network
    
    # Find wormhole for this route
    wh = network[:wormholes].find do |w| 
      (w[:system_a] == session[:current_system_id] && w[:system_b] == destination) ||
      (w[:system_a] == destination && w[:system_b] == session[:current_system_id])
    end
    
    if wh.nil?
      return render json: { error: "Wormhole not found" }, status: 404
    end
    
    # Check snap risk level for confirmation
    if wh[:snap_risk_level] == "danger"
      return render json: { 
        warning: "Wormhole at critical stability! Snap event possible.",
        snap_risk: true,
        requires_confirmation: true
      }, status: 200
    end
    
    # Update session
    session[:current_system_id] = destination
    render json: { success: true, new_location: destination }
  end
  
  def trigger_snap
    return redirect_to root_path unless session[:admin]
    
    wormhole_id = params[:wormhole_id]
    network = MockDataHelper.wormhole_network
    wh = network[:wormholes].find { |w| w[:id] == wormhole_id.to_i }
    
    unless wh && wh[:snap_eligible]
      return render json: { error: "Snap not eligible for this wormhole" }, status: 400
    end
    
    # Simulate snap event
    # In production: trigger event, orphan systems, generate EM field, etc.
    render json: {
      snap_triggered: true,
      wormhole_id: wormhole_id,
      new_exit_coordinates: wh[:snap_coordinates],
      orphaned_systems: ["AOL-732356"],  # Eden temporarily orphaned
      em_field_generated: wh[:estimated_em_field_value]
    }
  end
  
  private
  
  def find_route(source, destination, network)
    return [] if source == destination
    
    # Direct connection
    if network[:wormholes].any? { |w| 
      (w[:system_a] == source && w[:system_b] == destination) ||
      (w[:system_a] == destination && w[:system_b] == source)
    }
      return [source, destination]
    end
    
    # Via Sol (hub)
    if network[:wormholes].any? { |w| (w[:system_a] == source && w[:system_b] == "SOL") || (w[:system_a] == "SOL" && w[:system_b] == source) } &&
       network[:wormholes].any? { |w| (w[:system_a] == "SOL" && w[:system_b] == destination) || (w[:system_a] == destination && w[:system_b] == "SOL") }
      return [source, "SOL", destination]
    end
    
    []
  end
  
  def build_route_details(path, wormholes)
    route = []
    
    path.each_cons(2) do |source, dest|
      wh = wormholes.find { |w| 
        (w[:system_a] == source && w[:system_b] == dest) ||
        (w[:system_a] == dest && w[:system_b] == source)
      }
      route << wh if wh
    end
    
    route
  end
end
```

### `app/views/wormholes/index.html.erb`
```erb
<div class="wormhole-navigator">
  <!-- Header with context -->
  <div class="nav-header">
    <h1>Wormhole Navigator</h1>
    <div class="header-info">
      <p>Current Location: <strong><%= @systems.find { |s| s[:identifier] == @current_location }&.dig(:name) %></strong></p>
      <p class="post-snap-note" v-if="<%= @post_snap_context.present? %>">
        Post-Snap Status: Eden reconnected via AWS <%= time_ago_in_words(Time.parse(@post_snap_context[:aws_reconstruction_date])) %> ago
      </p>
    </div>
  </div>
  
  <!-- EM Economy Status Bar -->
  <div class="em-economy-panel">
    <h3>EM Economy Status</h3>
    <div class="em-stats">
      <div class="em-stat">
        <span class="label">Total Reserves:</span>
        <span class="value"><%= @em_status[:total_em_reserves] %> EM</span>
      </div>
      <div class="em-stat">
        <span class="label">Daily Harvest:</span>
        <span class="value positive">+<%= @em_status[:daily_harvest_rate] %> EM/day</span>
      </div>
      <div class="em-stat">
        <span class="label">Daily Consumption:</span>
        <span class="value negative">-<%= @em_status[:daily_consumption] %> EM/day</span>
      </div>
      <div class="em-stat">
        <span class="label">Net Flow:</span>
        <span class="value <%= @em_status[:net_daily_flow] >= 0 ? 'positive' : 'deficit' %>">
          <%= @em_status[:net_daily_flow] >= 0 ? '+' : '' %><%= @em_status[:net_daily_flow] %> EM/day
        </span>
      </div>
      <div class="em-stat">
        <span class="label">Days Until Critical:</span>
        <span class="value <%= @em_status[:days_until_critical] < 30 ? 'danger' : '' %>">
          <%= @em_status[:days_until_critical] %> days
        </span>
      </div>
    </div>
    <div class="em-bar">
      <div class="em-fill" style="width: <%= (@em_status[:total_em_reserves] / 30000.0 * 100).round %>%"></div>
    </div>
  </div>
  
  <!-- Main Navigation -->
  <div class="nav-container">
    <!-- SVG Visualization -->
    <div class="visualization-panel">
      <h3>Wormhole Network</h3>
      <svg id="network-svg" width="100%" height="500"></svg>
    </div>
    
    <!-- Right Sidebar -->
    <div class="sidebar">
      <!-- Pathfinding -->
      <div class="pathfind-panel">
        <h3>Plot Course</h3>
        
        <div class="form-group">
          <label>Source</label>
          <%= select_tag :source, 
              options_for_select(@systems.map { |s| [s[:name], s[:identifier]] }, @current_location),
              class: "select-input", id: "route-source" %>
        </div>
        
        <div class="form-group">
          <label>Destination</label>
          <%= select_tag :destination,
              options_for_select(@systems.map { |s| [s[:name], s[:identifier]] }),
              class: "select-input", id: "route-destination" %>
        </div>
        
        <button id="find-path-btn" class="btn btn-primary">Find Route</button>
      </div>
      
      <!-- Route Display -->
      <div id="route-display" class="route-panel" style="display: none;">
        <h3>Route Information</h3>
        
        <div id="route-summary" class="route-summary">
          <!-- Populated by JavaScript -->
        </div>
        
        <div id="wormhole-details" class="wormhole-details">
          <!-- Wormhole info for each hop -->
        </div>
      </div>
      
      <!-- Hammer Protocol (Admin Only) -->
      <% if @is_admin && @hammer_protocol.present? %>
        <div class="hammer-protocol-panel">
          <h3>🔨 Hammer Protocol <span class="admin-badge">ADMIN</span></h3>
          
          <div class="hammer-stats">
            <div class="stat">
              <span>Natural WH Status:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:snap_ready] ? "READY" : "standby" %></strong>
            </div>
            <div class="stat">
              <span>Mass Limit:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:current_mass_limit] %> tons</strong>
            </div>
            <div class="stat">
              <span>Current Transit:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:current_transit_mass] %> tons</strong>
            </div>
            <div class="stat">
              <span>Snap Threshold:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:snap_trigger_threshold] %> tons (94%)</strong>
            </div>
            <div class="stat">
              <span>Orphan Risk:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:orphan_risk_systems].join(", ") %></strong>
            </div>
            <div class="stat">
              <span>EM Field Value:</span>
              <strong><%= @hammer_protocol[:system_b_natural_wh][:estimated_em_field_value] %> EM</strong>
            </div>
          </div>
          
          <button id="trigger-snap-btn" class="btn btn-danger" 
                  onclick="confirmSnapTrigger()" 
                  <%= @hammer_protocol[:system_b_natural_wh][:snap_ready] ? '' : 'disabled' %>>
            Trigger Snap Event
          </button>
          <p class="help-text">Initiates controlled Snap event. Orphans connected systems temporarily. Harvests EM field.</p>
        </div>
      <% end %>
    </div>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const network = <%= raw @wormhole_network.to_json %>;
  const emStatus = <%= raw @em_status.to_json %>;
  
  initNetworkVisualization(network);
  
  document.getElementById('find-path-btn').addEventListener('click', findPath);
});

function initNetworkVisualization(network) {
  const svg = document.getElementById('network-svg');
  const width = svg.parentElement.clientWidth;
  const height = 500;
  
  // Clear SVG
  while (svg.firstChild) svg.removeChild(svg.firstChild);
  
  // Layout: Sol center, System B left, Eden right (triangle)
  const positions = {
    'SOL': { x: width * 0.5, y: height * 0.5 },
    [network.systems[1]?.identifier || 'SYSTEM-B']: { x: width * 0.2, y: height * 0.5 },
    'AOL-732356': { x: width * 0.8, y: height * 0.5 }
  };
  
  // Draw wormholes (lines) - natural first (dashed), then artificial (solid)
  network.wormholes.forEach(wh => {
    const p1 = positions[wh.system_a];
    const p2 = positions[wh.system_b];
    
    // Draw line
    const line = document.createElementNS('http://www.w3.org/2000/svg', 'line');
    line.setAttribute('x1', p1.x);
    line.setAttribute('y1', p1.y);
    line.setAttribute('x2', p2.x);
    line.setAttribute('y2', p2.y);
    line.setAttribute('stroke-width', 4);
    
    if (wh.natural) {
      // Natural WH: dashed line, color by stability
      line.setAttribute('class', `wormhole-natural stability-${stabilityLevel(wh.stability_index)}`);
      line.setAttribute('stroke-dasharray', '8,4');
      
      // Add EM flow animation indicator
      const emIndicator = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
      emIndicator.setAttribute('cx', (p1.x + p2.x) / 2);
      emIndicator.setAttribute('cy', (p1.y + p2.y) / 2);
      emIndicator.setAttribute('r', 3);
      emIndicator.setAttribute('class', 'em-flow');
      svg.appendChild(emIndicator);
    } else {
      // Artificial WH: solid line, always cyan (stable)
      line.setAttribute('class', 'wormhole-artificial');
      line.setAttribute('stroke', '#00d4ff');
      
      // Add AWS icon
      const awsIcon = document.createElementNS('http://www.w3.org/2000/svg', 'text');
      awsIcon.setAttribute('x', (p1.x + p2.x) / 2);
      awsIcon.setAttribute('y', (p1.y + p2.y) / 2 - 8);
      awsIcon.setAttribute('text-anchor', 'middle');
      awsIcon.setAttribute('class', 'aws-icon');
      awsIcon.textContent = '⚙';
      svg.appendChild(awsIcon);
    }
    
    svg.appendChild(line);
  });
  
  // Draw system nodes
  network.systems.forEach(sys => {
    const pos = positions[sys.identifier];
    
    const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    circle.setAttribute('cx', pos.x);
    circle.setAttribute('cy', pos.y);
    circle.setAttribute('r', 45);
    circle.setAttribute('class', 'system-node');
    svg.appendChild(circle);
    
    const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
    text.setAttribute('x', pos.x);
    text.setAttribute('y', pos.y + 5);
    text.setAttribute('text-anchor', 'middle');
    text.setAttribute('class', 'system-label');
    text.textContent = sys.name;
    svg.appendChild(text);
  });
}

function stabilityLevel(index) {
  if (index >= 0.8) return 'stable';
  if (index >= 0.5) return 'caution';
  return 'danger';
}

function findPath() {
  const source = document.getElementById('route-source').value;
  const destination = document.getElementById('route-destination').value;
  
  if (source === destination) {
    alert('Select different source and destination');
    return;
  }
  
  fetch(`/wormholes/pathfind?source=${source}&destination=${destination}`)
    .then(r => r.json())
    .then(data => {
      if (data.error) {
        alert(data.error);
        return;
      }
      displayRoute(data);
    })
    .catch(e => alert('Error: ' + e.message));
}

function displayRoute(data) {
  const summary = document.getElementById('route-summary');
  const details = document.getElementById('wormhole-details');
  
  summary.innerHTML = `
    <p><strong>Route:</strong> ${data.path.join(' → ')}</p>
    <p><strong>Hops:</strong> ${data.wormholes.length}</p>
    <p><strong>Route Mass Limit:</strong> ${data.route_mass_limit.toFixed(1)} tons</p>
  `;
  
  details.innerHTML = data.wormholes.map((wh, i) => `
    <div class="wormhole-hop ${wh.natural ? 'natural' : 'artificial'}">
      <h4>
        ${i + 1}. ${wh.system_a} → ${wh.system_b}
        <span class="wh-type ${wh.natural ? 'natural' : 'aws'}">
          ${wh.natural ? 'Natural WH' : 'AWS Link'}
        </span>
      </h4>
      <div class="wh-stats">
        <div class="stat">
          <span>Stability:</span>
          <strong>${(wh.stability_index * 100).toFixed(0)}%</strong>
          <div class="stability-bar">
            <div class="fill" style="width: ${wh.stability_index * 100}%"></div>
          </div>
        </div>
        <div class="stat">
          <span>Mass Limit:</span>
          <strong>${wh.mass_limit.toFixed(1)} tons</strong>
          <small>(System: ${wh.system_mass}T, EM: ${wh.em_surplus}, Stability: ${wh.stability_index})</small>
        </div>
        <div class="stat">
          <span>Risk Level:</span>
          <strong class="risk-${wh.snap_risk_level}">${wh.snap_risk_level.toUpperCase()}</strong>
        </div>
        ${wh.natural ? `
          <div class="stat">
            <span>EM Harvest Rate:</span>
            <strong>+${wh.em_harvest_rate} EM per 1000T</strong>
          </div>
          <div class="stat">
            <span>Recapture Satellites:</span>
            <strong>${wh.recapture_satellites} active</strong>
          </div>
          <div class="stat">
            <span>EM Maintenance:</span>
            <strong>${wh.em_maintenance_cost} EM/day</strong>
          </div>
        ` : `
          <div class="stat">
            <span>AWS Status:</span>
            <strong>${wh.aws_operational ? 'OPERATIONAL' : 'DAMAGED'}</strong>
          </div>
          <div class="stat">
            <span>Stabilizers:</span>
            <strong>${wh.stabilizer_count} (Dual-Link)</strong>
          </div>
        `}
      </div>
      <button class="btn btn-sm btn-navigate" onclick="confirmTraverse('${wh.system_b}')">
        Navigate to <%= @systems.find { |s| s[:identifier] == data.wormholes[i].system_b }&.dig(:name) %>
      </button>
    </div>
  `).join('');
  
  document.getElementById('route-display').style.display = 'block';
}

function confirmTraverse(destination) {
  if (confirm('Confirm travel to ' + destination + '?')) {
    fetch(`/wormholes/traverse`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'X-CSRF-Token': document.querySelector('[name=authenticity_token]')?.value },
      body: JSON.stringify({ destination: destination })
    })
    .then(r => r.json())
    .then(data => {
      if (data.warning) {
        alert(data.warning);
        return;
      }
      alert('Arrived at ' + destination);
      window.location.reload();
    })
    .catch(e => alert('Error: ' + e.message));
  }
}

function confirmSnapTrigger() {
  if (!confirm('WARNING: This will trigger a Snap event, potentially orphaning connected systems. Continue?')) {
    return;
  }
  
  fetch(`/wormholes/trigger_snap`, {
    method: 'POST',
    headers: { 'X-CSRF-Token': document.querySelector('[name=authenticity_token]')?.value },
    body: JSON.stringify({ wormhole_id: 1 })
  })
  .then(r => r.json())
  .then(data => {
    alert('SNAP EVENT TRIGGERED\\nNew exit: ' + data.new_exit_coordinates.join(', ') + '\\nEM field: ' + data.em_field_generated + ' EM');
    window.location.reload();
  });
}
</script>
```

### Create: `app/assets/stylesheets/wormholes.scss`
```scss
.wormhole-navigator {
  padding: 20px;
  background: var(--bg);
  min-height: 100vh;
  font-family: 'Courier New', monospace;
}

.nav-header {
  margin-bottom: 20px;
  padding-bottom: 15px;
  border-bottom: 1px solid var(--line);
  
  h1 {
    color: var(--accent);
    margin: 0 0 10px;
    font-size: 28px;
    text-transform: uppercase;
    letter-spacing: 2px;
  }
  
  .header-info {
    display: flex;
    gap: 30px;
    
    p {
      margin: 0;
      color: var(--muted);
      font-size: 14px;
      
      strong {
        color: var(--text);
      }
    }
    
    .post-snap-note {
      color: #ffaa00;
      font-weight: bold;
    }
  }
}

// EM Economy Status Bar
.em-economy-panel {
  background: var(--panel);
  border: 1px solid var(--line);
  border-radius: 4px;
  padding: 15px;
  margin-bottom: 20px;
  
  h3 {
    margin: 0 0 12px;
    color: var(--accent);
    font-size: 14px;
    text-transform: uppercase;
  }
  
  .em-stats {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 12px;
    margin-bottom: 12px;
  }
  
  .em-stat {
    display: flex;
    flex-direction: column;
    gap: 4px;
    
    .label {
      color: var(--muted);
      font-size: 11px;
      text-transform: uppercase;
    }
    
    .value {
      color: var(--text);
      font-weight: bold;
      font-size: 14px;
      
      &.positive {
        color: var(--green);
      }
      
      &.negative,
      &.deficit {
        color: var(--red);
      }
      
      &.danger {
        color: #ff6600;
        animation: pulse 1s infinite;
      }
    }
  }
  
  .em-bar {
    width: 100%;
    height: 8px;
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 2px;
    overflow: hidden;
    
    .em-fill {
      height: 100%;
      background: linear-gradient(90deg, var(--green) 0%, var(--accent) 50%, #ffaa00 100%);
      transition: width 0.3s ease;
    }
  }
}

// Main Container
.nav-container {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 20px;
}

// Visualization Panel
.visualization-panel {
  background: var(--panel);
  border: 1px solid var(--line);
  border-radius: 4px;
  padding: 20px;
  
  h3 {
    margin: 0 0 15px;
    color: var(--text);
    font-size: 16px;
  }
  
  svg {
    background: var(--panel2);
    border-radius: 4px;
    display: block;
    width: 100%;
    
    // Wormholes
    .wormhole-natural {
      stroke: var(--green);
      fill: none;
      
      &.stability-stable {
        stroke: var(--green);
      }
      
      &.stability-caution {
        stroke: #ffaa00;
      }
      
      &.stability-danger {
        stroke: var(--red);
      }
    }
    
    .wormhole-artificial {
      stroke: var(--accent);
      stroke-width: 3 !important;
      fill: none;
    }
    
    // EM Flow Animation
    .em-flow {
      fill: var(--green);
      animation: em-pulse 2s infinite;
    }
    
    // AWS Icon
    .aws-icon {
      font-size: 16px;
      fill: var(--accent);
    }
    
    // System Nodes
    .system-node {
      fill: var(--panel);
      stroke: var(--accent);
      stroke-width: 2;
      cursor: pointer;
      transition: r 0.2s ease;
      
      &:hover {
        r: 50;
      }
    }
    
    .system-label {
      fill: var(--text);
      font-size: 13px;
      font-weight: bold;
      text-transform: uppercase;
      pointer-events: none;
    }
  }
}

// Sidebar
.sidebar {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.pathfind-panel,
.route-panel,
.hammer-protocol-panel {
  background: var(--panel);
  border: 1px solid var(--line);
  border-radius: 4px;
  padding: 15px;
  
  h3 {
    margin: 0 0 12px;
    color: var(--text);
    font-size: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  
  .admin-badge {
    font-size: 10px;
    background: #ff6600;
    color: white;
    padding: 2px 6px;
    border-radius: 2px;
    font-weight: bold;
  }
}

// Pathfind Form
.form-group {
  margin-bottom: 10px;
  
  label {
    display: block;
    color: var(--muted);
    font-size: 11px;
    text-transform: uppercase;
    margin-bottom: 4px;
  }
  
  .select-input {
    width: 100%;
    padding: 8px;
    background: var(--panel2);
    border: 1px solid var(--line);
    color: var(--text);
    border-radius: 3px;
    font-family: 'Courier New', monospace;
    font-size: 12px;
    
    option {
      background: var(--panel2);
      color: var(--text);
    }
  }
}

.btn {
  padding: 8px 12px;
  border: none;
  border-radius: 3px;
  font-family: 'Courier New', monospace;
  font-size: 12px;
  cursor: pointer;
  text-transform: uppercase;
  transition: all 0.2s ease;
  
  &.btn-primary {
    width: 100%;
    background: var(--accent);
    color: var(--bg);
    font-weight: bold;
    
    &:hover {
      background: #00ffff;
      box-shadow: 0 0 10px var(--accent);
    }
  }
  
  &.btn-sm {
    padding: 4px 8px;
    font-size: 11px;
  }
  
  &.btn-navigate {
    background: var(--green);
    color: var(--bg);
    
    &:hover {
      background: #00ff00;
      box-shadow: 0 0 8px var(--green);
    }
  }
  
  &.btn-danger {
    width: 100%;
    background: var(--red);
    color: white;
    font-weight: bold;
    
    &:hover:not(:disabled) {
      background: #ff3333;
      box-shadow: 0 0 10px var(--red);
    }
    
    &:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  }
}

// Route Display
.route-panel {
  max-height: 600px;
  overflow-y: auto;
  
  #route-display {
    margin: 0;
    padding: 0;
    border: none;
  }
}

.route-summary {
  background: var(--panel2);
  padding: 10px;
  border: 1px solid var(--line);
  border-radius: 3px;
  margin-bottom: 12px;
  
  p {
    margin: 4px 0;
    color: var(--muted);
    font-size: 12px;
    
    strong {
      color: var(--accent);
    }
  }
}

.wormhole-details {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.wormhole-hop {
  background: var(--panel2);
  padding: 10px;
  border: 1px solid var(--line);
  border-left: 3px solid var(--green);
  border-radius: 3px;
  
  &.artificial {
    border-left-color: var(--accent);
  }
  
  h4 {
    margin: 0 0 8px;
    color: var(--text);
    font-size: 12px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    
    .wh-type {
      font-size: 9px;
      padding: 2px 6px;
      border-radius: 2px;
      background: var(--panel);
      color: var(--muted);
      
      &.natural {
        color: var(--green);
      }
      
      &.aws {
        color: var(--accent);
      }
    }
  }
  
  .wh-stats {
    display: flex;
    flex-direction: column;
    gap: 6px;
    margin-bottom: 10px;
    font-size: 12px;
    
    .stat {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      gap: 8px;
      
      span {
        color: var(--muted);
        flex-shrink: 0;
      }
      
      strong {
        color: var(--text);
        text-align: right;
        flex-grow: 1;
      }
      
      small {
        display: block;
        width: 100%;
        color: var(--muted);
        font-size: 10px;
        margin-top: 2px;
      }
      
      .stability-bar {
        width: 100%;
        height: 4px;
        background: var(--panel);
        border: 1px solid var(--line);
        border-radius: 2px;
        overflow: hidden;
        margin-top: 2px;
        
        .fill {
          height: 100%;
          background: linear-gradient(90deg, var(--red), #ffaa00, var(--green));
        }
      }
      
      .risk-safe {
        color: var(--green);
      }
      
      .risk-caution {
        color: #ffaa00;
      }
      
      .risk-danger {
        color: var(--red);
        font-weight: bold;
      }
    }
  }
}

// Hammer Protocol Panel
.hammer-protocol-panel {
  border-color: #ff6600;
  border-width: 2px;
  background: rgba(255, 102, 0, 0.05);
  
  h3 {
    color: #ff6600;
    text-shadow: 0 0 5px rgba(255, 102, 0, 0.5);
  }
  
  .hammer-stats {
    display: flex;
    flex-direction: column;
    gap: 8px;
    margin-bottom: 12px;
    background: var(--panel2);
    padding: 10px;
    border: 1px solid #ff6600;
    border-radius: 3px;
    
    .stat {
      display: flex;
      justify-content: space-between;
      font-size: 12px;
      gap: 8px;
      
      span {
        color: var(--muted);
      }
      
      strong {
        color: #ff6600;
        text-align: right;
        flex-shrink: 0;
      }
    }
  }
  
  .help-text {
    margin: 8px 0 0 0;
    font-size: 11px;
    color: var(--muted);
  }
}

// Animations
@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

@keyframes em-pulse {
  0% {
    r: 2;
    opacity: 1;
  }
  50% {
    r: 4;
  }
  100% {
    r: 2;
    opacity: 0;
  }
}

// Responsive
@media (max-width: 1200px) {
  .nav-container {
    grid-template-columns: 1fr;
  }
  
  .sidebar {
    order: -1;
  }
}

@media (max-width: 768px) {
  .nav-header .header-info {
    flex-direction: column;
    gap: 8px;
  }
  
  .em-stats {
    grid-template-columns: repeat(2, 1fr) !important;
  }
}
```

---

## Acceptance Criteria

- ✅ SVG visualization renders 3 systems (Sol, System B, Eden) with triangular layout
- ✅ Natural WH (Sol-System B) rendered as dashed line with EM flow animation
- ✅ Artificial WH (Sol-Eden) rendered as solid cyan line with AWS icon
- ✅ Color coding by stability: green (0.8+), orange (0.5-0.8), red (<0.5)
- ✅ EM Economy status bar displays total reserves, harvest rate, consumption, net flow, days to critical
- ✅ EM bar fills progressively based on total reserves / 30000
- ✅ Pathfinding works: direct routes or via Sol (hub)
- ✅ Wormhole details show all physics: stability, mass limit formula, EM metrics
- ✅ Natural WH details include: harvest rate, recapture satellites, maintenance cost, EM potential
- ✅ Artificial WH details include: AWS status, stabilizers, counterbalance quality
- ✅ Traversal updates player location correctly
- ✅ Traversal warns if snap_risk_level is "danger" (requires confirmation)
- ✅ Hammer Protocol (admin only): displays snap readiness, trigger threshold, orphan risk systems, EM field value
- ✅ Hammer Protocol "Trigger Snap" button initiates snap event (requires confirmation)
- ✅ Responsive layout (grid → stacked on mobile)
- ✅ Post-Snap context displayed in header (Eden reconnection timestamp)
- ✅ No console errors, all data loads correctly from MockDataHelper

---

**Status**: ✅ Ready for implementation | **Review Gate**: Wormhole physics accurate, Snap mechanics functional, admin interface secure
