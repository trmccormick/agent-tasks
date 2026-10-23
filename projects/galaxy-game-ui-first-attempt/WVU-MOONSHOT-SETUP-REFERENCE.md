# WVU Moonshot Rails 8 Setup Reference for Galaxy Game UI

**Purpose**: Reference guide showing which parts of the existing WVU Moonshot Rails 8 prototype can be adapted for the Galaxy Game UI project. This helps Qwen leverage existing, tested patterns for Docker, Gemfile, configuration, and controller/view structure.

**Location**: `/Users/tam0013/Documents/git/wvu-moonshot/`

---

## Overview: What to Adapt vs. What to Skip

| Component | Status | Notes |
|---|---|---|
| Docker setup (Dockerfile, docker-compose) | ✅ **ADAPT** | Reusable for any Rails 8 app, just remove DB service |
| Gemfile structure | ✅ **ADAPT** | Same Rails 8 gems needed (without pg, bcrypt) |
| Routes pattern | ✅ **ADAPT** | Simple RESTful pattern suitable for galaxy-game-ui |
| Tailwind CSS setup | ✅ **ADAPT** | Uses `@theme` for custom colors; same pattern to use |
| Stimulus controller pattern | ✅ **ADAPT** | Graduate filter controller = perfect pattern for admin panel |
| Layout template structure | ✅ **ADAPT** | Header/main/footer pattern works for galaxy-game-ui |
| Procfile.dev | ✅ **ADAPT** | Simple `web:` and `css:` processes |
| Environment file setup | ⚠️ **SIMPLIFY** | Skip DB connection vars; galaxy-game-ui has no database |
| Database schema, migrations | ❌ **SKIP** | galaxy-game-ui is frontend-only; no models |
| Authentication (sessions, passwords) | ❌ **SKIP** | Not needed for prototype |
| RSpec tests | ⚠️ **SKIP** (for now) | Galaxy-game-ui focuses on UI; add tests later if needed |

---

## Docker Setup (REUSABLE)

### Files to Adapt
- **Dockerfile**: Base Docker image, Ruby dependencies, bundle setup
- **docker-compose.yml**: Production orchestration pattern
- **docker-compose.dev.yml**: Development environment setup
- **up.sh** / **down.sh**: Simple scripts to start/stop containers

### Key Adaptations for Galaxy Game UI

**Remove the `db` service** from docker-compose files (no PostgreSQL needed):
```yaml
# DO NOT INCLUDE THIS for galaxy-game-ui:
# db:
#   container_name: db
#   image: postgres:16-alpine
#   ...
```

**Simplify environment files** (no database credentials):
```bash
# env/env.development (simplified for galaxy-game-ui)
# No DATABASE_* vars needed
RAILS_ENV=development
```

**Keep the volume mappings**:
```yaml
volumes:
  - ./galaxy-game-ui:/home/game              # Source code
  - ./data/node_modules:/home/game/node_modules  # Yarn cache
  - ./data/bundle:/usr/local/bundle          # Gem cache
```

**Startup script pattern** (adapt for galaxy-game-ui):
```bash
#!/bin/bash
# See: /Users/tam0013/Documents/git/wvu-moonshot/scripts/startup.sh

# Install yarn packages
yarn install --check-files

# Remove stale PID
rm -f /home/game/tmp/pids/server.pid

# Start the server on all interfaces (important for Docker)
bin/rails s -p 3000 -b '0.0.0.0'
```

---

## Gemfile Structure (REUSABLE, SIMPLIFIED)

### Current WVU Moonshot Gemfile
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/Gemfile`

### Key Gems to Keep for Galaxy Game UI
```ruby
source "https://rubygems.org"

gem "rails", "~> 8.1.3", ">= 8.1.3.1"
gem "propshaft"                    # Asset pipeline
gem "puma", ">= 5.0"               # Web server
gem "importmap-rails"              # JS importing
gem "turbo-rails"                  # Hotwire Turbo
gem "stimulus-rails"               # Hotwire Stimulus
gem "tailwindcss-rails", "~> 4.6"  # Tailwind CSS
```

### Gems to REMOVE for Galaxy Game UI
```ruby
# Database-related (NOT NEEDED for frontend-only prototype)
# gem "pg", "~> 1.1"
# gem "bcrypt", "~> 3.1.7"
# gem "image_processing", "~> 1.2"

