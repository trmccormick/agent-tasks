---
status: backlog
priority: HIGH
type: feature
system_domain: ACDA_PORTAL
mvp_alignment: IDENTIFIER_RECOVERY
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent** (Qwen).

Project: wvulibraries_acda_portal
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/tasks/backlog/2026-09-29-HIGH-FEATURE-IDENTIFIER-FIX-RAKE-TASKS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_acda_portal/tasks/backlog/2026-09-29-HIGH-FEATURE-IDENTIFIER-FIX-RAKE-TASKS.md \
         projects/wvulibraries_acda_portal/tasks/active/2026-09-29-HIGH-FEATURE-IDENTIFIER-FIX-RAKE-TASKS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_acda_portal/tasks -name "2026-09-29-HIGH-FEATURE-IDENTIFIER-FIX-RAKE-TASKS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Build Cleanup Rake Tasks for Broken wvul_ Records (Prepare for Re-import)

**Status**: BACKLOG
**Priority**: HIGH
**Type**: FEATURE
**Created**: 2026-09-29
**Last Updated**: 2026-09-30
**Codebase**: /Users/tam0013/Documents/git/hydra_acda_portal_public

---

## Background

**Old system (MCPPC)**: Individual Hydra Head site serving records at `https://mcppc.lib.wvu.edu/catalog/wvul_am1500_b6_f01_0031`

**New system (Hyku)**: Consolidated platform at `https://digitalhistory.lib.wvu.edu/` with corrected identifiers and URLs

**What happened**:
- Production ACDA Portal still points to old MCPPC URLs and identifiers
- Dev instance was successfully migrated to Hyku
- Need to sync production to match working Hyku data

**The problem**:

1. **Old identifiers** still have `wvul_` prefix (e.g., `wvul_am1500_b6_f01_0031`)
2. **Old URLs** point to retired MCPPC system
3. **Cached files** reference old record IDs (filesystem paths break)
4. **Acda#assign_id()** derives record ID from identifier, so changing identifier changes ID → breaks file lookups

## Problem Statement

**The Situation**: Production ACDA Portal has ~3,000 records with broken `wvul_*` identifiers
- Old identifiers: `wvul_am1500_b6_f01_0031` (from retired MCPPC system)
- Fedora files attached to these IDs: `thumbnail_file`, `image_file`, etc.
- Thumbnails don't display because files are orphaned under old ID paths
- Hyku has the correct records with proper identifiers (proven working on dev)

**Why re-import is cleaner**:
1. Bulkrax import workflow is proven (works on dev)
2. Re-import handles: metadata, thumbnail download from Hyku, Fedora storage (clean)
3. No risk of orphaned files or partial updates
4. Hyku becomes single source of truth going forward

**The Approach**:
1. **Diagnose** - Identify all `wvul_*` records
2. **Export report** - Show what will be deleted (for Sarah verification)
3. **DRY RUN** - Preview exact delete operations (Fedora + Solr eradication)
4. **Sarah reviews** - Confirms list is complete and correct
5. **Delete** - Remove records from ACDA Portal
6. **Re-import** - Sarah uses Bulkrax to import from Hyku (fresh, clean)

## Solution Overview

Build 3 rake tasks for **safe, verifiable deletion**:

1. **`identifiers:diagnose`** - Count and list all `wvul_*` records
2. **`identifiers:export_wvul[file.csv]`** - Export report (identifiers, Bulkrax IDs, counts)
3. **`identifiers:delete_wvul[csv_file]`** - Delete records from ACDA Portal (DRY RUN or APPLY=true)

**Workflow**:
```bash
# Step 1: Diagnose
bundle exec rake identifiers:diagnose
# Output: "Found 2847 records with wvul_ prefix"

# Step 2: Export report for Sarah to review
bundle exec rake identifiers:export_wvul[wvul_records.csv]
# Output: CSV with current_id, identifier, bulkrax_identifier

# Step 3: Preview deletions (DRY RUN)
bundle exec rake identifiers:delete_wvul[wvul_records.csv]
# Output: "[DRY RUN] Would delete 2847 records from Fedora + Solr"

# Step 4: Sarah reviews CSV, confirms deletion is correct

# Step 5: Execute deletion
bundle exec rake identifiers:delete_wvul[wvul_records.csv] APPLY=true
# Output: "✓ Deleted 2847 records. Eradicated from Solr."

# Step 6: Sarah re-imports via Bulkrax UI (using Hyku as source)
# Bulkrax handles: metadata, thumbnail download, Fedora storage, file containment
```

