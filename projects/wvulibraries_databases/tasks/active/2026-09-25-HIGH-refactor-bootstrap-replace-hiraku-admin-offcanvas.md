---
status: active
priority: HIGH
type: refactor
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## CRITICAL: Task Readiness Checklist (Human — before dispatching)

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

## Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvulibraries_databases
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks/active/2026-09-25-HIGH-refactor-bootstrap-replace-hiraku-admin-offcanvas.md

STEP 0 — Task is already in active/. Verify with:
  find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks -name "*bootstrap-replace-hiraku*"
  Expected: exactly one result at the active/ path.
  Paste the output in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Replace Hiraku Admin Offcanvas with Bootstrap 5 Native Offcanvas
**Status**: ACTIVE  
**Priority**: HIGH  
**Type**: refactor  
**Created**: 2026-09-25  
**Last Updated**: 2026-10-02  

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)  
**Why This Agent**: Requires Rails 7 / Bootstrap 5 implementation and full codebase changes; local Qwen has terminal access  
**Local attempts before cloud**: N/A  
**Supervision Level**: standard  

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/README.md`
3. **Prior findings**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/2026-09-25-ADMIN-OFFCANVAS-FINDINGS.md`
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report must be saved BEFORE starting work.

---

## Context

The admin offcanvas navigation currently uses Hiraku.js (^2.1.8). After the Rails 7 / Turbo migration, Hiraku initialization and the custom overlay are unreliable (incomplete viewport coverage, broken click handling on Turbo navigations). Bootstrap 5.3 is already a dependency of the project. Replacing Hiraku with Bootstrap’s native offcanvas removes the legacy dependency and the need for custom overlay workarounds.

**Relevant Architecture Docs** — read before starting:
- agent-tasks/README.md — EXECUTOR role and synthesis-gated workflow
- projects/wvulibraries_databases/README.md — project context
- projects/wvulibraries_databases/summaries/2026-09-25-ADMIN-OFFCANVAS-FINDINGS.md — prior root-cause analysis
- projects/wvulibraries_databases/summaries/2026-10-02-GROK-REVIEW-OVERLAY-VIEWPORT-FIX.md — latest findings on why documentElement portal still fails

---

## Critical Information for This Task

### Credentials
Not needed — all work is local development / Docker.

### Architecture Gotchas (Critical to understand BEFORE starting)

**GOTCHA 1**: Bootstrap Offcanvas Requires Specific HTML Structure  
- Wrong: Only adding data-bs-toggle="offcanvas" to existing Hiraku markup  
- Right: Use .offcanvas, .offcanvas-header, .offcanvas-body, and offcanvas-end  
- Why: Bootstrap JS looks for these exact classes/attributes to initialize  

**GOTCHA 2**: Absolute paths use `/Users/tam0013/Documents/git/` as root — the Rails app lives at `databases/databases/app/...`
- ❌ Wrong: `cd /Users/tam0013/Documents/git && grep -r ... databases/app/...` (shallow path)
- ✅ Right: `cd /Users/tam0013/Documents/git && grep -r ... databases/databases/app/...` (nested path)
- Why: The outer `databases/` is the git repo, the inner `databases/` contains the actual Rails app. Table paths start with `databases/databases/app/...` and resolve from this root.  

**GOTCHA 3**: Remove All Hiraku Artifacts  
- Wrong: Leaving Hiraku require/import/package entries  
- Right: Remove from interface.js, main.scss, and package.json, then yarn install  
- Why: Leftover CSS/JS can conflict with Bootstrap offcanvas  

**GOTCHA 4**: Dark Theme Must Be Explicitly Preserved  
- Wrong: Assuming default Bootstrap offcanvas styling is acceptable  
- Right: Override background, text color, close-button filter, and list hover states  
- Why: Original design is dark; Bootstrap defaults are light  

---

## REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and save a synthesis report.