# Authentication (NOT NEEDED for prototype)
# (sessions, passwords gems)

# Testing (SKIP FOR NOW; add later)
# gem "rspec-rails"
# gem "factory_bot_rails"
# gem "shoulda-matchers"
```

### Simplified Galaxy Game UI Gemfile
```ruby
source "https://rubygems.org"

gem "rails", "~> 8.1.3"
gem "propshaft"
gem "puma", ">= 5.0"
gem "importmap-rails"
gem "turbo-rails"
gem "stimulus-rails"
gem "tailwindcss-rails", "~> 4.6"

group :development do
  gem "web-console"
end
```

---

## Routes Pattern (REUSABLE)

### WVU Moonshot Routes
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/config/routes.rb`

```ruby
Rails.application.routes.draw do
  resource :session
  resources :passwords, param: :token
  resources :graduate_records, only: %i[ index show new create update ]

  get "up" => "rails/health#show", as: :rails_health_check

  root "graduate_records#index"
end
```

### Galaxy Game UI Routes (Adapted)
```ruby
Rails.application.routes.draw do
  get "up" => "rails/health#show", as: :rails_health_check

  root "dashboard#index"
  
  get "dashboard", to: "dashboard#index"
  get "wormholes", to: "wormholes#index"
  get "admin", to: "admin#index"
end
```

---

## Tailwind CSS Setup (REUSABLE PATTERN)

### WVU Moonshot Approach
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/app/assets/tailwind/application.css`

Uses `@theme` block to define custom colors as CSS variables:
```css
@import "tailwindcss";

@theme {
  --color-wvu-blue: #002855;
  --color-wvu-gold: #eaaa00;
  --color-wvu-warm-gray-light: #beb7b3;
}

.woodburn-hall {
  background-color: var(--color-wvu-off-white);
  background-image:
    radial-gradient(ellipse at top left, rgba(234, 170, 0, 0.05), transparent 55%),
    radial-gradient(ellipse at bottom right, rgba(0, 40, 85, 0.04), transparent 55%);
}
```

### Galaxy Game UI Tailwind Setup (Using Same Pattern)

Location: `app/assets/tailwind/application.css`

```css
@import "tailwindcss";

/* EVE Dashboard color palette for Galaxy Game UI */
@theme {
  --color-eve-dark: #0e1114;              /* Main background */
  --color-eve-panel: #161b21;             /* Panel background */
  --color-eve-panel2: #1d242c;            /* Alt panel */
  --color-eve-line: #2a333d;              /* Borders */
  --color-eve-text: #d8dee6;              /* Main text */
  --color-eve-muted: #8a97a6;             /* Muted text */
  --color-eve-accent: #4aa8ff;            /* Bright cyan */
  --color-eve-green: #3fb950;             /* Success */
  --color-eve-red: #f85149;               /* Danger */
  --color-eve-gold: #e3b341;              /* Warning */
  --color-neon-cyan: #00d9ff;             /* Bright cyan */
  --color-neon-purple: #b366ff;           /* Purple accent */
  --color-neon-gold: #ffd700;             /* Gold accent */
}

/* Glassmorphic background with sci-fi aesthetic */
.space-background {
  background-color: var(--color-eve-dark);
  background-image:
    radial-gradient(ellipse at top left, rgba(74, 168, 255, 0.1), transparent 55%),
    radial-gradient(ellipse at bottom right, rgba(179, 102, 255, 0.08), transparent 55%);
}

