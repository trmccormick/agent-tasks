# Task 4: Blueprints Catalog UI (Item Cards, Materials, Research)

**Priority**: HIGH  
**Complexity**: Medium  
**Estimated Time**: 4-5 hours  
**Status**: Ready for Implementation  
**Date Created**: 2026-10-22  

---

## Overview

Implement searchable blueprints catalog showing:
1. Blueprint list/cards with filtering
2. Detailed blueprint view (materials, research level, efficiencies)
3. Material requirements display
4. Integration with ItemLookupService for material data
5. Asset fallbacks for blueprint icons

Uses EVE Dashboard panel styling + Galaxy Game blueprint data.

---

## Current State

- No blueprints page exists
- MockDataHelper.blueprints() method available (Task 1)
- No asset images for blueprints yet
- Menu item placeholder exists ("PRODUCTION")

---

## Requirements

### 1. Blueprints Index View

**Route**: `GET /blueprints`

**Display**:
- Searchable grid of blueprint cards
- Filter by technology level (basic, intermediate, advanced)
- Sort by name, tech level, research requirement
- Card shows: name, image, brief description, tech level icon

**Data Structure**:
```ruby
{
  id: 1,
  name: "Spaceship Blueprint",
  description: "Construct a basic spaceship",
  technology_level: "basic",
  research_level_required: 2,
  production_time_hours: 48,
  materials_count: 3,
  image_path: nil  # Use placeholder for MVP
}
```

**Code**:
```erb
<div class="blueprints-container">
  <div class="topbar-blueprints">
    <h1>Blueprints Catalog</h1>
    
    <div class="search-filter">
      <%= form_with url: blueprints_path, method: :get, local: true do |f| %>
        <%= f.text_field :search, placeholder: "Search blueprints...", class: "search-input" %>
        
        <%= f.select :tech_level, 
            options_for_select([
              ["All Levels", ""],
              ["Basic", "basic"],
              ["Intermediate", "intermediate"],
              ["Advanced", "advanced"]
            ], params[:tech_level]),
            { class: "filter-select" } %>
        
        <%= f.select :sort, 
            options_for_select([
              ["Name (A-Z)", "name"],
              ["Tech Level", "tech_level"],
              ["Research Required", "research_level"]
            ], params[:sort] || "name"),
            { class: "filter-select" } %>
        
        <%= f.submit "Filter", class: "btn sm" %>
      <% end %>
    </div>
  </div>
  
  <div class="blueprints-grid">
    <% @blueprints.each do |blueprint| %>
      <div class="blueprint-card">
        <div class="card-image">
          <% if blueprint[:image_path] %>
            <%= image_tag blueprint[:image_path] %>
          <% else %>
            <div class="placeholder">
              <span class="tech-level <%= blueprint[:technology_level] %>">
                <%= blueprint[:technology_level].upcase %>
              </span>
            </div>
          <% end %>
        </div>
        
        <div class="card-body">
          <h3><%= blueprint[:name] %></h3>
          <p class="card-description"><%= blueprint[:description] %></p>
          
          <div class="card-stats">
            <span class="stat">
              <strong><%= blueprint[:materials_count] %></strong> Materials
            </span>
            <span class="stat">
              <strong><%= blueprint[:production_time_hours] %></strong> Hours
            </span>
          </div>
          
          <div class="card-footer">
            <span class="research-req">
              Research: <%= blueprint[:research_level_required] %>
            </span>
            <%= link_to "View Details", blueprint_path(blueprint[:id]), 
                class: "btn sm" %>
          </div>
        </div>
      </div>
    <% end %>
  </div>
</div>
```

### 2. Blueprint Detail View

**Route**: `GET /blueprints/:id`

**Display**:
- Full blueprint information
- Material requirements table (name, amount, source)
- Efficiencies (time, material)
- Research levels and progression
- Licensed runs remaining (if applicable)
- Estimated costs calculator

**Data Structure**:
```ruby
{
  id: 1,
  name: "Spaceship Blueprint",
  description: "Construct a basic spaceship for interplanetary travel",
  technology_level: "basic",
  research_level_required: 2,
  current_research_level: 1,
  production_time_hours: 48,
  output_quantity: 1,
  material_efficiency: 0.92,  # 92% efficiency (8% waste)
  time_efficiency: 0.95,  # 95% efficiency (5% slower)
  licensed_runs_remaining: 5,  # nil = unlimited
  materials: [
    {
      item_name: "Steel Ingot",
      amount: 150,
      unit_price: 10,
      subtotal: 1500
    },
    # ... more materials
  ],
  image_path: nil
}
```