**Save to**: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/YYYY-MM-DD-SYNTHESIS-bootstrap-replace-hiraku.md

**Template** (copy/paste ready):

## STATUS SYNTHESIS REPORT

**Task**: Replace Hiraku Admin Offcanvas with Bootstrap 5 Native Offcanvas
**Status**: active
**Date**: YYYY-MM-DD

### What I'm About to Do
Replace Hiraku-based admin offcanvas with Bootstrap 5.3 native offcanvas, preserving dark theme, right-side slide-in, all existing links, and full-viewport backdrop. Remove all Hiraku dependencies afterward.

### Files I'll Reference
| File | Purpose | Status |
|------|---------|--------|
| databases/databases/app/views/admin/_navigation.html.erb | Nav markup | not started |
| databases/databases/app/views/layouts/admin.html.erb | Menu button | not started |
| databases/databases/app/assets/stylesheets/interface/elements/_nav.scss | Dark theme overrides | not started |
| databases/databases/app/assets/javascripts/interface.js | Remove hiraku require | not started |
| databases/databases/app/assets/stylesheets/interface/main.scss | Remove hiraku import | not started |
| databases/databases/package.json | Remove hiraku dependency | not started |
| databases/databases/app/assets/javascripts/plugins/off_canvas.js | Delete or empty | not started |

### Prerequisites Completed
- Task is already in active/
- Read README.md EXECUTOR section
- Read project README
- Read prior offcanvas findings summaries
- Understand architecture gotchas
- Confirm container is running and assets can be precompiled

### Expected Outcomes
Menu button opens a right-side Bootstrap offcanvas with full-viewport dark backdrop; close works; all links work; Hiraku fully removed; no console errors; visual parity with original production menu.

### Critical Gotchas I Will Avoid
- Only adding data-bs attributes to old Hiraku markup — instead use full Bootstrap offcanvas HTML structure
- Leaving Hiraku require/import/package entries — instead remove all three and yarn install
- Assuming default Bootstrap light styling is fine — instead use explicit dark-theme overrides
- Editing shallow databases/app/... paths — instead use nested databases/databases/app/...

**SYNTHESIS COMPLETE.** Ready to proceed after approval.

---

## Problem Statement

Admin offcanvas currently depends on Hiraku.js. After the Rails 7 / Turbo migration, initialization and custom overlay coverage are unreliable. Overlay does not cover the full viewport; prior JS compensation attempts failed because the overlay was still inside the transformed body context.

**Current behavior**: Menu can open/close in some cases, but backdrop coverage is incomplete (~60%), and initialization is fragile under Turbo.  
**Expected behavior**: Bootstrap-native right-side offcanvas with full-viewport backdrop, original dark styling, and no Hiraku dependency.

---

## Files Involved

**Path Root**: `/Users/tam0013/Documents/git/` (base for all paths in this file)

### Primary Files — you will edit these
| File | Purpose |
|------|---------|
| databases/databases/app/views/admin/_navigation.html.erb | Nav markup → Bootstrap offcanvas |
| databases/databases/app/views/layouts/admin.html.erb | Menu button → Bootstrap trigger |
| databases/databases/app/assets/stylesheets/interface/elements/_nav.scss | Dark theme overrides |
| databases/databases/app/assets/javascripts/interface.js | Remove hiraku require |
| databases/databases/app/assets/stylesheets/interface/main.scss | Remove hiraku import |
| databases/databases/package.json | Remove hiraku dependency |
| databases/databases/app/assets/javascripts/plugins/off_canvas.js | Delete or empty |

### Reference Files — read but do not edit
| File | Why |
|------|-----|
| Production admin page behavior / screenshots | Visual parity target |
| projects/wvulibraries_databases/summaries/2026-10-02-GROK-REVIEW-OVERLAY-VIEWPORT-FIX.md | Prior findings and failed approaches |

### Migration
- No migration needed

---

## Implementation Steps