/* Reusable glass card component */
.glass-card {
  background: rgba(22, 27, 33, 0.8);
  border: 1px solid rgba(74, 168, 255, 0.2);
  border-radius: 0.75rem;
  padding: 1.5rem;
  backdrop-filter: blur(10px);
}
```

### Layout Template Using Tailwind
Location: `app/views/layouts/application.html.erb`

```erb
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Galaxy Game UI</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag :app, "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>
  </head>

  <body class="min-h-screen flex flex-col bg-eve-dark text-eve-text space-background">
    <!-- Navigation header -->
    <header class="bg-eve-panel border-b border-eve-line">
      <div class="max-w-7xl mx-auto px-4 py-4 flex items-center justify-between gap-4">
        <h1 class="text-2xl font-bold text-eve-accent">⚛ Galaxy Game</h1>
        <nav class="flex gap-6">
          <%= link_to "Dashboard", dashboard_path, class: "hover:text-eve-accent transition" %>
          <%= link_to "Wormholes", wormholes_path, class: "hover:text-eve-accent transition" %>
          <%= link_to "Admin", admin_path, class: "hover:text-eve-accent transition" %>
        </nav>
      </div>
    </header>

    <!-- Main content -->
    <main class="flex-1">
      <div class="max-w-7xl mx-auto px-4 py-8">
        <%= yield %>
      </div>
    </main>

    <!-- Footer -->
    <footer class="bg-eve-panel border-t border-eve-line py-6 text-center text-eve-muted">
      <p>Galaxy Game UI Prototype</p>
    </footer>
  </body>
</html>
```

---

## Stimulus Controller Pattern (REUSABLE)

### WVU Moonshot Example
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/app/javascript/controllers/graduate_filter_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["input", "item"]

  filter() {
    const query = this.inputTarget.value.toLowerCase()

    this.itemTargets.forEach(item => {
      const text = item.textContent.toLowerCase()
      if (text.includes(query)) {
        item.classList.remove("hidden")
      } else {
        item.classList.add("hidden")
      }
    })
  }
}
```

### Galaxy Game UI Admin Controller (Adapted Pattern)

This is the pattern Qwen should use for the Admin Telemetry Panel (Task 4).

File: `app/javascript/controllers/admin_controller.js`

```javascript
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["toast", "eventLog"]

  forceRebalanceTick() {
    this.showToast("✅ Force Rebalance Tick initiated", "success")
    this.appendEventLogEntry("Force Rebalance Tick triggered by admin")
  }

  triggerWormholeEvent() {
    this.showToast("⚡ Natural Wormhole Event spawned", "info")
    this.appendEventLogEntry("Natural wormhole decay event triggered")
  }

  showToast(message, type = "info") {
    const toast = document.createElement("div")
    toast.className = `toast toast-${type}`
    toast.textContent = message
    toast.style.cssText = `
      position: fixed;
      top: 1rem;
      right: 1rem;
      padding: 1rem;
      background: ${type === "success" ? "#3fb950" : "#4aa8ff"};
      color: #0e1114;
      border-radius: 0.5rem;
      z-index: 1000;
    `
    document.body.appendChild(toast)
    
    setTimeout(() => toast.remove(), 3000)
  }

  appendEventLogEntry(message) {
    const timestamp = new Date().toLocaleTimeString()
    const entry = `<div class="text-sm text-eve-muted">[${timestamp}] ${message}</div>`
    this.eventLogTarget.insertAdjacentHTML("afterbegin", entry)
    
    // Keep only last 20 entries
    const entries = this.eventLogTarget.querySelectorAll("div")
    if (entries.length > 20) {
      entries[entries.length - 1].remove()
    }
  }
}
```

### View Usage (in admin/index.html.erb)

```erb
<div data-controller="admin">
  <button data-action="admin#forceRebalanceTick" class="btn btn-primary">
    Force Rebalance Tick
  </button>
  
  <button data-action="admin#triggerWormholeEvent" class="btn btn-secondary">
    Trigger Wormhole Event
  </button>
  
  <div data-admin-target="eventLog" class="event-log">
    <!-- Event entries appended here -->
  </div>
</div>
```

---

## Procfile.dev Pattern (REUSABLE)

