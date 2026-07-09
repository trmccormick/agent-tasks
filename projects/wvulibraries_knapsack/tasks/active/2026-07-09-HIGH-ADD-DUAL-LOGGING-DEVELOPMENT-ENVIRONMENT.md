---
title: Add Dual Logging to Development Environment
priority: HIGH
date_created: 2026-07-09
assigned_to: Qwen (Implementation Agent)
status: active
github_issue: "N/A (enhancement)"
branch: fix/facet-links-and-hide-type-facet
---

## Task Summary
Add dual logging (file + STDOUT) to development environment so logs are captured to file for debugging while Stack Car continues to stream to terminal.

## Problem Statement
Currently in development (Stack Car), logs only go to STDOUT. They're not written to `hyrax-webapp/log/development.log`, making it impossible to debug issues that require reviewing logs after the container has stopped or logs have scrolled past the terminal.

## Acceptance Criteria
✅ Logs written to BOTH `hyrax-webapp/log/development.log` AND STDOUT when running Stack Car
✅ `hyrax-webapp/config/environments/development.rb` modified with dual logging logic
✅ Syntax validation passes: `ruby -c hyrax-webapp/config/environments/development.rb`
✅ Local testing: Start Stack Car, verify logs appear in `hyrax-webapp/log/development.log`
✅ Commit to `fix/facet-links-and-hide-type-facet` branch

## Implementation Details

### File to Modify
**File**: `hyrax-webapp/config/environments/development.rb` (Hyku submodule)

**Current Code** (lines ~82-86):
```ruby
  config.web_console.whitelisted_ips = ['172.18.0.0/16', '172.27.0.0/16', '0.0.0.0/0']
  if ENV["RAILS_LOG_TO_STDOUT"].present?
    logger           = ActiveSupport::Logger.new(STDOUT)
    logger.formatter = config.log_formatter
    config.logger = ActiveSupport::TaggedLogging.new(logger)
  end
```

**Replace With**:
```ruby
  config.web_console.whitelisted_ips = ['172.18.0.0/16', '172.27.0.0/16', '0.0.0.0/0']
  
  # Always log to file (for development debugging)
  file_logger = ActiveSupport::Logger.new(
    Rails.root.join('log', 'development.log')
  )
  file_logger.formatter = config.log_formatter

  # Log to STDOUT if enabled + also to file (dual logging)
  if ENV["RAILS_LOG_TO_STDOUT"].present?
    stdout_logger = ActiveSupport::Logger.new(STDOUT)
    stdout_logger.formatter = config.log_formatter
    config.logger = ActiveSupport::TaggedLogging.new(
      ActiveSupport::Logger.broadcast(file_logger).broadcast(stdout_logger)
    )
  else
    config.logger = ActiveSupport::TaggedLogging.new(file_logger)
  end
```

### Verification Steps
1. **Syntax Check**: 
   ```bash
   cd hyrax-webapp && ruby -c config/environments/development.rb
   ```
   Expected: "Syntax OK"

2. **Local Testing**:
   ```bash
   cd /Users/tam0013/Documents/git/wvu_knapsack
   sh down.sc.local.sh
   sleep 3
   sh ./up.sc.local.sh
   # Wait 2-3 minutes for containers to fully start
   ```

3. **Verify Logs Are Written**:
   ```bash
   ls -lah hyrax-webapp/log/development.log
   tail -50 hyrax-webapp/log/development.log | head -20
   ```
   Expected: Log file exists with recent timestamps and Rails startup logs

4. **Verify STDOUT Still Works**:
   - Stack Car should still show live logs on terminal
   - `sc logs web -f` should work as before

### Git Commit Details
**Branch**: `fix/facet-links-and-hide-type-facet`

**Commit Message**:
```
fix: Add dual logging to development environment for file capture

- Broadcast logs to both file (log/development.log) and STDOUT
- Ensures logs are captured to hyrax-webapp/log/development.log for debugging
- Maintains STDOUT logging for Stack Car visibility
```

**Files Changed**:
- `hyrax-webapp/config/environments/development.rb`

## Notes
- This is a submodule (hyrax-webapp), so commit from within that directory:
  ```bash
  cd hyrax-webapp && git add config/environments/development.rb && git commit -m "..." && git push origin HEAD:<current-branch>
  ```
- The submodule may be in detached HEAD state - that's expected
- After implementation, main repo will see submodule pointer update
- This change pairs with production logging fixes (already committed in commit 7426102)

## Related Tasks
- **Completed**: `2026-07-09-CRITICAL-BUGFIX-PRODUCTION-LOGGING-NOT-CAPTURED.md` — Added dual logging to production + Docker rotation
- **This Task**: Development dual logging (same pattern as production)
- **Next**: Deploy to HykuDev and validate both environments capture logs to file
