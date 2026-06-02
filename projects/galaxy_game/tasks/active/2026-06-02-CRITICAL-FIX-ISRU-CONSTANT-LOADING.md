---
title: Fix ISRUCapabilityManager Constant Loading Blocker
status: active
priority: CRITICAL
type: DIAGNOSIS
date_created: 2026-06-02
date_started: 2026-06-02
assigned_to: GPT-5 Mini (0x)
parent_task: Fix 11 RSpec Failures
blocked_by: None
blocks: ISRUCapabilityManager spec execution, all downstream shortage detection tests
---

# ISRUCapabilityManager Constant Loading Blocker

**Context**: While attempting to run `spec/services/logistics/isru_capability_manager_spec.rb`, the constant `Logistics::ISRUCapabilityManager` is not loading in the Rails environment. The service file exists and has valid syntax, but the autoloader is not recognizing it.

**Error**:
```
NameError: uninitialized constant Logistics::ISRUCapabilityManager
```

**Evidence**:
- ✅ File exists: `app/services/logistics/isru_capability_manager.rb`
- ✅ Syntax valid: `ruby -c app/services/logistics/isru_capability_manager.rb` returns "Syntax OK"
- ❌ Constant not autoloading: `bundle exec rails runner "puts Logistics::ISRUCapabilityManager"` raises NameError
- ✅ File structure: Proper namespace (`module Logistics; class ISRUCapabilityManager < BaseService`)

---

## Diagnosis Steps

### Step 1: Verify Autoload Configuration
```bash
docker exec web bash -c 'cd /home/galaxy_game && bundle exec rails runner "
puts Rails.autoloaders.main.dirs.grep(/services/)[0..3]
puts \"---\"
puts Rails.autoloaders.main.load_paths[0..3]
"'
```

**What to look for**: Should include `app/services` directory in load paths.

### Step 2: Check Zeitwerk Namespace Map
```bash
docker exec web bash -c 'cd /home/galaxy_game && bundle exec rails runner "
require \"active_support\"
puts Zeitwerk::Loader.each_gem { |name| puts name if name.include?(\"rails\") }.first(3)
"'
```

### Step 3: Try Explicit Require (Diagnostic Only)
```bash
docker exec web bash -c 'cd /home/galaxy_game && bundle exec rails runner "
require_relative \"app/services/logistics/isru_capability_manager\"
puts Logistics::ISRUCapabilityManager
"'
```

If explicit require works, the issue is autoload configuration, not the file itself.

---

## Likely Causes (in order)

1. **Autoload path not configured** — Check `config/application.rb` or `config/initializers/` for autoload configuration
2. **Zeitwerk eager load issue** — File not being eager loaded in test environment
3. **Module namespace mismatch** — Module name doesn't match file path structure
4. **Spring cache stale** — Spring preloader has stale state (less likely but possible)

---

## Fix Process

1. Run **Step 1** above to confirm autoload paths include `app/services`
2. If path is missing, add to `config/application.rb`:
   ```ruby
   config.autoload_paths << Rails.root.join("app", "services")
   ```
3. Restart Rails environment: `docker-compose restart web`
4. Re-run constant check to verify it loads
5. Run the targeted spec: `docker exec web bundle exec rspec spec/services/logistics/isru_capability_manager_spec.rb`

---

## Return

Provide:
- Diagnosis output from Step 1 and Step 3
- Whether autoload path includes `app/services`
- The fix applied (if any)
- Spec execution result (pass/fail/error)

Stop after diagnosis and fix. Do not write additional code or apply further changes unless told to proceed.
