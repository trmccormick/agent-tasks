---
status: completed
priority: CRITICAL
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
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

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvu-moonshot
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks/backlog/2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvu-moonshot/tasks/backlog/2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md \
         projects/wvu-moonshot/tasks/active/2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvu-moonshot/tasks -name "2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Fix CSV Loader Education Column Grouping for Foundation CSV Format
**Status**: BACKLOG
**Priority**: CRITICAL
**Type**: bug-fix
**Created**: 2026-09-14
**Last Updated**: 2026-09-14

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS — RSpec commands use `docker exec moonshot bundle exec rspec` format
- **MVP Alignment**: VALID — CSV Loader is the critical blocker for all Phase 2 features (Manual Entry, CSV Export, Test Coverage)
- **MVP Impact Note**: Blocks entire Phase 2: Manual Entry form, CSV Export, and Test Coverage tasks cannot proceed until education_history works
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Clear bug with concrete test validation — well-suited for local agent
**Local attempts before cloud**: N/A (first dispatch)
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/wvu-moonshot/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The WVU Moonshot app imports Foundation CSV files containing donor/graduate records. The `DonorCsvLoader` service handles the import, mapping CSV columns to model attributes and storing unknown columns in a JSONB `data` field.

Foundation CSV format repeats education columns (up to 3 degrees per person): each degree has 4 columns — `Institution Name`, `Class Of`, `Educational College Code`, `Educational Sub-Department Code`. The loader must group these into numbered jsonb keys (`education_1`, `education_2`, `education_3`).

This task fixes the column grouping logic so education data is correctly stored and queryable via `GraduateRecord#education_history`.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: CSV headers with duplicate names**
- ❌ Wrong: Using `row.each { |header, value| }` — Ruby's CSV library returns each header once, so repeating columns are lost
- ✅ Right: Use `headers = row.headers` first, then iterate `headers.each_with_index { |header, col_index| value = row[col_index] }` to access duplicate column names by position
- Why: Foundation CSV has the same column name repeated (e.g., "Institution Name" appears 3 times). The `row.each` enumerator deduplicates headers — you must use index-based access instead.

⚠️ **GOTCHA 2: Education instance tracking**
- ❌ Wrong: Tracking instances by comparing column indices with gaps (`col_index > last_education_index + 4`)
- ✅ Right: Track instances by counting occurrences of each education column name — every time you see "Institution Name" for the first time, it's education_1; second time is education_2, etc.
- Why: The index-gap approach fails when non-education columns appear between education blocks or when the CSV structure varies slightly.

⚠️ **GOTCHA 3: Test data uses Foundation format headers**
- ❌ Wrong: Assuming standard column names like "Education" or "Degree"
- ✅ Right: Use exact Foundation CSV header names: `"Institution Name"`, `"Class Of"`, `"Educational College Code"`, `"Educational Sub-Department Code"`
- Why: These are the literal headers in the Foundation CSV — case-sensitive, with spaces.

### Multi-Domain / Multi-Tenant Routing (if applicable)

N/A — single-domain Rails app.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Fix CSV Loader Education Column Grouping for Foundation CSV Format
**Status**: [backlog → active → completed]
**Date**: 2026-09-14

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/donor_csv_loader.rb` | Fix map_row() education column grouping | not started |
| `spec/services/donor_csv_loader_spec.rb` | Test validation (no changes needed) | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ Using `row.each` for duplicate headers — instead ✅ use `headers.each_with_index` with index-based access
- ❌ Tracking education instances by column index gaps — instead ✅ count occurrences of each education column name
- ❌ Assuming standard column names — instead ✅ use exact Foundation CSV header names

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The education column grouping logic in `DonorCsvLoader.map_row()` does not correctly store repeating Foundation CSV education columns into numbered jsonb keys (`education_1`, `education_2`, `education_3`).

**Error output**:
```
Failures:

  1) DonorCsvLoader Foundation CSV import with is_alumni and education_history parses education_history from repeating education columns
     Failure/Error: expect(abbott.education_history).to eq(["Arts & Sciences (1997)"])
       expected: ["Arts & Sciences (1997)"]
            got: []

  2) DonorCsvLoader Foundation CSV import with is_alumni and education_history preserves raw education data in jsonb for audit trail
     Failure/Error: expect(abbott.data["education_1"]).to include(...)
       expected nil to include {...}, but it does not respond to `include?`
```

**Current behavior**: `abbott.data["education_1"]` returns `nil`. The education columns are either stored under the wrong key or overwritten because duplicate headers are deduplicated by Ruby's CSV enumerator.

**Expected behavior**: 
- `abbott.data["education_1"]` contains: `{"Institution Name" => "West Virginia University", "Class Of" => "1997", "Educational College Code" => "Arts & Sciences", "Educational Sub-Department Code" => "1449-History"}`
- `abbott.education_history` returns: `["Arts & Sciences (1997)"]`
- For multi-degree records (Brian Abe): `abe.data["education_1"]` and `abe.data["education_2"]` both populated

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/donor_csv_loader.rb` | Fix map_row() to correctly group repeating education columns | `map_row(row, headers)` line ~85-120 |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/services/donor_csv_loader_spec.rb` | Test validation — lines 132-175 contain the failing Foundation CSV tests |
| `app/models/graduate_record.rb` | Contains `education_history()` method that reads from data["education_N"] — verify it works after fix |
| `data/imports/donors.csv` | Real Foundation CSV sample data with repeating education columns |

### Migration (if needed)
- [x] No migration needed — is_alumni column already added via prior migration

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/wvu-moonshot/tasks/backlog/2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md \
       projects/wvu-moonshot/tasks/active/2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks \
     -name "2026-09-14-CRITICAL-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Debug: Add logging to map_row() to understand current behavior

Add temporary debug output to `DonorCsvLoader.map_row()` in [app/services/donor_csv_loader.rb](app/services/donor_csv_loader.rb):

```ruby
# At the top of map_row(), after the method signature:
puts "=== DEBUG map_row ==="
puts "Headers: #{headers.inspect}"
puts "Row data: #{row.to_h.inspect}"
```

Then inside the column processing loop, add:
```ruby
if EDUCATION_COLUMN_NAMES.include?(header)
  puts "EDU COL: col_index=#{col_index}, header=#{header}, instance=#{education_instance}"
