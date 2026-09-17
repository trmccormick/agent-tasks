# Task: Investigate Why Images Are Not Displaying Locally

**Status**: BACKLOG (Ready for Qwen dispatch after facet-config investigation)  
**Priority**: HIGH (Blocks feature validation)  
**Created**: 2026-08-26  
**Assigned to**: Qwen  

---

## Problem Statement

**Symptom**: Image thumbnails do not render in local Stack Car environment on M4 Mac.

**Observations**:
- **Local M4** (Stack Car): No images displayed in catalog/filtered views
- **Dev VM hykudev**: Images display correctly
- **Production**: Images display correctly  
- **Branch**: `fix/hide-type-facet-add-show-more-facets` (HEAD: 6b26690)
- **Not blocking catalog/facet-limiting feature**, but prevents visual validation of search results

**Test URLs (Local M4 Stack Car)**:
- Homepage: `https://demo-wvu-knapsack.localhost.direct/?locale=en`
- Catalog: `https://demo-wvu-knapsack.localhost.direct/catalog?locale=en`
- Filtered: `https://demo-wvu-knapsack.localhost.direct/catalog?f%5Blocation_sim%5D%5B%5D=Clarysville+Inn%3B+Clarysville%3B+Allegany+Country%3B+Maryland%3B+United+States&locale=en`

---

## Acceptance Criteria

Investigation task MUST produce:

1. **Root Cause Identified**:
   - Image URL pattern traced (where thumbnails referenced in HTML)
   - Storage backend checked (file:// path, S3, filesystem path, etc.)
   - Local file system verified (existence, permissions, mount point)
   - Docker mount points validated (volume mounts in docker-compose.local.yml)
   - Image processing pipeline checked (ImageMagick, libvips, Hydra derivatives)

2. **Differential Analysis**:
   - Why M4 Stack Car lacks images while Dev VM and Production have them
   - Configuration differences identified (storage.yml, Hyku settings, Hyrax config)
   - Image path resolution traced for each environment

3. **Synthesis Report**:
   - Root cause documented
   - Fix recommendation provided (config change, missing mount, Docker setup, etc.)
   - Prioritized fix approach (if simple config vs requires data migration)

---

## Investigation Steps

### STEP 0: Prepare Environment
- Move task from backlog/ to active/ via git mv
- Create synthesis file path: `projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-LOCAL-IMAGES-NOT-DISPLAYING.md`

### STEP 1: Verify Image URLs in HTML
Render a catalog page and inspect image source attributes:
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?locale=en' -k \
  | grep -i 'src=.*\(jpg\|png\|gif\|webp\)' | head -10
```
**Check**:
- Are image URLs present in HTML? (not empty src="")
- What is the URL pattern? (e.g., `/images/...`, `http://...`, file path, etc.)

### STEP 2: Trace Image Storage Configuration
Check configuration files:
- `config/storage.yml` - Storage backend definition
- `config/initializers/active_storage.rb` - ActiveStorage setup
- `hyrax-webapp/config/environments/development.rb` - Dev environment storage config
- `docker-compose.local.yml` - Volume mounts and environment variables

**Check**:
- Which storage adapter is configured? (local filesystem, S3, Azure, etc.)
- Are volume mounts correct in Docker compose?
- Is a local data directory being used? (`data/storage`, `tmp/storage`, etc.)

### STEP 3: Verify Local File System
Check if image files actually exist locally:
```bash
# List storage directories
ls -la /Users/tam0013/Documents/git/wvu_knapsack/data/storage/ 2>/dev/null || echo "Directory not found"
ls -la /Users/tam0013/Documents/git/wvu_knapsack/file_cache/ 2>/dev/null || echo "Directory not found"
find /Users/tam0013/Documents/git/wvu_knapsack -name "*.jpg" -o -name "*.png" 2>/dev/null | head -20
```

**Check**:
- Do storage directories exist?
- Are image files present?
- File permissions correct for web server access?

### STEP 4: Verify Docker Mounts
Check Docker volumes in Stack Car:
```bash
# Inside container
docker exec <web_container> ls -la /app/storage 2>/dev/null || echo "Mount not found"
docker exec <web_container> ls -la /app/file_cache 2>/dev/null || echo "Mount not found"
```

**Check**:
- Are mounts accessible inside container?
- Do they point to correct host directories?
- Are files visible from container perspective?

### STEP 5: Trace Image Processing Pipeline
Check Hyrax/Hyku image derivative generation:
- `app/indexers/` - Which indexer handles images?
- `lib/hyku_knapsack/` - Any custom image processing?
- `hyrax-webapp/app/services/` - Hyrax image services

**Check**:
- Are derivatives being generated?
- Is thumbnail processing configured?
- Are processed images being stored?

### STEP 6: Compare Dev VM vs M4
If accessible to Qwen, check dev VM configuration:
```bash
# On dev VM
git log --oneline -5
grep -r "storage" config/initializers/ | head -10
diff /Users/tam0013/Documents/git/wvu_knapsack/docker-compose.local.yml \
     /path/to/dev/docker-compose.local.yml
```

**Check**:
- Configuration differences between environments
- File path handling differences
- Docker compose mount differences

---

## Synthesis Requirements

Create file: `projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-LOCAL-IMAGES-NOT-DISPLAYING.md`

**Format**:
- **Root Cause**: Clear statement of why images not displaying on M4 Stack Car
- **Evidence**: Logs, file checks, configuration findings that support root cause
- **Differential**: Why M4 is different from Dev VM and Production
- **Recommended Fix**: Specific action to resolve (config change, data migration, Docker setup, etc.)
- **Priority**: Effort estimate (trivial, easy, moderate, hard)
- **Blockers**: Any dependencies or additional info needed

---

## Context

**Environment**: M4 Mac, Stack Car (hostname-based multi-tenant routing)  
**Stack**: Hyku 7.1.0 + Hyrax 5.2.0, Rails 7.2.3, Ruby 3.3.10  
**Branch**: fix/hide-type-facet-add-show-more-facets (6b26690)  
**Related Issue**: Pre-existing (not related to facet-limiting feature work)  
**Token Budget**: Copilot at 87% usage; minimize Copilot involvement, Qwen driving investigation

---

## Agent Dispatch Interface

**For Qwen**: Copy the following dispatch handoff to agent chat:

```
## DISPATCH: Local Images Investigation

**Task File**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-26-HIGH-INVESTIGATION-LOCAL-IMAGES-NOT-DISPLAYING.md`

**What to do**:
1. Read the full task file (link above)
2. Follow STEP 0 through STEP 6
3. Create synthesis report per Synthesis Requirements section
4. Commit task move and synthesis report to agent-tasks repo

**Synthesis Report Path**: `projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-LOCAL-IMAGES-NOT-DISPLAYING.md`

**Acceptance Criteria** (must be met):
- Root cause identified and documented
- Evidence provided (file checks, config findings, logs)
- Differential analysis complete (why M4 ≠ Dev VM ≠ Production)
- Fix recommendation with priority/effort estimate

**Context**:
- M4 Mac, Stack Car environment
- Images missing in catalog/filtered views (but homepage displays normally)
- Dev VM and production both show images correctly
- Branch: fix/hide-type-facet-add-show-more-facets
- Not blocking current feature work, but prevents validation

**Questions?** Check task file STEP sections for detailed investigation approach.
```

---

## Notes

- **Not critical path** for facet-limiting feature deployment (feature works without images)
- **Secondary to**: Homepage vs Catalog facet config investigation (currently active with Qwen)
- **Pre-existing issue**: Not introduced by recent changes
- **Token conservation**: Qwen drives this, Copilot minimal involvement
