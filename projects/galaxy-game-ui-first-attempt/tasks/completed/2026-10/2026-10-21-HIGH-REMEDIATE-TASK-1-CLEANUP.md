---
title: "Fix Task 1 (Setup) — Remove Duplicates & Contradictions"
status: active
priority: HIGH
created: 2026-10-21
resume: false
review_status: pending
---

# Remediate Task 1 File — Critical Issues

## Context

Claude's audit found Task 1 is corrupted with duplicate steps, mixed color schemes, and contradictions. This task fixes the task file itself (not the code—infrastructure is already fixed on main).

**Audit findings:**
- Steps 7–10 appear TWICE with different content
- Copy 1 uses `galaxy-*` colors; Copy 2 uses `eve-*` colors (need GALAXY ONLY)
- Layouts differ between copies; stray `'eve-line':` fragment orphaned after Step 10
- Acceptance contradiction: says "GET / redirects to /dashboard" but route renders directly (should be 200, not 302)
- Task text says "no auth/database" but app has authentication required for /admin

## Acceptance Criteria

- [ ] Duplicate Steps 7–10 removed (keep only GALAXY version with galaxy-* colors)
- [ ] Stray `'eve-line':` CSS fragment removed
- [ ] Acceptance item fixed: GET / returns 200 (renders dashboard), /admin returns 302 (requires login)
- [ ] Context updated: "Frontend-only prototype with authentication for admin panel"
- [ ] No `git add .` in steps (use explicit paths per GUARDRAILS)
- [ ] No commits in steps (move to Session Closeout per IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md)
- [ ] File validated against TASK_TEMPLATE.md structure
- [ ] Review gate: human approval before marking complete

## Steps

### Step 1: Open & Audit Current Task 1 File
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks -name "*2026-10-*SETUP*" -o -name "*2026-10-*Rails*"
cat [path to Task 1]
```
Confirm:
- Lines with "Steps 7–10" appear twice
- One block has `galaxy-*` colors, other has `eve-*` colors
- Stray CSS fragment after Step 10

### Step 2: Open task file in editor
Full path: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS-8-PROJECT-SETUP.md`

### Step 3: Remove Duplicate Steps 7–10
- Find first "Step 7:" block
- Find second "Step 7:" block
- DELETE the second occurrence (the one with `eve-*` colors)
- DELETE stray `'eve-line':` fragment
- Keep ONLY the first block (galaxy-dark, galaxy-panel, etc.)

### Step 4: Fix Acceptance Contradiction
Find acceptance criteria:
```
- [ ] GET / redirects to /dashboard and returns auth screen
```

Replace with:
```
- [ ] GET / returns 200 and renders dashboard directly (no redirect)
- [ ] GET /admin returns 302 redirect to login (authentication required)
- [ ] GET /wormholes returns 200 (public view)
```

### Step 5: Update Context Section
Find: "Frontend-only prototype, no persistence models. No authentication."

Replace with: "Frontend-only prototype, no persistence models. Authentication required only for /admin panel; public access for /dashboard and /wormholes."

### Step 6: Audit Process Steps
- Verify NO `git add .` anywhere (use explicit paths)
- Verify NO commit steps in Steps section (move to Session Closeout)
- Confirm all steps reference GALAXY (not EVE) resources/locations

### Step 7: Validate Against Template
Compare against [TASK_TEMPLATE.md](../../../../TASK_TEMPLATE.md):
- [ ] YAML section correct
- [ ] Context section clear
- [ ] Steps use imperative voice
- [ ] Acceptance criteria are testable
- [ ] No pre-checked boxes

### Step 8: Save & Prepare for Review
File path: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS-8-PROJECT-SETUP.md`

Stage:
```bash
git add "projects/galaxy-game-ui/tasks/completed/2026-10/2026-10-20-HIGH-FEATURE-RAILS-8-PROJECT-SETUP.md"
```

## Stop Condition

**DO NOT commit.** Create a handoff file with:
- Path to corrected task file
- Summary of edits made
- Confirmation: "Ready for human review and approval"

Wait for GO disposition before proceeding.

## Gotchas

- The task file is IN `completed/2026-10/` — you're editing an already-closed task to fix it
- Don't remove acceptance criteria; reword them to match actual behavior
- Galaxy Game uses GCC currency and Luna/Mars settlements — keep any remaining EVE references out
- If you find method names don't match MockDataHelper (e.g., `player_contracts` mentioned but doesn't exist), note it for follow-up task

## Notes

- Infrastructure fixes already committed to main (Zeitwerk, Tailwind v4, mock data)
- This task fixes the task FILE, not code
- Next: Design session defer, then rewrite Tasks 2–4 with correct scope