---

## Implementation Tasks

### Task 1: Implement `identifiers:diagnose`
**Goal**: Count and list all records with `wvul_*` prefix

**Steps**:
1. Query: `Acda.where("identifier LIKE 'wvul_%'").count`
2. Show summary:
   ```
   Records with wvul_ prefix: 2847
   These will be deleted and re-imported from Hyku
   ```
3. If VERBOSE environment variable set, list all records:
   ```
   bundle exec rake identifiers:diagnose VERBOSE=true
   ```
   Output table: current_id | identifier | bulkrax_identifier
4. Show next step: "Run: bundle exec rake identifiers:export_wvul[wvul_records.csv]"

**File to edit**: `/Users/tam0013/Documents/git/hydra_acda_portal_public/hydra/lib/tasks/fix_broken_identifiers.rake`
**Skeleton provided**: Yes (basic structure exists, needs implementation)

### Task 2: Implement `identifiers:export_wvul[output_file]`
**Goal**: Export all `wvul_*` records to CSV for Sarah to verify before deletion

**Steps**:
1. Accept output_file parameter (default: "wvul_records_to_delete.csv")
2. Query all records where identifier starts with `wvul_`
3. Export CSV with columns: current_id, identifier, bulkrax_identifier
4. Show summary:
   ```
   ✓ Exported 2847 wvul_ records to wvul_records_to_delete.csv
   
   Review the CSV and confirm with Sarah before deletion.
   Then run: bundle exec rake identifiers:delete_wvul[wvul_records_to_delete.csv]
   ```

**CSV Format**:
```
current_id,identifier,bulkrax_identifier
wvul_am1500_b6_f01_0031,wvul_am1500_b6_f01_0031,b-8-2986
wvul_am1500_b6_f01_0032,wvul_am1500_b6_f01_0032,b-8-2987
```

### Task 3: Implement `identifiers:delete_wvul[csv_file]`
**Goal**: Delete records from ACDA Portal (from Fedora + Solr eradication) with DRY RUN support

**Steps**:
1. Accept csv_file parameter with current_id list
2. Accept APPLY environment variable (defaults to DRY RUN)
3. For each record in CSV:
   - Find record by `Acda.find(current_id)`
   - **DRY RUN**: Show "[DRY RUN] Would delete: {current_id} from Fedora + Solr"
   - **APPLY=true**:
     a. Delete from Fedora: `record.destroy` (cascades to contained files like thumbnail_file)
     b. Eradicate from Solr: `ActiveFedora::Base.eradicate(current_id)` (avoid tombstone)
     c. Show: "✓ Deleted: {current_id}"
4. Show progress every 100 records ("Deleting... 100/2847 complete (3.5%)")
5. Show summary at end:
   ```
   DRY RUN: Would delete 2847 records from Fedora + Solr
   Run with APPLY=true to execute
   ```
   OR (if APPLY=true):
   ```
   ✓ Deleted 2847 records from Fedora
   ✓ Eradicated 2847 records from Solr (no tombstones)
   
   Next steps:
   1. Sarah configures Bulkrax in ACDA Portal to use Hyku as source
   2. Sarah runs Bulkrax import (pulls records from Hyku)
   3. Bulkrax handles all re-ingestion (metadata, thumbnails, Fedora storage)
   ```

---

## Architecture & Gotchas

### Key Model Methods
```ruby
# Acda model
record.id                    # ActiveFedora ID (derived from identifier)
record.identifier            # String property (DC identifier)
record.assign_id             # Generates ID from current identifier
record.bulkrax_identifier    # Source tracking ID from Bulkrax
record.thumbnail_file        # PCDM file (Fedora-stored)
record.save!                 # Persists to database/Fedora
```

### Fedora Deletion & Containment
```ruby
# When you destroy a record, ActiveFedora cascades to contained files
record = Acda.find('wvul_am1500_b6_f01_0031')
record.destroy  # Deletes record + thumbnail_file + all contained files

# Then eradicate to avoid Solr tombstones (matches existing delete_records.rb pattern)
ActiveFedora::Base.eradicate('wvul_am1500_b6_f01_0031')
```