end
```

### Step 2 — Run failing test with debug output

```bash
docker exec moonshot bundle exec rspec spec/services/donor_csv_loader_spec.rb:138 -v
```

Capture the console output. Look for:
- Which headers are detected as education columns?
- What is the `education_instance` value when each education column is encountered?
- Are the keys being stored in the correct `education_N` hash?

### Step 3 — Fix the education column grouping logic

Replace the current `map_row()` implementation with corrected logic:

**Current (broken) approach**: Uses index-gap comparison to detect new education instances.

**Corrected approach**: Count occurrences of each education column name across the row:

```ruby
# Add as a class constant near EDUCATION_COLUMN_NAMES:
EDUCATION_COL_COUNTS = {}.freeze  # Will be mutated per-row in map_row

# In map_row(), replace the education detection logic with:
education_col_counts = Hash.new(0)

headers.each_with_index do |header, col_index|
  value = row[col_index].to_s.strip
  
  if EDUCATION_COLUMN_NAMES.include?(header)
    education_col_counts[header] += 1
    
    # Determine which education instance this belongs to:
    # Count how many COMPLETE education blocks (4 columns) have been seen before this column
    edu_instance = 0
    EDUCATION_COLUMN_NAMES.each do |edu_col|
      if edu_col == header
        # This is the Nth occurrence of this column name — determine instance from previous columns
        break
      end
      # For columns that come before this one in the education block, use their counts
      prev_count = education_col_counts[edu_col] || 0
      edu_instance = [edu_instance, prev_count].max
    end
    edu_instance += 1  # Convert from 0-based to 1-based
    
    edu_key = "education_#{edu_instance}"
    attrs[:data][edu_key] ||= {}
    attrs[:data][edu_key][header] = value.presence
  else
    # ... existing non-education column handling ...
  end
end
```

**Alternative simpler approach** (if the above is too complex): Group by fixed column positions. If education columns start at index 4, then:
- Columns 4-7 → education_1
- Columns 8-11 → education_2  
- Columns 12-15 → education_3

Choose whichever approach you can verify works with the test data. Remove debug `puts` statements after verification.

### Step 4 — Verify all tests pass

```bash
docker exec moonshot bundle exec rspec spec/services/donor_csv_loader_spec.rb --format progress
```

**Expected result**: 
```
.........
9 examples, 0 failures
```

All 9 tests must pass:
- 6 existing CSV loader tests (unchanged)
- 3 new Foundation CSV tests (is_alumni conversion, education_history parsing, raw data preservation)

### Step 5 — Run full test suite to verify no regressions

```bash
docker exec moonshot bundle exec rspec --format progress
```

**Expected result**: All 41 existing tests pass (no regressions).

### Step 6 — Synthesis Report (before committing anything)

Create synthesis report at:
`/Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/2026-09-14-BUG-FIX-CSV-LOADER-EDUCATION-COLUMN-GROUPING.md`

```markdown
## SYNTHESIS REPORT

**Task**: Fix CSV Loader Education Column Grouping for Foundation CSV Format
**Status**: completed
**Date**: 2026-09-14

### What I Did
[Describe the fix applied to map_row()]

### Test Results
- CSV loader tests: X/9 passed
- Full test suite: X/41 passed (no regressions)

### Files Modified
| File | Change |
|---|---|
| `app/services/donor_csv_loader.rb` | [what changed] |

### Root Cause
[One paragraph explaining the bug]

### Risk Assessment
[Any shared code or edge cases that could be affected]

### Ready to Commit? — YES / NO
```

**Post synthesis report in chat before committing.**

---

## Acceptance Criteria

- [ ] All 9 CSV loader tests pass (`spec/services/donor_csv_loader_spec.rb`)
- [ ] `abbott.data["education_1"]` contains all 4 education fields (Institution Name, Class Of, Educational College Code, Educational Sub-Department Code)
- [ ] For multi-degree record (Brian Abe): both `abe.data["education_1"]` and `abe.data["education_2"]` are populated
- [ ] `abbott.education_history` returns `["Arts & Sciences (1997)"]`
- [ ] `abe.education_history` returns `["Business & Economics (2010)", "Engineering/Mineral Resources (2004)"]`
- [ ] All 41 existing RSpec tests continue passing (no regressions)
- [ ] Synthesis report saved to `/Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/`

---

## Dependencies and Blocking

**This task blocks**:
- `2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md` — Manual Entry form needs education_history to display degrees
- `2026-09-11-HIGH-FEATURE-CSV-EXPORT.md` — CSV Export needs education_history for export format
- `2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md` — Test Coverage task depends on CSV Loader being fully working

**Blocked by**: None (can proceed independently)
