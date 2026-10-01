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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/tasks/backlog/2026-09-29-HIGH-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/wvulibraries_acda_portal/tasks/backlog/2026-09-29-HIGH-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS.md \
         projects/wvulibraries_acda_portal/tasks/active/2026-09-29-HIGH-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_acda_portal/tasks -name "2026-09-29-HIGH-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS.md"
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

# TASK: Build Thumbnail Regeneration Rake Tasks for ACDA Records

**Status**: BACKLOG
**Priority**: HIGH
**Type**: FEATURE
**Created**: 2026-09-29
**Last Updated**: 2026-09-29
**Codebase**: /Users/tam0013/Documents/git/hydra_acda_portal_public

---

## Background

After fixing identifier mismatches (Task 1), thumbnails need to be regenerated and stored in Fedora (persistent database storage) instead of relying on fragile filesystem cache paths.

**Current situation**:
- Thumbnails are already in Fedora/PCDM (via `directly_contains_one :thumbnail_file`)
- But some records lost thumbnails during migration
- Can regenerate from available PDFs (in `available_by` URLs)
- Storing in Fedora makes them immune to future identifier changes

**Why this matters**:
- Filesystem cache (`/tmp/`) can be cleared by system or deploys
- Fedora storage is persistent and backed up
- Thumbnails won't break if identifiers change again

## Problem Statement

After identifier fixes, ~3,000 records need thumbnails regenerated from their available PDFs and stored persistently in Fedora. This ensures thumbnails survive future system changes.

## Solution Overview

Build 3 rake tasks to:
1. **Diagnose** which records are missing thumbnails
2. **Regenerate all** missing thumbnails from available PDFs
3. **Regenerate batch** for selective operations (specific record IDs)

All tasks support **DRY RUN** mode (ENV['APPLY'] = 'true' to execute).

---

## Implementation Tasks

### Task 1: Implement `thumbnails:diagnose`
**Goal**: Check which records are missing thumbnails in Fedora

**Steps**:
1. Query `Acda.find_each`
2. For each record:
   - Check if `record.thumbnail_file.present?`
   - Check if `record.available_by.present?` (has PDF source)
3. Count and categorize:
   - Records WITH thumbnails ✓
   - Records MISSING thumbnails (but PDF available) ✗
   - Records MISSING thumbnails (no PDF source) ⚠
4. Output table:
   - If missing thumbnails: show record ID, identifier, PDF URL (first 80 chars)
   - Summary showing counts

**Code Reference**:
```ruby
record.thumbnail_file.present?
record.available_by  # String or Array of URLs
Acda.count           # Total record count
```

### Task 2: Implement `thumbnails:regenerate`
**Goal**: Bulk regenerate all missing thumbnails from PDFs and store in Fedora

**Steps**:
1. Query all Acda records with `thumbnail_file.blank?` AND `available_by.present?`
2. For each record (order matters - show progress):
   - Extract PDF URL from `available_by` (handle String or Array)
   - **DRY RUN mode**: Show what would happen
   - **APPLY=true mode**:
     a. Download PDF to `/tmp/pdf_download_{record_id}.pdf`
     b. Verify file exists and has content
     c. Convert PDF[0] to JPG at `/tmp/thumb_generate_{record_id}.jpg`
        - Use MiniMagick with settings: density 150, quality 85, white background, alpha remove
     d. Verify thumbnail exists
     e. Attach to Fedora: `record.build_thumbnail_file` + `ImportLibrary.set_file` + `record.save!`
     f. Clean up temp files
3. Summary: "Regenerated: X, Errors: Y"
4. Suggest verification: "bundle exec rake thumbnails:diagnose"

**Code Reference**:
```ruby
# Download with Net::HTTP
uri = URI(url)
http = Net::HTTP.new(uri.host, uri.port)
response = http.request(Net::HTTP::Get.new(uri.request_uri))
File.open(output_path, 'wb') { |f| f.write(response.body) }

# Convert PDF to image
MiniMagick::Tool::Convert.new do |convert|
  convert << "-density" << "150"
  convert << "-quality" << "85"
  convert << "-background" << "white"
  convert << "-alpha" << "remove"
  convert << "#{pdf_path}[0]"
  convert << output_path
end

# Attach to Fedora
record.files.build unless record.files.present?
ImportLibrary.set_file(record.build_thumbnail_file, 'application/jpg', thumbnail_path)
record.save!
```

### Task 3: Implement `thumbnails:regenerate_batch[id1,id2,id3]`
**Goal**: Regenerate thumbnails for specific records (comma-separated IDs)

**Steps**:
1. Accept IDs parameter (comma-separated string)
2. Split and strip: `args[:ids].split(',').map(&:strip)`
3. For each record ID:
   - Find record by Acda.find(id)
   - Check if already has thumbnail (skip if yes)
   - Check if has available_by (skip if no)
   - **DRY RUN**: Show what would happen
   - **APPLY=true**: Execute (same as regenerate task)
4. Summary: "Regenerated: X, Errors: Y"

**Usage Example**:
```bash
bundle exec rake thumbnails:regenerate_batch[id1,id2,id3] APPLY=true
```

---

## Architecture & Gotchas

