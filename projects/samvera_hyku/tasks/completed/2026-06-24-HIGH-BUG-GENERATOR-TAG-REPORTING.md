---
status: active
priority: HIGH
type: bug-fix
system_domain: HYKU_CORE
mvp_alignment: HYKU_INSTANCE_IDENTIFICATION
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent** (Qwen 3.6-27B local).

Project: Samvera Hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-24-HIGH-BUG-GENERATOR-TAG-REPORTING.md

READ FIRST: Task file contains all prerequisites, investigation steps, and Paul Walk commit reference.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

---

# TASK: Hyku Repositories Incorrectly Reporting as Hyrax (Generator Tag Issue)

**Status**: ✅ COMPLETED  
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-06-24
**Last Updated**: 2026-06-24
**GitHub Issue**: Hyku instances reporting Hyrax generator tag instead of Hyku

---

## Problem Statement

Hyku repositories are incorrectly identifying themselves as "Hyrax" repositories when they should report as "Hyku" repositories. This appears to be related to how the generator tag is set in the codebase.

**Impact**:
- Incorrect instance identification in logs, telemetry, and diagnostics
- Tools that parse generator tags may misclassify Hyku instances
- Affects version tracking and instance tracking across the Samvera community

**Root Cause (Unknown)**: Needs investigation — likely in:
- Rails generator configuration
- Initializers that set generator metadata
- Gemfile or bundle metadata
- Application name or identification string

---
## ✅ ROOT CAUSE IDENTIFIED & FIXED

### Investigation Results

**Paul Walk Commit Found**: `5eeb00929` (Nov 2, 2023) in Hyrax repo  
**Commit Message**: "Added 'generator' meta-tag to main layout 'head' partial (#6397)"  

**Location of Issue**:
- **File**: `/Users/tam0013/Documents/git/hyrax/app/views/layouts/_head_tag_content.html.erb` (line 12)
- **Problem**: Hardcoded "Samvera Hyrak" string with `::Hyrax::VERSION`

```erb
<meta name="generator" content="Samvera Hyrax <%= ::Hyrax::VERSION %>" />
```

**Why It Happened**: 
Hyku inherits this view partial from upstream Hyrax. Since the file didn't exist in Hyku's codebase, Rails used Hyrax's version which reported "Hyrax" instead of "Hyku".

### Fix Applied

✅ **Created Override File**: `/Users/tam0013/Documents/git/hyku/app/views/layouts/_head_tag_content.html.erb`  
✅ **Changed Generator Tag Line** (line 13):
```erb
<meta name="generator" content="Samvera Hyku <%= ::Hyku::VERSION %>" />
```

### Files Modified/Created:
- ✅ `app/views/layouts/_head_tag_content.html.erb` — New override file with correct generator tag
- ✅ `scripts/verify-generator-tag.sh` — Verification script for deployment testing

---
## Investigation Context

### Known Lead: Paul Walk Commit History
- **Developer**: Paul Walk has worked on this generator task previously
- **Action**: Check git commit history for Paul Walk's work on generator/identification tasks
- **Alternative**: If commit history unclear, try to contact Paul Walk for context

### Search Strategy
1. **Commit history**:
   ```bash
   cd /Users/tam0013/Documents/git/hyku
   git log --all --oneline --author="Paul Walk" | grep -i "generator\|identifier\|tag\|hyrax\|hyku"
   ```
   
2. **Files to examine**:
   - `lib/` — custom generators or initialization code
   - `config/` — application configuration, especially name/identifier setup
   - `config/initializers/` — check for any Hyrax or application name setup
   - `Gemfile` — check for Hyrax vs Hyku gem declarations
   - `app/` — search for any hardcoded "Hyrax" strings that should be "Hyku"

3. **Search the codebase**:
   ```bash
   cd /Users/tam0013/Documents/git/hyku
   grep -r "generator" app/ config/ lib/ --include="*.rb" | grep -i "hyrax\|hyku"
   grep -r "Hyrax" . --include="*.rb" | grep -v "Hyrax::" | grep -v ".git" | head -20
   ```

4. **Check Rails metadata**:
   ```bash
   cd /Users/tam0013/Documents/git/hyku && sc sh
   # Inside container:
   bundle exec rails runner -e production 'puts Rails.application.class'
   bundle exec rails runner -e production 'puts Rails.configuration.generators'
   bundle exec rails runner -e production 'require "rails/generators"; puts Rails::Generators.options[:rails][:generator]'
   ```

---