**Code**:
```erb
<div class="blueprint-detail">
  <div class="detail-header">
    <div class="detail-image">
      <% if @blueprint[:image_path] %>
        <%= image_tag @blueprint[:image_path] %>
      <% else %>
        <div class="placeholder large">
          <span class="tech-level <%= @blueprint[:technology_level] %>">
            <%= @blueprint[:technology_level].upcase %>
          </span>
        </div>
      <% end %>
    </div>
    
    <div class="detail-info">
      <h1><%= @blueprint[:name] %></h1>
      <p class="detail-description"><%= @blueprint[:description] %></p>
      
      <div class="detail-stats">
        <div class="stat-box">
          <span class="label">Production Time</span>
          <span class="value"><%= @blueprint[:production_time_hours] %> Hours</span>
        </div>
        
        <div class="stat-box">
          <span class="label">Output Quantity</span>
          <span class="value"><%= @blueprint[:output_quantity] %> Unit</span>
        </div>
        
        <div class="stat-box">
          <span class="label">Material Efficiency</span>
          <span class="value"><%= (@blueprint[:material_efficiency] * 100).round %>%</span>
        </div>
        
        <div class="stat-box">
          <span class="label">Time Efficiency</span>
          <span class="value"><%= (@blueprint[:time_efficiency] * 100).round %>%</span>
        </div>
      </div>
      
      <% if @blueprint[:licensed_runs_remaining] %>
        <div class="licensed-runs">
          <p>Licensed Runs Remaining: <strong><%= @blueprint[:licensed_runs_remaining] %></strong></p>
        </div>
      <% end %>
      
      <%= link_to "Manufacture Item", new_manufacturing_path(blueprint_id: @blueprint[:id]), 
          class: "btn btn-primary" %>
    </div>
  </div>
  
  <div class="materials-section">
    <h2>Materials Required</h2>
    
    <table class="materials-table">
      <thead>
        <tr>
          <th>Item</th>
          <th>Amount</th>
          <th>Unit Price</th>
          <th>Subtotal</th>
          <th>In Stock</th>
        </tr>
      </thead>
      <tbody>
        <% @blueprint[:materials].each do |material| %>
          <tr class="material-row">
            <td class="item-cell">
              <div class="item-link">
                <%= link_to material[:item_name], item_path(material[:item_name]) %>
              </div>
            </td>
            <td><%= material[:amount] %> units</td>
            <td><%= number_to_currency(material[:unit_price]) %></td>
            <td><strong><%= number_to_currency(material[:subtotal]) %></strong></td>
            <td>
              <% in_stock = current_player.inventory.find_by(name: material[:item_name])&.amount || 0 %>
              <span class="stock-indicator <%= in_stock >= material[:amount] ? 'sufficient' : 'insufficient' %>">
                <%= in_stock %>
              </span>
            </td>
          </tr>
        <% end %>
      </tbody>
    </table>
    
    <div class="materials-summary">
      <p>Total Cost: <strong><%= number_to_currency(@blueprint[:total_cost]) %></strong></p>
    </div>
  </div>
  
  <div class="research-section">
    <h2>Research & Improvements</h2>
    
    <div class="research-box">
      <p>Current Research Level: <strong><%= @blueprint[:current_research_level] %></strong></p>
      <p>Required Research Level: <strong><%= @blueprint[:research_level_required] %></strong></p>
      
      <% if @blueprint[:current_research_level] < @blueprint[:research_level_required] %>
        <p class="warning">You must reach research level <%= @blueprint[:research_level_required] %> before manufacturing</p>
      <% else %>
        <p class="success">Research requirement met ✓</p>
      <% end %>
    </div>
  </div>
</div>
```

### 3. Materials Integration

**Use ItemLookupService**:
```ruby
# In blueprints controller
def show
  @blueprint = MockDataHelper.blueprint_by_name(params[:name])
  
  # Enrich materials with lookup service data
  item_service = Lookup::ItemLookupService.new
  @blueprint[:materials].each do |material|
    item_data = item_service.find_item(material[:item_name])
    material[:description] = item_data[:description]
    material[:material_type] = item_data[:material_type]
  end
end
```

---

