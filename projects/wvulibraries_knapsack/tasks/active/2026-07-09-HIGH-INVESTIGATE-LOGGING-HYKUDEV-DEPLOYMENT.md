---
status: active
priority: HIGH
assigned_to: Qwen
created: 2026-07-09
updated: 2026-07-09
branch: fix/facet-links-and-hide-type-facet
---

# Task: Investigate & Fix Logging Issues on HykuDev Deployment

## Context
The `fix/facet-links-and-hide-type-facet` branch was deployed to the hykudev VM (production-like environment). During testing, logging issues were observed that do not appear in local Stack Car development.

**Production status**: Main production (`admin-hyku.lib.wvu.edu`) is running normally.  
**Development status**: Stack Car local dev works but has different logging setup than production.  
**Issue scope**: Logs not appearing in expected location(s) on hykudev deployment.

## Objective
Research and document the logging configuration differences between:
1. **Local dev** (Stack Car via `up.sc.local.sh`)
2. **HykuDev VM** (production-like via `up.sh` script)
3. **Production** (admin-hyku.lib.wvu.edu)

Then identify and implement the necessary fixes to ensure logs are properly captured on hykudev.

## Key Questions to Research

### 1. Log Volume Mounts & Paths
- [ ] What log volumes/bind-mounts are configured in each environment?
  - Check: `docker-compose.yml` (local) vs `docker-compose.production.yml` (hykudev/prod)
  - Look for: `./data/logs/rails`, `./data/logs/solr`, other log paths
- [ ] Are log directories properly created with correct ownership?
  - Local dev may skip log volume configuration; production expects `/data/logs/` mounted

### 2. Rails Logging Configuration
- [ ] How is Rails logging configured per environment?
  - Check: `config/environments/production.rb`, `config/environments/development.rb`
  - Does HykuDev need specific log path overrides?
- [ ] Is the web container logging to stdout or to file?
  - Stack Car: Likely uses stdout (appears in `sc logs web -f`)
  - HykuDev: May need file-based logging to `/data/logs/rails`

### 3. Docker Compose Differences
- [ ] What differences exist in `docker-compose.local.yml` vs `docker-compose.production.yml`?
  - Specifically: volume mounts for logs
  - Service log driver configurations
- [ ] Does HykuDev use `up.sh` (production compose) or different configuration?

### 4. Log Driver & Rotation
- [ ] Is there a log rotation policy configured?
- [ ] Are logs being captured by Docker's log drivers?

## Investigation Steps

1. **Compare compose files**:
   ```bash
   cd /Users/tam0013/Documents/git/wvu_knapsack
   diff -u docker-compose.local.yml docker-compose.production.yml | grep -A5 -B5 "logs\|volumes"
   ```

2. **Check Rails environment configs**:
   ```bash
   # Look for logger configuration
   grep -r "logger\|log_level\|log_to_stdout" config/environments/
   ```

3. **Verify on HykuDev VM**:
   - SSH into hykudev
   - Check: `ls -la /data/logs/rails/` (or wherever logs should be)
   - Check: `docker logs wvu_knapsack-web-1` (see container stdout)
   - Check: `docker inspect wvu_knapsack-web-1 | grep -A5 LogDriver`

4. **Create synthesis report** with findings:
   - Document environment-specific logging configurations
   - Identify the root cause of missing logs on hykudev
   - Propose fixes

## Acceptance Criteria
- [ ] Written synthesis report documenting logging setup differences in `summaries/SYNTHESIS-LOGGING-INVESTIGATION.md`
- [ ] Root cause identified (missing volume mount, misconfigured logger, etc.)
- [ ] Proposed fix documented with implementation steps
- [ ] Either:
  - Docker compose configuration changes identified, OR
  - Rails environment config changes identified, OR
  - Both

## Related Files
- Local dev: `docker-compose.local.yml`, `up.sc.local.sh`
- Production: `docker-compose.production.yml`, `up.sh`
- Rails: `config/environments/production.rb`, `config/initializers/`
- Hyrax docs: See `HYKU_BUILD_GUIDE.md` for logging expectations

## Notes
- Do NOT implement fixes yet—only research and document
- Focus on understanding WHY logs aren't appearing, not just "make them appear"
- Consider that Stack Car may be hiding logging differences that exist in production
- HykuDev should mirror production logging setup for accurate testing