Complete and save synthesis report BEFORE Step 1.

### Step 0 — Verify task location
find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/tasks -name "*bootstrap-replace-hiraku*"
Expected: exactly one file under tasks/active/. Paste output before proceeding.

### Step 1 — Audit remaining Hiraku usage
cd /Users/tam0013/Documents/git/databases
grep -ri "hiraku\|Hiraku" --include="*.js" --include="*.scss" --include="*.css" --include="*.erb" --include="*.html" --include="package.json" .
Paste full output. List every file that must change. All table paths in this task use the stated root: `/Users/tam0013/Documents/git/` — each path starts with `databases/databases/app/...` and resolves from there.

### Step 2 — Rewrite _navigation.html.erb to Bootstrap offcanvas
Use Bootstrap structure:
- outer div: class="offcanvas offcanvas-end" id="offCanvas"
- offcanvas-header with title MENU and btn-close btn-close-white
- offcanvas-body containing the existing nav links/sections

Keep all existing link targets and labels.

### Step 3 — Update Menu button in admin.html.erb
Use:
button with data-bs-toggle="offcanvas" data-bs-target="#offCanvas" aria-controls="offCanvas"

### Step 4 — Apply dark-theme SCSS overrides
Ensure visual parity with original production menu (dark background, light text, dashed dividers, uppercase titles, white close button).

### Step 5 — Remove all Hiraku artifacts
- interface.js: remove hiraku require
- main.scss: remove hiraku import
- package.json: remove hiraku entry
- Then run `yarn install` inside the container at `/home/databases` (the app workdir):
  ```bash
  docker exec databases yarn install --cwd /home/databases
  ```
- off_canvas.js: delete or leave as no-op comment

### Step 6 — Precompile and restart
```bash
docker exec databases bundle exec rake assets:precompile
docker restart databases
```

### Step 7 — Browser verification
1. Open admin page
2. Hard refresh
3. Confirm menu open/close, full-viewport backdrop, links, no console errors
4. Confirm no Hiraku references remain

---

## Acceptance Criteria

- Right-side Bootstrap offcanvas opens from Menu button
- Nav hidden by default
- Full viewport backdrop (no light gaps)
- Close control works
- All original links present and working
- Dark theme parity with production
- No JS console errors
- grep -ri hiraku shows no remaining functional references
- Verified locally and/or on dev VM

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in files outside the Primary Files list above
- Same failure persists after two attempts
- Asset compilation fails with a dependency change that can't be resolved locally
- Any architectural decision about Bootstrap component structure is required

---

## Commit Instructions

Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvulibraries_databases/tasks/active/[FILENAME] \
       projects/wvulibraries_databases/tasks/completed/[YYYY-MM]/[FILENAME]

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/wvulibraries_databases/tasks/active/[FILENAME] \
   projects/wvulibraries_databases/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/wvulibraries_databases/tasks/completed/[YYYY-MM]/[FILENAME]

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation

- [ ] No doc changes needed

---

## Dependencies

**Blocked by**: none (prior Option A Hiraku fix is superseded; do not wait on it)

**Blocks**: none

**Related tasks**:
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/2026-09-25-ADMIN-OFFCANVAS-FINDINGS.md`
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/summaries/2026-10-02-GROK-REVIEW-OVERLAY-VIEWPORT-FIX.md`

---

## Completion Report

*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary

*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]  

---

## Implementation Notes

1. Bootstrap 5 offcanvas docs: https://getbootstrap.com/docs/5.3/components/offcanvas/
2. Use offcanvas-end for right-side panel
3. Prefer btn-close-white (or filter: invert(1)) for visibility on dark background
4. After removing Hiraku from package.json, run yarn install in the app workdir inside the container
5. Do not batch all changes without intermediate browser checks
6. Decision 2026-10-02: stop patching Hiraku overlay; replace with Bootstrap native offcanvas while preserving original look and behavior

---