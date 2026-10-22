# Task 2: Landing Page Redesign (Galaxy Game Themed, System Selector)

**Priority**: HIGH  
**Complexity**: Medium  
**Estimated Time**: 4-5 hours  
**Status**: Ready for Implementation  
**Date Created**: 2026-10-22  

---

## Overview

Replace the current landing page with a Galaxy Game branded experience:
1. Hero section with Galaxy Game branding
2. Three-system quick-start cards (Sol, Eden, System B)
3. System selector dropdown
4. Login/session creation flow
5. Organization logos for context

Currently at: `/views/landing/index.html.erb` or redirects to `/dashboard`

---

## Current State

- Generic landing page exists in main Galaxy Game
- galaxy-game-ui has basic session/login (broken CSS with WVU classes)
- No system selector or multi-system context
- No Galaxy Game branding or imagery

---

## Requirements

### 1. Hero Section

**Content**:
- Galaxy Game title/logo (use org logo from `/data/images/logos/`)
- Tagline: "Wormhole Transit Consortium Network" (or similar)
- Background: space/cosmos theme (use existing biome/terrain tiles as texture)
- Call-to-action button: "Enter Game" or "Start Your Journey"

**Styling**:
- Dark theme (EVE Dashboard colors: --bg, --text)
- Hero height: 60vh minimum
- Responsive on mobile (button stacks)

**Code**:
```erb
<section class="landing-hero">
  <div class="hero-content">
    <img class="hero-logo" src="/data/images/logos/Wormhole Transit Consortium.png" 
         alt="Wormhole Transit Consortium">
    <h1>Galaxy Game</h1>
    <p class="hero-tagline">Navigate the wormhole network. Expand your empire.</p>
    <%= link_to "Enter Game", new_session_path, class: "btn btn-primary btn-lg" %>
  </div>
</section>
```

### 2. Three-System Quick Start Cards

**Systems** (from MockDataHelper.wormhole_network):
- **Sol** (Home): "Your home system. Stable, resourced."
- **Eden** (Exploration): "A frontier outpost. Opportunity awaits."
- **System B** (Frontier): "Unknown territory. Risk and reward."

**Card Structure**:
```ruby
{
  system_name: "Sol",
  identifier: "SOL",
  description: "Your home system",
  stability: "stable",
  distance_from_sol: "home",
  biome_image: "/data/images/biomes/plains.png",
  quick_facts: ["Rocky planets", "Active settlements", "Safe wormholes"]
}
```

**Styling**:
- 3-column grid (responsive to 1 column mobile)
- Card height: 300px
- Biome image thumbnail (optional background)
- Icon showing stability (green=stable, orange=stabilizing, red=unstable)
- Hover effect (scale, shadow increase)

**Code**:
```erb
<section class="landing-systems">
  <h2>Known Systems</h2>
  <div class="systems-grid">
    <% @systems.each do |system| %>
      <div class="system-card">
        <div class="system-image">
          <%= image_tag system[:biome_image], alt: system[:system_name] %>
        </div>
        <h3><%= system[:system_name] %></h3>
        <p class="system-description"><%= system[:description] %></p>
        <ul class="system-facts">
          <% system[:quick_facts].each do |fact| %>
            <li><%= fact %></li>
          <% end %>
        </ul>
        <span class="stability-badge stability-<%= system[:stability] %>">
          <%= system[:stability].titleize %>
        </span>
      </div>
    <% end %>
  </div>
</section>
```

### 3. System Selector Dropdown

**Location**: Top-right corner (EVE Dashboard style topbar)

**Behavior**:
- Shows currently selected system ("Sol" by default)
- Dropdown shows all 3 systems + quick info
- Selection persists in session
- Updates wormhole view and dashboard data

**Code**:
```erb
<div class="topbar-system-selector">
  <%= form_with url: sessions_path, method: :post, local: true do |f| %>
    <%= f.hidden_field :system_id, value: @current_system_id %>
    <div class="selector-wrapper">
      <label>System:</label>
      <%= f.select :system, 
          options_for_select([
            ["Sol (Home)", "SOL"],
            ["Eden (Outpost)", "AOL-732356"],
            ["Alpha Centauri I", "AC-01"]
          ], @current_system_id),
          { class: "system-select" } %>
    </div>
  <% end %>
</div>
```

