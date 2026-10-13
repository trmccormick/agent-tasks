---
status: completed
resume: false
priority: HIGH
type: bug-fix
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
completed_date: 2026-10-13
notes: "Fix verified locally. Ready for hykudev VM testing. See handoff: 2026-10-13-HYKU-7.2.0-NAMEERROR-FIX-LOCAL-VERIFIED.md"
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project]`, `[SUBFOLDER]` and `[FILENAME]` as usual. Change nothing else.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/[YYYY-MM-DD]-[PRIORITY]-TYPE-[DESCRIPTIVE-NAME].md
Synthesis Report Required Before Implementation (brief): "Read this task. Summarize your plan in 2 lines and post to chat. Wait for approval."
After approval: implement the task exactly as specified, test locally, report results back to human via chat before committing any changes.

## Instructions for This Session

- Step 0: Move this task file from tasks/backlog/ to tasks/active/. Update its frontmatter status from "backlog" to "in-progress".
- Read the full task file below (especially Implementation Steps, Architecture Gotchas, Acceptance Criteria).
- Do NOT proceed past the synthesis report until approved by human.
```

---

## 2026-10-13-HIGH-BUG-FIX-HYKU-7.2.0-WEB-CONTAINER-CRASH

**Source:** Local build failure (`sh down.sc.local.sh`), hykudev VM crash loop  
**Branch:** `feature/hyku-v7.2.0-upgrade` (not main)  
**Affected Environments:** local sc, hykudev lib.wvu.edu  
**Blocked by:** None  

---

### Problem Statement

Web container fails to boot with a crash during Rails initializer load:

```
NameError: uninitialized constant Hyku::ORIGINAL_VERSION
/app/samvera/config/initializers/zzz_temporary_hyku_version_override.rb:26
bundler: failed to load command: puma (/usr/local/bundle/bin/puma)
```

This prevents both local build and hykudev test VM from starting. The hykudev VM was previously shut down by devops due to container restart loops caused by this same error.

### Root Cause Analysis

File: `config/initializers/zzz_temporary_hyku_version_override.rb` (in knapsack repo)

The initializer has two execution phases:
1. **Lines 13-24**: A conditional block that checks `if defined?(Hyku) && Hyku.const_defined?(:VERSION)` and sets `ORIGINAL_VERSION = VERSION`, then overrides to `'7.2.0'`
2. **Line 26 (CRASH)**: Unconditionally reads `Hyku::ORIGINAL_VERSION` in a log message

**The crash happens because:** In Hyku 7.2.0, the `Hyku` module or `VERSION` constant may not be loaded by the time this initializer runs. The conditional on line 13 fails silently (never enters the block), so `ORIGINAL_VERSION` is never defined. Line 26 then tries to read it and raises `NameError`.

**This is not a timing/load-order bug.** Even when the conditional succeeds, `ORIGINAL_VERSION` is only set inside the conditional — but line 26 is OUTSIDE the conditional, running unconditionally regardless of whether the conditional block executed.

### Implementation Steps

1. **Read** `config/initializers/zzz_temporary_hyku_version_override.rb` in knapsack repo
2. **Fix line 26** to guard against missing `ORIGINAL_VERSION`. Choose one approach:

   **Option A — Safe access with `defined?`:**
   ```ruby
   original = defined?(Hyku::ORIGINAL_VERSION) ? Hyku::ORIGINAL_VERSION : 'N/A'
   current  = defined?(Hyku::VERSION)       ? Hyku::VERSION       : 'N/A'
   Rails.logger.info "[WVU Knapsack] Temporarily overriding Hyku VERSION from '#{original}' to '#{current}' (will be removed when samvera/hyku#3397 is resolved)"
   ```

   **Option B — Rescue wrapper:**
   ```ruby
   begin
     Rails.logger.info "[WVU Knapsack] Temporarily overriding Hyku VERSION from '#{Hyku::ORIGINAL_VERSION}' to '#{Hyku::VERSION}' (will be removed when samvera/hyku#3397 is resolved)"
   rescue NameError => e
     Rails.logger.warn "[WVU Knapsack] VERSION override check unavailable: #{e.message}"
   end
   ```

3. **Commit** the change to `feature/hyku-v7.2.0-upgrade` branch (only knapsack repo files — do NOT modify hyrax-webapp submodule)
4. **Push** to remote if needed

### Architecture Gotchas

- **File exists on disk but is untracked** in the feature branch — it was never committed (`git show feature/hyku-v7.2.0-upgrade:config/initializers/zzz_temporary_hyku_version_override.rb` returns error). Verify with `git status` before committing.
- **Only modify knapsack files.** The hyrax-webapp submodule is read-only for this project. Any Hyku changes that need to be committed must go through upstream PRs, not local submodule edits.
- **The initializer uses `zzz_` prefix** which means Rails loads it last among initializers — ensuring it runs after Hyku module is loaded (but as shown, that's not guaranteed in 7.2.0).
- **This is a TEMPORARY file.** Per its own comments: "DO NOT commit this as a permanent solution." Once samvera/hyku#3397 merges and submodule is updated, this file should be deleted.

### Acceptance Criteria

- [ ] `sh down.sc.local.sh` completes without NameError crash
- [ ] Local web container stays running (Puma starts successfully)
- [ ] No new errors in Rails boot log related to VERSION override
- [ ] Hyku::VERSION is still correctly overridden to 7.2.0 (verified via logs or `Rails.logger`)
- [ ] The fix works on both local and hykudev environments

### Dependencies & Blocks

- **Blocked by:** None — this is the critical blocker for HYKU-720 progress
- **Blocks:** All subsequent Hyku 7.2.0 upgrade testing (local, dev VM, staging)
- **Related:** hykudev VM incident (`tasks/incident/HYKU-720-HYKudev-REBOOT-LOOP.md`)

### Session Notes

- The knapsack puma.rb was tested locally and works fine — not the cause of this crash
- The hyrax-webapp submodule points to `v7.2-stable` (v7.2.0 tag) on `feature/hyku-v7.2.0-upgrade`
- This file is a workaround for samvera/hyku#3397 (version not yet bumped in 7.2-stable branch)