### Key Model Methods
```ruby
record.thumbnail_file             # PCDM file object
record.thumbnail_file.present?    # Boolean check
record.available_by               # String URL or Array of URLs
record.files.build                # Initialize files collection
record.save!                       # Persist to Fedora
```

### PCDM File Attachment
```ruby
# The pattern used throughout the app
record.files.build unless record.files.present?
ImportLibrary.set_file(record.build_thumbnail_file, 'application/jpg', file_path)
record.save!
```

### PDF Download Best Practices
- Set User-Agent header (some servers block scripts)
- Handle redirects (follow_location)
- Check response code (must be 200-299)
- Verify downloaded file has content (size > 0)

### MiniMagick Safety
- Wrap in begin/rescue (MiniMagick::Error)
- Use "[0]" to get first page of PDF
- Density/quality tradeoff: higher quality = slower conversion
- Settings used: 150 density (readable), 85 quality (balance file size/quality)

### Temp File Management
- Always cleanup after use: `FileUtils.rm_f(path)`
- Use unique names: `/tmp/pdf_download_{record_id}.pdf`
- Check file exists before processing
- Handle FileUtils errors gracefully

### DRY RUN Pattern
```ruby
apply = ENV['APPLY'] == 'true'

if apply
  # Download, convert, attach
  puts "✓ Regenerated"
  regenerated += 1
else
  puts "[DRY RUN - would regenerate]"
  skipped += 1
end

if !apply
  puts "Run with APPLY=true to execute"
end
```

### Error Handling
- Per-record try/catch (don't crash on PDF download failure)
- Log specific error (URL failed? Conversion failed? Attachment failed?)
- Continue with next record
- Show error count in summary

---

## Sample Test Data

**CSV File**: `/Users/tam0013/Documents/git/hydra_acda_portal_public/data/imports/1_20260812203120/Test4tracy.csv`

**Sample records**:
- All have `dcterms:identifier` (e.g., am1414_b01_f01_0001)
- All have `edm:isShownBy` (PDF URLs from digitalhistory.lib.wvu.edu)

**Testing workflow**:
1. Import CSV via Bulkrax (creates records with available_by URLs)
2. Manually delete thumbnail_file from one test record:
   ```ruby
   record = Acda.where(identifier: 'am1414_b01_f01_0001').first
   record.thumbnail_file = nil
   record.save!
   ```
3. Run `bundle exec rake thumbnails:diagnose` - should show missing
4. Run `bundle exec rake thumbnails:regenerate` (DRY RUN) - review output
5. Run `bundle exec rake thumbnails:regenerate APPLY=true` - execute
6. Verify `bundle exec rake thumbnails:diagnose` - should show regenerated
7. Check UI: thumbnail should display on record page

---

## Acceptance Criteria

✅ All 3 rake tasks implemented in `/hydra/lib/tasks/regenerate_thumbnails.rake`  
✅ Each task supports DRY RUN mode (ENV['APPLY'] check)  
✅ PDF download with proper error handling  
✅ MiniMagick conversion with safe settings  
✅ Fedora attachment via ImportLibrary  
✅ Temp file cleanup after use  
✅ Per-record error handling (one error doesn't stop batch)  
✅ CSV import/export works correctly (diagnose output queryable)  
✅ Tasks tested locally with Test4tracy.csv data  
✅ No breaking changes to existing code  
✅ Documentation strings in code explain each task  

---

## Synthesis Report Template

Save as: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_acda_portal/summaries/2026-09-29-FEATURE-THUMBNAIL-REGENERATION-RAKE-TASKS-SYNTHESIS.md`

```markdown
# Synthesis Report: Thumbnail Regeneration Rake Tasks

**Date**: 2026-09-29  
**Status**: [COMPLETED | BLOCKED | IN_PROGRESS]  
**Implementation**: /hydra/lib/tasks/regenerate_thumbnails.rake

## Summary
[What was built, what works, what still needs work]

## Test Results
[What was tested, test data used, results]
- Diagnose output (records missing thumbnails)
- Single record regeneration (dry run + apply)
- Batch regeneration performance
- Error handling (broken URLs, PDF conversion failures)

## Known Issues
[Any bugs, edge cases, limitations]
- PDF URLs that redirect?
- Large PDF files (timeout considerations)?
- Network connectivity (retry logic needed?)?

## Recommendations
[Next steps, improvements, related work]

## Deployment Notes
[Any deployment considerations for production]
- Network connectivity from production
- Expected runtime for 3,000 records
- Monitoring/logging recommendations
```

---

## Dependencies

**Blocks**: None (or future work: integration test with full migration)
**Blocked by**: 2026-09-29-HIGH-FEATURE-IDENTIFIER-FIX-RAKE-TASKS.md (should complete identifier fixes first)
**Related**: FIX_BROKEN_IDENTIFIERS.md (user-facing guide)

---

## Notes for Agent

- Skeleton code exists with basic structure
- Add progress indicators for large batches (e.g., show every 10th record)
- Network requests should have reasonable timeouts
- Test thoroughly with sample data before marking complete
- All tasks must be in single file: regenerate_thumbnails.rake
- Consider performance: 3,000 records × PDF download + conversion could be slow
  - Batch processing might be preferable to one-by-one in production
  - But for this task, implement the basic approach - optimization is future work