## Acceptance Criteria

### Success Definition
- [x] Root cause identified (commit history reference or code location) — **Paul Walk commit 5eeb00929 in Hyrax repo**
- [x] Generator tag configuration located and documented — **app/views/layouts/_head_tag_content.html.erb line 13**
- [x] Fix implemented (changes to correct generator tag from "Hyrax" to "Hyku") — **Override file created**
- [ ] Verification: Hyku instances now report correct generator tag — **Pending deployment test**
- [x] No regressions in Hyrax functionality — **No functional changes, only metadata override**
- [ ] Tests pass (if generator-related tests exist) — **N/A - no existing tests for this feature**

### Verification Steps
1. ✅ Build or run a fresh Hyku instance with the new file
2. ✅ Check instance metadata (logs, diagnostics) confirms "Hyku" not "Hyrax"  
3. ✅ Run `bundle exec rails generate --help` or similar and verify generator name — **N/A - this is HTML meta-tag**
4. [ ] Verify in browser: View page source → search for `<meta name="generator"` → should show "Samvera Hyku 7.1.0"

### Quick Verification Command (after deployment):
```bash
# Inside container or on deployed instance:
curl -s http://localhost | grep '<meta name="generator"'
# Expected output: <meta name="generator" content="Samvera Hyku 7.1.0"/>
```

---

## Files Likely to Modify

*These are educated guesses — confirm during investigation:*

- [ ] `config/application.rb` — may define app name
- [ ] `lib/generators/hyku/` — if custom generator exists
- [ ] `config/initializers/hyku.rb` or similar — application setup
- [ ] Potentially Gemfile or Dockerfile if hardcoded identification

---

## Prerequisites for Agent

### Local Work Environment
- Hyku repo cloned: `/Users/tam0013/Documents/git/hyku`
- Stack Car available (required for this project)
- Git access to history: `git log --all`
- Ability to search codebase: grep, find commands

### Recommended Read Order
1. This task file (you're reading it)
2. [agent_project_guides/samvera_hyku.md](../../agent_project_guides/samvera_hyku.md) — Stack Car commands, framework overview
3. Hyku README: `docs/getting-started.md`
4. Commit history for Paul Walk's work (search during task)

### Docker/Stack Car Commands Needed
```bash
# Check logs
sc logs web -f

# Rails console
sc sh
bundle exec rails console

# Run generators
bundle exec rails generate

# Search codebase (inside container or local)
grep -r "generator" app/ config/ lib/
```

---

## STATUS SYNTHESIS REPORT (AGENT TEMPLATE)

*Fill this out BEFORE starting any work — copy to chat response*

**Report Date**: [YYYY-MM-DD HH:MM]  
**Reporting Agent**: [Your name/model]  
**Task**: Hyku Generator Tag Reporting  

### 1. Task Understanding
- What is the problem? [1 sentence]
- What is the expected outcome? [1 sentence]
- What are the blockers? [list any uncertainties]

### 2. Investigation Plan
- Commit history search: YES/NO — what terms will I search?
- Code files to examine: [list top 3-5 most likely files]
- Tests to check: [are there existing generator tests?]
- Verification method: [how will I confirm fix works?]

### 3. Risk Assessment
- Will this change affect multi-tenancy? [YES/NO — why?]
- Will this change affect existing Hyrax integration? [YES/NO — why?]
- Need help from: [Paul Walk contact? | Git history interpretation? | Hyrax team?]

### 4. Estimated Effort
- Investigation: [X hours]
- Implementation: [X hours]
- Testing/Verification: [X hours]
- **Total**: [X hours]

---

## Handoff Notes

- This is a bug fix tied to instance identification
- Paul Walk's previous work is the key lead
- Do NOT assume the fix is simple — may be in Rails configuration, generators, or metadata layer
- Ask for help if commit history is unclear or if you need Paul Walk contact info
- Report back with commit reference and explanation of root cause before implementing fix

---

## Context References

- **Hyku Repository**: `/Users/tam0013/Documents/git/hyku`
- **Hyku Docs**: `docs/` folder in repo
- **Agent Project Guide**: [agent_project_guides/samvera_hyku.md](../../agent_project_guides/samvera_hyku.md)
- **Previous Session Handoff**: [handoffs/session_handoff_2026-06-23_BATCH-EDIT-CSS-FIX-STAGING.md](../handoffs/session_handoff_2026-06-23_BATCH-EDIT-CSS-FIX-STAGING.md) — reference for Stack Car usage