### 4. Login/Session Creation

**Existing** (in `sessions/new.html.erb`):
- Form with name, email, password fields
- Currently broken CSS (WVU classes)

**Update**:
- Use EVE Dashboard button styles (btn, btn-primary)
- Integrate into landing page flow
- Optional: Show login as modal or dedicated page

**Code**:
```erb
<section class="landing-login">
  <div class="login-container">
    <h2>Establish Connection</h2>
    <p>Enter your credentials to access the network</p>
    
    <%= form_with url: sessions_path, local: true do |f| %>
      <div class="form-group">
        <%= f.label :name %>
        <%= f.text_field :name, class: "form-input" %>
      </div>
      
      <div class="form-group">
        <%= f.label :email %>
        <%= f.email_field :email, class: "form-input" %>
      </div>
      
      <div class="form-group">
        <%= f.label :password %>
        <%= f.password_field :password, class: "form-input" %>
      </div>
      
      <%= f.submit "Login", class: "btn btn-primary btn-lg" %>
    <% end %>
  </div>
</section>
```

### 5. Asset Integration

**Images Used**:
- Org logo: `/data/images/logos/Wormhole Transit Consortium.png`
- System biomes: `/data/images/biomes/` (plains, forest, desert, etc.)
- Optional: Terrain tiles as background texture

**Fallback**:
- If images missing, use solid color blocks with text
- Template placeholders for future asset generation

---

## File Modifications

### 1. Create/Update: `app/views/landing/index.html.erb`

Replace generic content with Galaxy Game themed landing page (see code above).

### 2. Update: `app/controllers/landing_controller.rb` (or create)

```ruby
class LandingController < ApplicationController
  skip_before_action :authenticate_user!, only: [:index]

  def index
    @systems = MockDataHelper.wormhole_network[:systems]
    @current_system_id = session[:current_system_id] || "SOL"
  end
end
```

### 3. Update: `config/routes.rb`

```ruby
# Add landing page route
root "landing#index"
get "landing", to: "landing#index"

# Sessions route (existing, ensure it sets system_id)
post "sessions", to: "sessions#create"
```

### 4. Update: `app/controllers/sessions_controller.rb`

```ruby
def create
  # ... existing session creation logic ...
  
  # Set default system
  session[:current_system_id] = params[:system_id] || "SOL"
  
  redirect_to dashboard_path
end
```

### 5. Create: `app/assets/stylesheets/landing.scss`