### DRY RUN Pattern
```ruby
apply = ENV['APPLY'] == 'true'

CSV.foreach(csv_file, headers: true) do |row|
  current_id = row['current_id']
  
  if apply
    record = Acda.find(current_id)
    record.destroy
    ActiveFedora::Base.eradicate(current_id)
    puts "✓ Deleted: #{current_id}"
  else
    puts "[DRY RUN] Would delete: #{current_id}"
  end
end

puts "Run with APPLY=true to execute" unless apply
```

### Progress Indication
- Show count every 100 records (batch is ~3,000 total)
- Example: "Deleting... 100/2847 complete (3.5%)"
- Helps user monitor long-running operations

---

## Sample Test Data & Local Testing

**CSV File**: `/Users/tam0013/Documents/git/hydra_acda_portal_public/data/imports/1_20260812203120/Test4tracy.csv`

**Testing workflow**:
1. Use Test4tracy.csv to import test records (via Bulkrax or SQL)
2. Manually add `wvul_` prefix to a few test identifiers:
   ```sql
   UPDATE acda SET identifier = 'wvul_' || identifier WHERE id LIKE 'am1414%' LIMIT 10;
   ```
3. Run diagnose: `bundle exec rake identifiers:diagnose VERBOSE=true`
   - Should show ~10 records with wvul_ prefix
4. Export CSV: `bundle exec rake identifiers:export_wvul[test_delete.csv]`
   - Should create CSV with 10 rows (current_id, identifier, bulkrax_identifier)
5. Run DRY RUN delete: `bundle exec rake identifiers:delete_wvul[test_delete.csv]`
   - Should show "[DRY RUN] Would delete 10 records"
6. Run with APPLY: `bundle exec rake identifiers:delete_wvul[test_delete.csv] APPLY=true`
   - Should delete the 10 test records
7. Verify gone: `bundle exec rake identifiers:diagnose VERBOSE=true`
   - Should show 0 records with wvul_ prefix

---

## Acceptance Criteria

✅ All 3 rake tasks implemented in `/hydra/lib/tasks/fix_broken_identifiers.rake`  
✅ `identifiers:diagnose` correctly counts records with `wvul_` prefix  
✅ `identifiers:export_wvul[file.csv]` exports CSV with current_id, identifier, bulkrax_identifier  
✅ `identifiers:delete_wvul[csv]` supports DRY RUN (default) and APPLY=true  
✅ Deletion handles Fedora cascade + Solr eradication (no tombstones)  
✅ Progress indicators show every 100 records (for ~3,000 record batch)  
✅ Tasks tested locally with Test4tracy.csv sample data  
✅ Output messages are clear ("[DRY RUN] Would delete", "✓ Deleted", etc.)  
✅ CSV format matches what export_wvul creates  
✅ No breaking changes to existing code  
✅ Documentation strings in code explain each task  

---

## Synthesis Report Template

Save as: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/summaries/2026-09-29-FEATURE-IDENTIFIER-FIX-RAKE-TASKS-SYNTHESIS.md`

```markdown
# Synthesis Report: Identifier Fix Rake Tasks

**Date**: 2026-09-29  
**Status**: [COMPLETED | BLOCKED | IN_PROGRESS]  
**Implementation**: /hydra/lib/tasks/fix_broken_identifiers.rake

## Summary
[What was built, what works, what still needs work]

## Test Results
[What was tested, test data used, results]

## Known Issues
[Any bugs, edge cases, limitations]

## Recommendations
[Next steps, improvements, related work]

## Deployment Notes
[Any deployment considerations for production]
```

---

## Dependencies

**Blocks**: 2026-09-29-HIGH-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS.md
**Blocked by**: None
**Related**: FIX_BROKEN_IDENTIFIERS.md (user-facing guide)

---

## Notes for Agent

- Skeleton code exists with basic structure - use it as starting point
- **Core logic**: Find records where `identifier LIKE 'wvul_%'`, export to CSV, delete from Fedora + Solr
- **Important**: `delete_wvul` must support both DRY RUN (default) and APPLY=true
- Deletion must cascade (record.destroy handles contained files like thumbnail_file)
- Eradicate pattern matches existing delete_records.rb (see that file for reference)
- CSV import: Use Ruby CSV library with headers: true
- Add progress indicators every 100 records (batch is ~3,000 total)
- Test thoroughly with Test4tracy.csv local data before marking complete
- All 3 tasks must be in single file: fix_broken_identifiers.rake
- Pattern to follow: See url_checks.rake and delete_records.rb for similar patterns
- Keep messages simple and clear ([DRY RUN], ✓, ⚠️ for status)