## File Modifications

### 1. Create: `app/controllers/blueprints_controller.rb`

```ruby
class BlueprintsController < ApplicationController
  def index
    @blueprints = MockDataHelper.blueprints(limit: 20)
    
    # Apply filters
    @blueprints = @blueprints.select { |b| 
      b[:technology_level] == params[:tech_level] 
    } if params[:tech_level].present?
    
    # Apply search
    if params[:search].present?
      @blueprints = @blueprints.select { |b| 
        b[:name].downcase.include?(params[:search].downcase) 
      }
    end
    
    # Sort
    sort = params[:sort] || "name"
    @blueprints = @blueprints.sort_by { |b| b[sort.to_sym] }
  end
  
  def show
    @blueprint = MockDataHelper.blueprint_by_name(params[:name])
    
    # Calculate total cost
    @blueprint[:total_cost] = @blueprint[:materials].sum { |m| m[:subtotal] }
  end
end
```

### 2. Create: `app/views/blueprints/index.html.erb`

(See code above)

### 3. Create: `app/views/blueprints/show.html.erb`

(See code above)

### 4. Create: `app/assets/stylesheets/blueprints.scss`

```scss
.blueprints-container {
  padding: 20px;
  background: var(--bg);
  min-height: 100vh;
}

.topbar-blueprints {
  margin-bottom: 30px;
  
  h1 {
    color: var(--text);
    margin: 0 0 20px;
    font-size: 28px;
  }
  
  .search-filter {
    display: flex;
    gap: 15px;
    flex-wrap: wrap;
    
    .search-input,
    .filter-select {
      padding: 8px 12px;
      background: var(--panel);
      border: 1px solid var(--line);
      color: var(--text);
      border-radius: 4px;
      
      &:focus {
        outline: none;
        border-color: var(--accent);
      }
    }
    
    .search-input {
      flex: 1;
      min-width: 200px;
    }
  }
}

.blueprints-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  
  .blueprint-card {
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 4px;
    overflow: hidden;
    transition: transform 0.2s, box-shadow 0.2s;
    cursor: pointer;
    
    &:hover {
      transform: translateY(-5px);
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    }
    
    .card-image {
      width: 100%;
      height: 150px;
      background: var(--panel2);
      display: flex;
      align-items: center;
      justify-content: center;
      border-bottom: 1px solid var(--line);
      
      img {
        max-width: 100%;
        max-height: 100%;
      }
      
      .placeholder {
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        
        .tech-level {
          padding: 10px 20px;
          border-radius: 4px;
          font-weight: bold;
          font-size: 14px;
          
          &.basic {
            background: rgba(0, 255, 100, 0.2);
            color: var(--green);
          }
          
          &.intermediate {
            background: rgba(255, 165, 0, 0.2);
            color: #ffa500;
          }
          
          &.advanced {
            background: rgba(100, 100, 255, 0.2);
            color: #6464ff;
          }
        }
      }
    }
    
    .card-body {
      padding: 15px;
      
      h3 {
        margin: 0 0 8px;
        color: var(--text);
        font-size: 16px;
      }
      
      .card-description {
        margin: 0 0 12px;
        color: var(--muted);
        font-size: 13px;
        line-height: 1.4;
      }
      
      .card-stats {
        display: flex;
        gap: 15px;
        margin-bottom: 12px;
        padding-bottom: 12px;
        border-bottom: 1px solid var(--line);
        
        .stat {
          font-size: 12px;
          color: var(--muted);
          
          strong {
            color: var(--accent);
          }
        }
      }
      
      .card-footer {
        display: flex;
        justify-content: space-between;
        align-items: center;
        
        .research-req {
          font-size: 11px;
          color: var(--muted);
        }
        
        .btn {
          padding: 6px 12px;
        }
      }
    }
  }
}

// Detail view
.blueprint-detail {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  background: var(--bg);
  min-height: 100vh;
}

.detail-header {
  display: grid;
  grid-template-columns: 300px 1fr;
  gap: 40px;
  margin-bottom: 40px;
  padding-bottom: 40px;
  border-bottom: 1px solid var(--line);
  
  .detail-image {
    width: 300px;
    height: 300px;
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    
    img {
      max-width: 100%;
      max-height: 100%;
    }
    
    .placeholder.large {
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }
  
  .detail-info {
    h1 {
      margin: 0 0 12px;
      color: var(--text);
      font-size: 32px;
    }
    
    .detail-description {
      margin: 0 0 20px;
      color: var(--muted);
      font-size: 16px;
      line-height: 1.6;
    }
    
    .detail-stats {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 20px;
      margin-bottom: 20px;
      
      .stat-box {
        padding: 15px;
        background: var(--panel);
        border: 1px solid var(--line);
        border-radius: 4px;
        
        .label {
          display: block;
          color: var(--muted);
          font-size: 12px;
          margin-bottom: 8px;
        }
        
        .value {
          display: block;
          color: var(--accent);
          font-size: 18px;
          font-weight: bold;
        }
      }
    }
    
    .licensed-runs {
      padding: 15px;
      background: rgba(0, 255, 100, 0.1);
      border-left: 3px solid var(--green);
      margin-bottom: 20px;
      
      p {
        margin: 0;
        color: var(--text);
        font-size: 14px;
      }
    }
  }
}

.materials-section {
  background: var(--panel);
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 4px;
  margin-bottom: 30px;
  
  h2 {
    color: var(--text);
    margin: 0 0 20px;
    font-size: 20px;
  }
  
  .materials-table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 15px;
    
    thead {
      border-bottom: 2px solid var(--line);
      
      th {
        padding: 12px;
        text-align: left;
        color: var(--muted);
        font-size: 12px;
        font-weight: bold;
      }
    }
    
    tbody {
      tr:hover {
        background: var(--panel2);
      }
      
      td {
        padding: 12px;
        border-bottom: 1px solid var(--line);
        color: var(--text);
        font-size: 14px;
        
        .item-link a {
          color: var(--accent);
          text-decoration: none;
          
          &:hover {
            text-decoration: underline;
          }
        }
        
        .stock-indicator {
          padding: 4px 8px;
          border-radius: 3px;
          font-weight: bold;
          
          &.sufficient {
            background: rgba(0, 255, 0, 0.2);
            color: var(--green);
          }
          
          &.insufficient {
            background: rgba(255, 0, 0, 0.2);
            color: var(--red);
          }
        }
      }
    }
  }
  
  .materials-summary {
    text-align: right;
    padding-top: 15px;
    border-top: 1px solid var(--line);
    color: var(--text);
    
    p {
      margin: 0;
      font-size: 16px;
      
      strong {
        color: var(--accent);
      }
    }
  }
}

.research-section {
  background: var(--panel);
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 4px;
  
  h2 {
    color: var(--text);
    margin: 0 0 20px;
    font-size: 20px;
  }
  
  .research-box {
    padding: 15px;
    background: var(--panel2);
    border: 1px solid var(--line);
    border-radius: 4px;
    
    p {
      margin: 8px 0;
      color: var(--text);
      font-size: 14px;
      
      &.warning {
        color: #ffa500;
      }
      
      &.success {
        color: var(--green);
      }
    }
  }
}

// Responsive
@media (max-width: 768px) {
  .detail-header {
    grid-template-columns: 1fr;
    
    .detail-image {
      width: 100%;
      height: 250px;
    }
  }
  
  .detail-stats {
    grid-template-columns: 1fr !important;
  }
  
  .blueprints-grid {
    grid-template-columns: 1fr;
  }
}
```

### 5. Update: `config/routes.rb`

```ruby
resources :blueprints, only: [:index, :show]
```

---

## Acceptance Criteria

- ✅ Blueprint index page loads and displays grid of cards
- ✅ Search functionality works (filters by name)
- ✅ Tech level filter works
- ✅ Sort options work (name, tech level, research)
- ✅ Blueprint cards show placeholder when image missing
- ✅ Blueprint detail view shows all information
- ✅ Materials table displays correctly
- ✅ Research section shows requirements
- ✅ Licensed runs display (if applicable)
- ✅ Item links from materials connect to Items catalog (Task 5)
- ✅ Stock indicator shows whether player has materials
- ✅ Responsive layout on mobile
- ✅ EVE Dashboard styling applied

---

## References

- **MockDataHelper**: `blueprints()` and `blueprint_by_name()` methods
- **ItemLookupService**: For enriching material data
- **EVE CSS**: --bg, --panel, --panel2, --text, --muted, --accent, --green, --red, --line

---

**Status**: ✅ Ready for implementation  
**Assigned To**: Implementation Agent  
**Review Gate**: Verify all filtering/sorting works, materials display, responsive design