```scss
// Landing page styles (EVE Dashboard theme)
.landing-hero {
  height: 60vh;
  background: linear-gradient(135deg, var(--bg), #1a1a2e);
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 40px;
  border-bottom: 2px solid var(--line);
  
  .hero-content {
    max-width: 600px;
    
    .hero-logo {
      max-width: 150px;
      height: auto;
      margin-bottom: 30px;
    }
    
    h1 {
      font-size: 48px;
      color: var(--text);
      margin: 20px 0;
      text-shadow: 0 0 20px rgba(0, 255, 100, 0.3);
    }
    
    .hero-tagline {
      font-size: 18px;
      color: var(--muted);
      margin-bottom: 30px;
    }
    
    .btn-lg {
      padding: 15px 40px;
      font-size: 16px;
    }
  }
}

.landing-systems {
  padding: 60px 40px;
  background: var(--bg);
  
  h2 {
    font-size: 32px;
    color: var(--text);
    margin-bottom: 40px;
    text-align: center;
  }
  
  .systems-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 30px;
    max-width: 1200px;
    margin: 0 auto;
  }
  
  .system-card {
    background: var(--panel);
    border: 1px solid var(--line);
    padding: 20px;
    border-radius: 4px;
    transition: transform 0.2s, box-shadow 0.2s;
    cursor: pointer;
    
    &:hover {
      transform: translateY(-5px);
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    }
    
    .system-image {
      width: 100%;
      height: 150px;
      overflow: hidden;
      border-radius: 4px;
      margin-bottom: 15px;
      background: var(--panel2);
      
      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }
    }
    
    h3 {
      color: var(--accent);
      margin: 10px 0;
      font-size: 20px;
    }
    
    .system-description {
      color: var(--text);
      font-size: 14px;
      margin-bottom: 10px;
    }
    
    .system-facts {
      list-style: none;
      padding: 10px 0;
      margin: 10px 0;
      border-top: 1px solid var(--line);
      border-bottom: 1px solid var(--line);
      
      li {
        color: var(--muted);
        font-size: 12px;
        padding: 3px 0;
        
        &:before {
          content: "→ ";
          color: var(--green);
        }
      }
    }
    
    .stability-badge {
      display: inline-block;
      padding: 5px 10px;
      border-radius: 3px;
      font-size: 12px;
      font-weight: bold;
      margin-top: 10px;
      
      &.stability-stable {
        background: rgba(0, 255, 0, 0.2);
        color: var(--green);
      }
      
      &.stability-stabilizing {
        background: rgba(255, 165, 0, 0.2);
        color: #ffa500;
      }
      
      &.stability-unstable {
        background: rgba(255, 0, 0, 0.2);
        color: var(--red);
      }
    }
  }
}

.landing-login {
  padding: 60px 40px;
  background: var(--panel);
  border-top: 2px solid var(--line);
  
  .login-container {
    max-width: 400px;
    margin: 0 auto;
    background: var(--bg);
    padding: 40px;
    border: 1px solid var(--line);
    border-radius: 4px;
    
    h2 {
      color: var(--text);
      margin-bottom: 10px;
    }
    
    > p {
      color: var(--muted);
      margin-bottom: 30px;
    }
    
    .form-group {
      margin-bottom: 20px;
      
      label {
        display: block;
        color: var(--text);
        margin-bottom: 8px;
        font-size: 14px;
      }
      
      input {
        width: 100%;
        padding: 10px;
        background: var(--panel);
        border: 1px solid var(--line);
        color: var(--text);
        border-radius: 4px;
        
        &:focus {
          outline: none;
          border-color: var(--accent);
        }
      }
    }
    
    .btn-lg {
      width: 100%;
      margin-top: 20px;
    }
  }
}

.topbar-system-selector {
  display: flex;
  align-items: center;
  gap: 10px;
  
  label {
    color: var(--text);
    font-size: 12px;
  }
  
  .system-select {
    padding: 5px 10px;
    background: var(--panel);
    color: var(--text);
    border: 1px solid var(--line);
    border-radius: 3px;
    font-size: 14px;
    cursor: pointer;
  }
}

// Responsive
@media (max-width: 768px) {
  .landing-hero {
    height: 50vh;
    
    .hero-content h1 {
      font-size: 32px;
    }
    
    .hero-tagline {
      font-size: 16px;
    }
  }
  
  .landing-systems .systems-grid {
    grid-template-columns: 1fr;
  }
  
  .landing-login .login-container {
    padding: 20px;
  }
}
```

---

## Acceptance Criteria

- ✅ Landing page loads at root path (`/`)
- ✅ Hero section displays Galaxy Game branding (org logo)
- ✅ Three system cards render with biome images (or fallback placeholders)
- ✅ Stability badges show correct colors (green/orange/red)
- ✅ System selector dropdown functional and persists selection
- ✅ Login form works and creates session
- ✅ Page responsive on mobile (1-column layout)
- ✅ EVE Dashboard CSS colors/styling applied consistently
- ✅ No WVU CSS classes (all replaced with EVE style)
- ✅ After login, redirects to dashboard with selected system set
- ✅ Missing images use placeholder UI (not broken img tags)

---

## References

- **Asset paths**: `/data/images/logos/`, `/data/images/biomes/`
- **EVE CSS variables**: `--bg`, `--panel`, `--text`, `--accent`, `--green`, `--red`, `--line`, `--muted`
- **MockDataHelper**: `wormhole_network()` method returns systems and stability info
- **Main landing page reference**: `galaxyGame/galaxy_game/app/views/landing/index.html.erb`

---

## Next Steps

After completion:
- Task 3: Dashboard will build on authenticated session from this landing page
- System selector will control which system's data displays in dashboard
- Wormhole Navigator will show connections from selected system

---

**Status**: ✅ Ready for implementation  
**Assigned To**: Implementation Agent  
**Review Gate**: Verify responsive design, asset loading, login flow works end-to-end
