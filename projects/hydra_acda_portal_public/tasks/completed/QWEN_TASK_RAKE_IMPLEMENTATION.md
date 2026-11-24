# Qwen Task: Build and Test Identifier/Thumbnail Rake Tasks

**Status:** Ready for implementation  
**Priority:** High  
**Token Budget:** Full task (we're building for local testing)

## Overview

Two rake task files have been scaffolded with basic structure. Qwen should:
1. **Complete implementations** of both rake task files
2. **Add proper error handling** throughout
3. **Test locally** with sample CSV data
4. **Document** any edge cases found

## Files to Build

### File 1: `hydra/lib/tasks/fix_broken_identifiers.rake` (PARTIALLY DONE)
**Status:** Scaffolded - needs implementation  
**Current Size:** 9.4 KB (basic structure + comments)

#### Tasks to Complete:
1. `identifiers:diagnose` - Query Acda records for ID mismatches
2. `identifiers:export_mismatches[output_file]` - Export to CSV
3. `identifiers:fix_single[current_id, new_identifier]` - Single record test
4. `identifiers:fix_from_csv[csv_file]` - Bulk update with file migration
5. `identifiers:delete_bulkrax_entries[csv_file]` - Delete Bulkrax entries
6. `identifiers:list_wvul` - List wvul_ prefix records

#### Implementation Notes:
- All tasks must support DRY RUN mode (ENV['APPLY'] check)
- File migrations: `/home/hydra/tmp/{thumbnails,images,pdf}/OLD_ID.{ext}` → `NEW_ID.{ext}`
- Database: Use `Acda` model, `Bulkrax::Entry` for deletions
- The `assign_id` method in Acda derives ID from identifier field
- Record ID is in `record.id`, identifier in `record.identifier`

### File 2: `hydra/lib/tasks/regenerate_thumbnails.rake` (PARTIALLY DONE)
**Status:** Scaffolded - needs implementation  
**Current Size:** 6.6 KB (basic structure + comments)

#### Tasks to Complete:
1. `thumbnails:diagnose` - Check which records missing thumbnail_file
2. `thumbnails:regenerate` - Bulk regenerate all missing from PDFs
3. `thumbnails:regenerate_batch[id1,id2,id3]` - Regenerate specific records

#### Implementation Notes:
- Check `record.thumbnail_file.present?` to see if attached
- PDFs available in `record.available_by` (URL or array of URLs)
- Download PDF from URL → Convert to JPG with MiniMagick → Attach to Fedora
- Use `ImportLibrary.set_file(record.build_thumbnail_file, 'application/jpg', file_path)`
- Record model is `Acda`, has method `record.save!`
- Temp files in `/tmp/` should be cleaned up after use

## Sample Data for Testing

**File:** `/Users/tam0013/Documents/git/hydra_acda_portal_public/data/imports/1_20260812203120/Test4tracy.csv`

**CSV Columns:** (Dublin Core)
```
dcterms:identifier  - Record identifier (e.g., am1414_b01_f01_0001)
edm:isShownBy       - PDF URL (download source)
edm:preview         - External preview image URL
...
```

**Sample Records:**
- `am1414_b01_f01_0001` through `am1414_b01_f01_XXXX`
- All have `dcterms:identifier` and `edm:isShownBy` (PDF URLs)

## Testing Strategy

### Phase 1: Local Dev Setup
```bash
# 1. Import sample CSV via Bulkrax UI or rails console
#    Use: data/imports/1_20260812203120/Test4tracy.csv
#    Creates records with identifiers like: am1414_b01_f01_0001

# 2. Verify records in DB
#    rails console: Acda.where(identifier: "am1414_b01_f01_0001").first

# 3. Change identifier on TEST record only
#    record.update!(identifier: "test_modified_identifier")
#    This breaks the ID/identifier mismatch
```

### Phase 2: Test Identifier Fixes
```bash
# 1. Diagnose
bundle exec rake identifiers:diagnose
# Should show your test record with mismatch

# 2. Export
bundle exec rake identifiers:export_mismatches[test_mismatches.csv]

# 3. Fix single record (DRY RUN)
bundle exec rake identifiers:fix_single[old_id,am1414_b01_f01_0001]
# Review output

# 4. Fix with APPLY
bundle exec rake identifiers:fix_single[old_id,am1414_b01_f01_0001] APPLY=true
# Verify record.id updated

# 5. Verify
bundle exec rake identifiers:diagnose
```

### Phase 3: Test Thumbnail Regen
```bash
# 1. Remove thumbnail from test record
#    rails console: record.thumbnail_file = nil; record.save!

# 2. Diagnose
bundle exec rake thumbnails:diagnose
# Should show missing thumbnail

# 3. Regenerate (DRY RUN)
bundle exec rake thumbnails:regenerate
# Should show what would happen

# 4. Regenerate (APPLY)
bundle exec rake thumbnails:regenerate APPLY=true
# Downloads PDF, generates thumb, attaches to Fedora

# 5. Verify
bundle exec rake thumbnails:diagnose
# Should show thumbnail present
```

## Key Implementation Details

### Model Associations
```ruby
# Acda model has:
property :identifier           # String
property :available_by         # String or URL
has_one :thumbnail_file        # PCDM file
directly_contains_one :thumbnail_file  # Via PCDM

# Fedora file attachment via ImportLibrary
ImportLibrary.set_file(record.build_thumbnail_file, 'application/jpg', file_path)
```

### ID Generation
```ruby
# record.assign_id derives from identifier field:
# - Removes protocol/domain
# - Removes special chars: /:?%&=#+_
# - Removes periods
# - Returns cleaned string

# When identifier changes, assign_id returns different value
# This breaks file lookups in /tmp/
```

### CSV Formats

**For identifiers:export_mismatches:**
```
current_id,expected_id,identifier,bulkrax_identifier,has_thumbnail,has_image,has_pdf
```

**For identifiers:fix_from_csv (user creates this):**
```
current_id,new_identifier
wvul_am1500_b6_f01_0031,am1500_b6_f01_0033
```

**For identifiers:delete_bulkrax_entries:**
```
bulkrax_identifier
b-123-1
b-123-2
```

## Acceptance Criteria

✅ All tasks implement DRY RUN mode  
✅ All tasks have error handling per-record  
✅ File operations are safe (check existence before overwrite)  
✅ CSV export/import works properly  
✅ Thumbnail regeneration downloads PDFs successfully  
✅ Tasks work with local test data  
✅ Documentation strings in code explain each task  
✅ Logging shows progress/errors clearly

## Questions/Edge Cases to Handle

1. **Multiple values in CSV columns?** (Some fields have arrays) → Use split/join
2. **PDF download failures?** → Retry logic or skip record?
3. **Thumbnail generation failures?** → Log and continue or fail the whole batch?
4. **File permissions in /tmp/?** → Handle gracefully
5. **Large batch sizes?** → Progress indicator every N records

## Deliverables

1. ✅ Completed `fix_broken_identifiers.rake` with all 6 tasks implemented
2. ✅ Completed `regenerate_thumbnails.rake` with all 3 tasks implemented
3. ✅ Both files tested locally with Test4tracy.csv data
4. ✅ Test results document with before/after screenshots if applicable
5. ✅ Any issues/edge cases found documented in `QWEN_TEST_RESULTS.md`

## References

- **Acda model:** `hydra/app/models/acda.rb`
- **ImportLibrary:** `hydra/lib/import_library.rb`
- **Sample CSV:** `data/imports/1_20260812203120/Test4tracy.csv`
- **Documentation:** `FIX_BROKEN_IDENTIFIERS.md`
- **Previous tasks for reference:** 
  - `hydra/lib/tasks/url_checks.rake` (similar structure)

---

**Next Step:** Once Qwen completes this, we'll integrate into CI/CD and test on production snapshot.
