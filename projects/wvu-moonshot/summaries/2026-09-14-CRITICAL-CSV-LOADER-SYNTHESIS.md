# STATUS SYNTHESIS REPORT

**Task**: CSV Loader: Parse is_alumni and Education History
**Status**: backlog → active
**Date**: 2026-09-14

### What I'm About to Do
1. Create migration adding `is_alumni :boolean, default: false` column to `graduate_records` table
2. Update DonorCsvLoader to extract PRIMARY CONSTITUENCY from CSV and convert "Alumni" → true, everything else → false
3. Add `education_history` method to GraduateRecord model that returns condensed format array (e.g., ["Business & Economics (2010)", "Engineering/Mineral Resources (2004)"]) from repeating education_1/2/3 jsonb keys
4. Run migration and verify schema change
5. Add RSpec tests for is_alumni conversion and education_history parsing with real Foundation CSV data
6. Verify all existing tests still pass

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `wvu-moonshot/db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb` | Add is_alumni column to schema | new file (via rails generate) |
| `wvu-moonshot/app/services/donor_csv_loader.rb` | Add is_alumni conversion logic | modify |
| `wvu-moonshot/app/models/graduate_record.rb` | Add education_history method | modify |
| `wvu-moonshot/spec/services/donor_csv_loader_spec.rb` | Add tests for is_alumni + education | modify |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ (verified single copy)
- ✅ YAML status updated from backlog → active
- ✅ Read task file in full
- ✅ Understood all 4 architecture gotchas
- ✅ Reviewed DonorCsvLoader code — COLUMN_MAP handles known columns, rest goes to jsonb data
- ✅ Reviewed GraduateRecord model — has convenience methods (college_code, class_year) that dig into data
- ✅ Reviewed test suite — 6 existing tests covering create/update/skip/missing-file scenarios
- ✅ Reviewed real Foundation CSV — PRIMARY CONSTITUENCY is first column; education columns repeat in groups of 3

### Key Design Decisions Based on Code Review
1. **is_alumni conversion**: PRIMARY CONSTITUENCY is stored in jsonb data (not mapped by COLUMN_MAP). Need to add it to COLUMN_MAP or handle specially in map_row. Since it's a model attribute, I'll add it to COLUMN_MAP mapping to :is_alumni and convert the string value.
2. **education_history**: The real CSV has repeating columns (INSTITUTIONNAME, CLASSOF, EDUCATIONALCOLLEGECODE) that get stored as flat keys in jsonb. Need to detect which education block each key belongs to by column position — columns 10-12 = edu_1, 13-15 = edu_2, 16-18 = edu_3 (0-indexed: 9-11, 12-14, 15-17).
3. **Migration**: Standard Rails migration with `add_column :graduate_records, :is_alumni, :boolean, default: false`

### Expected Outcomes
Migration runs cleanly. is_alumni column added to database. DonorCsvLoader extracts PRIMARY CONSTITUENCY and converts "Alumni" → true during import. GraduateRecord#education_history returns array of strings in format "College Name (Year)". All existing RSpec tests still pass. New tests verify is_alumni conversion and education_history parsing with real Foundation data.

### Critical Gotchas I Will Avoid
- ❌ Storing is_alumni only in jsonb — instead ✅ Create migration for database column
- ❌ Hard-coding "PRIMARY CONSTITUENCY" column name — instead ✅ Use case-insensitive COLUMN_MAP lookup
- ❌ Assuming exactly 3 education blocks in specific positions — instead ✅ Loop through education_1/2/3 keys and skip blanks
- ❌ Discarding raw education data — instead ✅ Preserve in jsonb while providing condensed accessor

---

**SYNTHESIS COMPLETE.** Ready to proceed with implementation.