### WVU Moonshot
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/Procfile.dev`

```
web: bin/rails server
css: bin/rails tailwindcss:watch
```

### Galaxy Game UI (Same Pattern)

File: `Procfile.dev`

```
web: bin/rails server
css: bin/rails tailwindcss:watch
```

**Run locally** (not in Docker):
```bash
cd /Users/tam0013/Documents/git/galaxy-game-ui
gem install foreman  # if not already installed
foreman start        # Runs both web and css processes
```

---

## Environment Configuration (SIMPLIFIED for Galaxy Game UI)

### WVU Moonshot Structure
```
env/
├── env                 # Base template
├── env.development     # Dev environment
├── env.production      # Prod environment
├── env.db              # Database connection
└── env.local.example   # Example for local overrides
```

### Galaxy Game UI Simplification

Since galaxy-game-ui has **no database**, simplify to:

```bash
# env/env.development
RAILS_ENV=development
RAILS_LOG_TO_STDOUT=true
```

```bash
# env/env.production
RAILS_ENV=production
RAILS_LOG_TO_STDOUT=true
```

**No need for**:
- DATABASE_USERNAME, DATABASE_PASSWORD, DATABASE_HOST, DATABASE_PORT
- Session storage configuration (uses default memory store)
- Cache configuration (uses default memory store)

---

## Controller Structure Pattern (REUSABLE)

### WVU Moonshot Controller
Location: `/Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot/app/controllers/graduate_records_controller.rb`

```ruby
class GraduateRecordsController < ApplicationController
  def index
    # Render view with mocked/database data
    # Uses Stimulus controller for client-side filtering
  end

  def show
    # Show detail view with related data
  end
end
```

### Galaxy Game UI Controller Pattern

File: `app/controllers/dashboard_controller.rb`

```ruby
class DashboardController < ApplicationController
  def index
    # All data comes from MockDataHelper (in-memory mocking)
    # No database queries
  end
end
```

File: `app/helpers/mock_data.rb` (see Task 1 for full implementation)

```ruby
module MockDataHelper
  def self.system_status
    { name: "Alpha Station", security_level: "high", eap: 250.00, balance_isk: 5_000_000, balance_gcc: 1500 }
  end

  def self.active_units
    # Array of 3-5 mocked PVE units with status badges
  end
  
  # ... other factory methods
end
```

---

## File Structure Reference

### WVU Moonshot Directory Layout
```
wvu-moonshot/
├── Dockerfile               # Production Docker image
├── Dockerfile.dev           # Development Docker image
├── docker-compose.yml       # Production orchestration
├── docker-compose.dev.yml   # Development orchestration
├── up.sh / down.sh          # Container control scripts
├── env/                     # Environment files
├── scripts/                 # Bash scripts (startup.sh, etc.)
├── data/                    # Persistent volumes (node_modules, bundle, postgres)
└── wvu-moonshot/            # Rails app root
    ├── Gemfile              # Ruby dependencies
    ├── Procfile.dev         # Development processes
    ├── app/
    │   ├── controllers/     # Rails controllers
    │   ├── views/           # ERB templates
    │   ├── helpers/         # Ruby helpers (MockDataHelper pattern)
    │   ├── javascript/      # Stimulus controllers
    │   │   └── controllers/ # One file per controller
    │   └── assets/
    │       ├── tailwind/    # Custom Tailwind CSS (@theme)
    │       └── stylesheets/ # Application-wide CSS
    ├── config/
    │   ├── routes.rb        # URL routing
    │   ├── tailwind.config.js # Tailwind configuration (if using CLI)
    │   └── environments/    # Rails environment configs
    └── ...
```

### Galaxy Game UI Directory Layout (Following Same Pattern)
```
galaxy-game-ui/
├── Dockerfile               # Reuse from wvu-moonshot
├── docker-compose.yml       # Adapted (no db service)
├── up.sh / down.sh          # Simple start/stop scripts
├── env/                     # Simplified environment files
├── scripts/                 # Startup script
├── data/                    # Volume mounts
└── (Rails app created by: rails new . --css tailwind --skip-test)
    ├── Gemfile              # Simplified (no pg, bcrypt)
    ├── Procfile.dev         # Same as wvu-moonshot
    ├── app/
    │   ├── controllers/
    │   │   ├── application_controller.rb
    │   │   ├── dashboard_controller.rb
    │   │   ├── wormholes_controller.rb
    │   │   └── admin_controller.rb
    │   ├── views/
    │   │   ├── layouts/application.html.erb
    │   │   ├── dashboard/index.html.erb
    │   │   ├── wormholes/index.html.erb
    │   │   └── admin/index.html.erb
    │   ├── helpers/
    │   │   ├── dashboard_helper.rb
    │   │   ├── wormholes_helper.rb
    │   │   ├── admin_helper.rb
    │   │   └── mock_data.rb (MockDataHelper module)
    │   ├── javascript/controllers/
    │   │   ├── application.js
    │   │   └── admin_controller.js
    │   └── assets/tailwind/
    │       └── application.css (using @theme pattern)
    └── config/
        ├── routes.rb        # 3 simple routes
        └── environments/    # Rails env configs
