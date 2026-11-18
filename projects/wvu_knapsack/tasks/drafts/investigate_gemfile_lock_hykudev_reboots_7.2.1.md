---
status: draft
resume: false
priority: HIGH
type: investigation
system_domain: DEPLOYMENT | OPERATIONS | OTHER
mvp_alignment: SPEC_HEALTH | OTHER
local_worker_safe: true
---

# Task: Investigate Why Gemfile.lock Preservation Prevents hykudev Reboots (Hyku 7.2.1)

**Project:** WVU Knapsack  
**Type:** Investigation / Root Cause Analysis  
**Priority:** High  
**Created:** 2026-11-17  
**Last Updated:** 2026-11-18  

---

## DRAFT STATUS NOTE

This task is in draft status and awaiting review approval. It has NOT yet been approved for dispatch.

**Current State:** In `tasks/drafts/` committed to git  
**Next Step:** Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backbox/` with `status: backlog`

---

## DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Investigation Agent**.

Project: WVU Knapsack
Task: <agent-tasks>/projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md
     If the source is not tracked, stage the source first:
       git add projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md \
              projects/wvu_knapsack/tasks/active/investigate_gemfile_lock_hykudev_reboots_7.2.1.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/wvu_knapsack/tasks/active/investigate_gemfile_lock_hykudev_reboots_7.2.1.md
       test ! -e projects/wvu_knapsack/tasks/backlog/[SUBFOLDER]/investigate_gemfile_lock_hykudev_reboots_7.2.1.md && test -f projects/wvu_knapsack/tasks/active/investigate_gemfile_lock_hykudev_reboots_7.2.1.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/wvu_knapsack/tasks -name "investigate_gemfile_lock_hykudev_reboots_7.2.1.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/wvu_knapsack/README.md
  3. <agent-tasks>/projects/wvu_knapsack/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/wvu_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-INVESTIGATION-GEMFILE-LOCK-REBOOTS.md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## Task Readiness Checklist (Human — before dispatching to implementation)

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**This task requires human (Tracy) authorization before dispatch.**

---

## Objective

Determine the specific gem conflicts that caused hykudev to reboot continuously during the Hyku 7.2.1 upgrade testing, and document why copying Gemfile.lock from production resolved the issue.

> **Version note:** This task targets Hyku 7.2.1 (not 7.2.0). Version 7.2.1 includes critical GA4 security fixes that 7.2.0 lacks. All references to "7.2.0" in prior drafts have been updated to 7.2.1.

## Context

During the 7.2.1 upgrade testing on hykudev:
- **Without prod's Gemfile.lock:** Container kept crashing/rebooting
- **After copying Gemfile.lock from prod:** Container stayed stable
- **Same submodule SHA** as production, but resolution differed

## Investigation Methodology (Revised per Review Finding F4)

The investigation MUST follow this structured approach. Vague "can explain" deliverables are replaced with specific, measurable outputs.

### Step 1: Lockfile Diff Analysis
```bash
# Compare fresh resolution against the backup lockfile
diff -u hyrax-webapp/Gemfile.lock.backup hyrax-webapp/Gemfile.lock | head -200
# Identify all gem version differences between fresh and backup
```
**Deliverable:** A list of every gem whose version differs between the two lockfiles.

### Step 2: Boot Log Capture
```bash
# Start containers and capture ALL output (stderr + stdout) for crash analysis
docker compose up --remove-orphans 2>&1 | tee /tmp/7.2.1_boot_crash.log
# Stop after the first crash/reboot cycle
docker compose down
```
**Deliverable:** Full boot log showing the exact error that triggers the reboot cycle.

### Step 3: Bounded Gem Bisect (Time-boxed to 4 hours max)
1. Restore the backup lockfile: `cp hyrax-webapp/Gemfile.lock.backup hyrax-webapp/Gemfile.lock`
2. Verify container boots successfully with backup lockfile
3. Change ONE suspect gem version at a time (from the diff in Step 1)
4. Rebuild and restart after each change; capture the crash log
5. Map each crash to the specific gem that caused it

**Stop condition:** Either all crashing gems are identified, or 4 hours elapse — whichever comes first. If time expires, report partial findings with the most confident mappings.

### Step 4: Root Cause Mapping
For each crashing gem, produce a row in the comparison table:

| Gem Name | Attempted Version (fresh resolve) | Production Version (backup lockfile) | Root Cause Category | Evidence (error message / line) |
|----------|-----------------------------------|--------------------------------------|---------------------|-------------------------------|
| [gem]    | [version]                         | [version]                            | [API change / dep conflict / other] | [specific error] |

## Specific Questions to Answer

1. **What exact gem versions conflict during fresh `bundle install` on 7.2.1?**
   - Use the lockfile diff (Step 1) to identify all version differences
   - Cross-reference with boot crash logs (Step 2) to find which ones cause failures

2. **Why do these conflicts cause container reboots?**
   - Which gems have breaking API changes?
   - Which initializers crash due to missing constants/methods?
   - Document the boot sequence failure from captured logs

3. **What's the root cause pattern?**
   - Is this a Hyku 7.2.1 issue (needs upstream PR)?
   - Is this an environmental divergence (local/VM resolution differs)?
   - Is this a knapsack-specific gem constraint issue?

> **Evidence-bound only:** Do not speculate about how production "avoided" the problem without concrete evidence from lockfile diffs or deployment logs.

## Deliverables

1. **Document: `gemfile_lock_hykudev_reboot_analysis.md`** (save to `/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/`)
   - Exact gem versions that conflict (from lockfile diff)
   - Specific error messages from captured boot logs
   - Which gems have breaking changes in 7.2.1
   - Root cause mapping table (see Step 4 above)

2. **Comparison Table:** (as specified in Step 4)

3. **Recommendation:**
   - Should Gemfile.lock be committed to track dependency state?
   - Should we pin specific critical gems in Gemfile?
   - Should we file upstream bug with Hyku/samvera?

## Submodule Modification Note (per Review Finding F3)

During this investigation, you will modify the `hyrax-webapp` submodule working tree:
- **Step 1:** You will compare against `hyrax-webapp/Gemfile.lock.backup` (a backup copy placed outside the submodule)
- **Step 3:** You may temporarily restore `Gemfile.lock` inside `hyrax-webapp/` during bisect testing

**CRITICAL:** These changes are intentional and temporary. They MUST NEVER be committed to the submodule. At the end of the investigation:
```bash
# Verify submodule working tree is clean before any commit
git -C hyrax-webapp status
# Must show no uncommitted changes in hyrax-webapp/
```

## Success Criteria (Measurable)

✅ **Task passes when:**
1. Lockfile diff identifies all gem version differences between fresh and backup
2. Boot crash log captures the exact error triggering the reboot cycle
3. Gem bisect maps each crashing gem to a specific root cause category
4. Comparison table is complete with at least one row (even if only one gem is identified)
5. Investigation completes within 4 hours or stops with documented partial findings

❌ **Task fails when:**
1. No crash can be reproduced despite multiple attempts
2. Time-box expires without any gem-to-crash mapping established

## Dependencies and Prerequisites

- **Prerequisite:** Human (Tracy) authorization before dispatch
- **Related documentation:**
  - [gemfile_lock_hykudev_reboot_investigation.md](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gemfile_lock_hykudev_reboot_investigation.md) — Initial analysis (commit c8ccf24 shows NameError crash example)
  - [gemfile_lock_root_cause.md](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gemfile_lock_root_cause.md) — Docker build sequence explanation
  - [Gem Version Drift](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gem_version_drift.md) — Why versions diverge
- **7.2.1 branch:** `feature/hyku-v7.2.1-upgrade` (recommended over 7.2.0)

## Resources

- **Backup Gemfile.lock:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/Gemfile.lock.backup` (checksum: `7b4ed53d782f8f0103bcd2537d0d6052`)
- **hykudev access:** Available for testing (coordinate with team for VM access)

## Notes

- Time-box: 4 hours maximum. Report partial findings if time expires.
- Do not speculate about production behavior without evidence from lockfile diffs or deployment logs.
- Document everything — even partial findings help narrow down the issue.
- If you find this is an upstream (Hyku) bug, we may need to file a samvera issue.
