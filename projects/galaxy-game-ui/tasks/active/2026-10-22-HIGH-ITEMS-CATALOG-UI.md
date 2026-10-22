# Task 5: Items Catalog UI (Searchable, Filtered by Type, Material Properties)

**Priority**: HIGH | **Complexity**: Medium | **Time**: 4-5 hours | **Status**: Ready

---

## Overview
Searchable items catalog with filtering by material type, descriptions, material properties, and asset integration. Shows where items come from and which blueprints use them.

---

## Requirements

### 1. Items Index
- Grid of item cards (searchable, filterable by material_type)
- 9 material type filters: manufactured_goods, metal, component, processed_material, gas, consumable, liquid, fuel, raw_material
- Sort: name, rarity, weight
- Card displays: name, icon (if available, else placeholder), material_type badge, rarity

### 2. Item Detail Page
- Full item information: description, material_type, source, properties
- Which blueprints use this item
- Where to find/harvest it
- Average market price
- Asset image (or placeholder)
- "Used in X blueprints" links

### 3. Asset Integration
- Use asset_map from MockDataHelper
- Fallback placeholders when image missing
- Track "missing assets" for generation priority

---

## File Modifications

### `app/controllers/items_controller.rb`
```ruby
class ItemsController < ApplicationController
  def index
    @items = MockDataHelper.items(limit: 50)
    
    # Filter by material type
    if params[:type].present?
      @items = @items.select { |i| i[:material_type] == params[:type] }
    end
    
    # Search
    if params[:search].present?
      @items = @items.select { |i| 
        i[:name].downcase.include?(params[:search].downcase)
      }
    end
    
    # Sort
    sort = params[:sort] || "name"
    @items = @items.sort_by { |i| i[sort.to_sym] }
  end
  
  def show
    @item = MockDataHelper.item_by_name(params[:name])
    @item_service = Lookup::ItemLookupService.new
    @blueprints_using_item = find_blueprints_using_item(@item[:name])
  end
  
  private
  
  def find_blueprints_using_item(item_name)
    MockDataHelper.blueprints.select { |b|
      b[:materials].any? { |m| m[:item_name] == item_name }
    }
  end
end
```

### `app/views/items/index.html.erb`
```erb
<div class="items-container">
  <div class="topbar-items">
    <h1>Item Catalog</h1>
    
    <div class="search-filter">
      <%= form_with url: items_path, method: :get, local: true do |f| %>
        <%= f.text_field :search, placeholder: "Search items...", class: "search-input" %>
        
        <%= f.select :type, 
            options_for_select([
              ["All Types", ""],
              ["Manufactured Goods", "manufactured_goods"],
              ["Metals", "metal"],
              ["Components", "component"],
              ["Processed Materials", "processed_material"],
              ["Gases", "gas"],
              ["Consumables", "consumable"],
              ["Liquids", "liquid"],
              ["Fuels", "fuel"],
              ["Raw Materials", "raw_material"]
            ], params[:type]),
            { class: "filter-select" } %>
        
        <%= f.submit "Filter", class: "btn sm" %>
      <% end %>
    </div>
  </div>
  
  <div class="items-grid">
    <% @items.each do |item| %>
      <div class="item-card">
        <div class="card-image">
          <% if item[:image_path] %>
            <%= image_tag item[:image_path] %>
          <% else %>
            <div class="placeholder"><%= item[:material_type].capitalize %></div>
          <% end %>
        </div>
        
        <div class="card-body">
          <h3><%= link_to item[:name], item_path(item[:name]) %></h3>
          <p class="card-description"><%= truncate(item[:description], length: 80) %></p>
          
          <div class="card-badges">
            <span class="badge type"><%= item[:material_type].titleize %></span>
            <span class="badge rarity <%= item[:rarity] %>"><%= item[:rarity].titleize %></span>
          </div>
        </div>
      </div>
    <% end %>
  </div>
</div>
```

### `app/views/items/show.html.erb`
```erb
<div class="item-detail">
  <div class="detail-header">
    <div class="detail-image">
      <% if @item[:image_path] %>
        <%= image_tag @item[:image_path] %>
      <% else %>
        <div class="placeholder large"><%= @item[:material_type].capitalize %></div>
      <% end %>
    </div>
    
    <div class="detail-info">
      <h1><%= @item[:name] %></h1>
      <p class="detail-description"><%= @item[:description] %></p>
      
      <div class="detail-badges">
        <span class="badge"><%= @item[:material_type].titleize %></span>
        <span class="badge rarity <%= @item[:rarity] %>"><%= @item[:rarity].titleize %></span>
        <span class="badge"><strong><%= @item[:unit_weight] %></strong> kg</span>
      </div>
      
      <div class="detail-properties">
        <h3>Properties</h3>
        <dl>
          <dt>Source:</dt>
          <dd><%= @item[:source] %></dd>
          <dt>Unit Weight:</dt>
          <dd><%= @item[:unit_weight] %> kg</dd>
          <dt>Durability:</dt>
          <dd><%= @item[:durability] %></dd>
        </dl>
      </div>
    </div>
  </div>
  
  <div class="blueprints-using">
    <h2>Used In Blueprints</h2>
    <% if @blueprints_using_item.any? %>
      <ul class="blueprint-links">
        <% @blueprints_using_item.each do |bp| %>
          <li><%= link_to bp[:name], blueprint_path(bp[:id]) %></li>
        <% end %>
      </ul>
    <% else %>
      <p>This item is not used in any known blueprints</p>
    <% end %>
  </div>
</div>
```