```

---

## What Qwen Should Do (Implementation Steps Reference)

### From WVU Moonshot, Adapt These Steps:

1. **Copy Dockerfile** ✅
   - From: `/Users/tam0013/Documents/git/wvu-moonshot/Dockerfile`
   - To: `/Users/tam0013/Documents/git/galaxy-game-ui/Dockerfile`
   - Change: Remove PostgreSQL dependencies (libpq-dev might stay for compatibility)

2. **Copy docker-compose files** ✅
   - From: `docker-compose.yml`, `docker-compose.dev.yml`
   - To: `galaxy-game-ui/` root
   - Change: Remove `db:` service entirely

3. **Copy up.sh / down.sh** ✅
   - Simple 1-line scripts to start/stop containers

4. **Copy Procfile.dev** ✅
   - Identical `web:` and `css:` processes

5. **Adapt Gemfile** ✅
   - Keep: rails, propshaft, puma, importmap-rails, turbo-rails, stimulus-rails, tailwindcss-rails
   - Remove: pg, bcrypt, image_processing, testing gems

6. **Adapt Tailwind setup** ✅
   - Use `@theme` block pattern (same as wvu-moonshot)
   - Define EVE Dashboard colors as CSS variables
   - Create `.glass-card` and `.space-background` reusable classes

7. **Use Stimulus controller pattern** ✅
   - For admin_controller.js: showToast(), appendEventLogEntry() methods
   - Same import and target pattern

8. **Layout template** ✅
   - Header with navigation
   - Main content area
   - Footer
   - Use EVE Dashboard color classes

---

## Quick Comparison: WVU Moonshot vs Galaxy Game UI

| Aspect | WVU Moonshot | Galaxy Game UI |
|---|---|---|
| **Primary Purpose** | Event check-in system | UI prototype showcase |
| **Database** | PostgreSQL 16 | ❌ None (mocked data only) |
| **Authentication** | Sessions + passwords | ❌ None needed |
| **Data Source** | ActiveRecord models | MockDataHelper (in-memory) |
| **Testing** | RSpec with 41 tests | ⏭️ Skip for now |
| **Asset Pipeline** | Propshaft + Tailwind | Same |
| **Frontend Framework** | Hotwire (Turbo + Stimulus) | Same |
| **Deployment** | Docker container | Docker container |
| **Primary Skill Needed** | Rails MVC + PostgreSQL | Rails views + Stimulus JS |

---

## External References

- **WVU Moonshot GitHub**: `/Users/tam0013/Documents/git/wvu-moonshot/`
- **Rails 8 Guide**: https://guides.rubyonrails.org/
- **Hotwire (Stimulus)**: https://stimulus.hotwired.dev/
- **Tailwind CSS**: https://tailwindcss.com/
- **Docker Documentation**: https://docs.docker.com/

---

## Summary: What to Copy & Adapt

✅ **COPY DIRECTLY**:
- Dockerfile template
- docker-compose.yml structure (remove db service)
- up.sh / down.sh scripts
- Procfile.dev
- Rails project structure (controllers, views, helpers)

✅ **ADAPT PATTERN**:
- Gemfile (remove DB gems)
- Tailwind CSS setup (use @theme with EVE Dashboard colors)
- Environment files (simplify to no DB vars)
- Layout template (header/nav/footer structure)

✅ **USE AS CODE REFERENCE**:
- Stimulus controller pattern (graduate_filter → admin_controller)
- Helper method organization
- View template structure

❌ **SKIP ENTIRELY**:
- Database migrations, models, seeds
- Authentication (sessions, passwords, bcrypt)
- CSV import logic
- RSpec tests (for now)