### Create: `app/assets/stylesheets/items.scss`
```scss
.items-container {
  padding: 20px;
  background: var(--bg);
  min-height: 100vh;
}

.topbar-items {
  margin-bottom: 30px;
  
  h1 {
    color: var(--text);
    margin: 0 0 20px;
  }
  
  .search-filter {
    display: flex;
    gap: 15px;
    flex-wrap: wrap;
    
    input, select {
      padding: 8px 12px;
      background: var(--panel);
      border: 1px solid var(--line);
      color: var(--text);
      border-radius: 4px;
    }
    
    .search-input {
      flex: 1;
      min-width: 200px;
    }
  }
}

.items-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
  
  .item-card {
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 4px;
    overflow: hidden;
    transition: transform 0.2s, box-shadow 0.2s;
    
    &:hover {
      transform: translateY(-5px);
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    }
    
    .card-image {
      width: 100%;
      height: 120px;
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
        color: var(--muted);
        font-size: 12px;
      }
    }
    
    .card-body {
      padding: 12px;
      
      h3 {
        margin: 0 0 6px;
        color: var(--text);
        
        a {
          color: var(--accent);
          text-decoration: none;
          
          &:hover {
            text-decoration: underline;
          }
        }
      }
      
      .card-description {
        margin: 0 0 10px;
        color: var(--muted);
        font-size: 12px;
      }
      
      .card-badges {
        display: flex;
        gap: 6px;
        flex-wrap: wrap;
        
        .badge {
          padding: 3px 8px;
          background: var(--panel2);
          border: 1px solid var(--line);
          border-radius: 3px;
          font-size: 11px;
          color: var(--text);
          
          &.rarity {
            &.common {
              border-color: var(--green);
              color: var(--green);
            }
            &.uncommon {
              border-color: #ffa500;
              color: #ffa500;
            }
            &.rare {
              border-color: #6464ff;
              color: #6464ff;
            }
          }
        }
      }
    }
  }
}

.item-detail {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
}

.detail-header {
  display: grid;
  grid-template-columns: 250px 1fr;
  gap: 40px;
  margin-bottom: 40px;
  padding-bottom: 40px;
  border-bottom: 1px solid var(--line);
  
  .detail-image {
    width: 250px;
    height: 250px;
    background: var(--panel);
    border: 1px solid var(--line);
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .detail-info {
    h1 {
      margin: 0 0 12px;
      color: var(--text);
    }
    
    .detail-properties {
      margin-top: 20px;
      padding: 15px;
      background: var(--panel);
      border: 1px solid var(--line);
      border-radius: 4px;
      
      dl {
        margin: 0;
        
        dt {
          color: var(--muted);
          font-weight: bold;
          margin-top: 8px;
        }
        
        dd {
          margin: 3px 0 0 20px;
          color: var(--text);
        }
      }
    }
  }
}

.blueprints-using {
  background: var(--panel);
  padding: 20px;
  border: 1px solid var(--line);
  border-radius: 4px;
  
  h2 {
    margin: 0 0 15px;
    color: var(--text);
  }
  
  .blueprint-links {
    list-style: none;
    padding: 0;
    margin: 0;
    
    li {
      padding: 8px 0;
      border-bottom: 1px solid var(--line);
      
      &:last-child {
        border-bottom: none;
      }
      
      a {
        color: var(--accent);
        text-decoration: none;
        
        &:hover {
          text-decoration: underline;
        }
      }
    }
  }
}

@media (max-width: 768px) {
  .detail-header {
    grid-template-columns: 1fr;
    
    .detail-image {
      width: 100%;
      height: 200px;
    }
  }
}
```

---

## Acceptance Criteria

- ✅ Items index displays grid with all 9 material types
- ✅ Search filters by name
- ✅ Material type filter works
- ✅ Item cards show placeholder when image missing
- ✅ Item detail shows all properties
- ✅ Links to blueprints using item work
- ✅ Missing assets identified for generation
- ✅ Responsive design
- ✅ EVE Dashboard styling applied

---

**Status**: ✅ Ready for implementation | **Review Gate**: All filtering works, links functional
