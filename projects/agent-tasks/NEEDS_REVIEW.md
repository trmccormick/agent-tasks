# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Implementation agents: add an entry here instead of just noting something in status.md when an escalation trigger below fires. Remove/archive entries once resolved — this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- A workflow procedure produces consistent friction across 3+ sessions (e.g., handoff file proliferation)
- A tool or pattern is working well but needs documentation for future adoption
- A session discovers a gap in guidance that would help future sessions avoid debugging
- Two projects use contradictory patterns for the same task type (standardization needed)
- A procedure worked well — add an entry to recommend it for adoption elsewhere

---

## Entry template

### [DATE] — [topic]
**What happened**: 
**Observed in**: [project/session]
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-10-13 — Handoff file lifecycle undefined
**What happened**: During Hyku 7.2.0 upgrade session, three intermediate handoff files accumulated (`2026-10-13-HYKU-7.2.0-*` variations). No guidance on consolidation, archiving, or which one is "the real" handoff for next session.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: SESSION_CLOSEOUT.md (no consolidation guidance); SESSION_GUIDANCE.md (no handoff lifecycle); galaxy_game NEEDS_REVIEW.md (no equivalent friction reported)
**What needs a second opinion**: (1) Should SESSION_CLOSEOUT.md add a "Handoff Consolidation" step? (2) Should handoff template have a "NEXT SESSION" section? (3) Should old intermediate handoffs be archived or deleted (and when)?
**Priority**: MEDIUM — affects all future multi-session projects
**Status**: OPEN

### 2026-10-13 — SESSION_GUIDANCE checklists missing for routine tasks
**What happened**: Added "Hyku Submodule Upgrades (7.2.0+ specific)" architectural gotchas to SESSION_GUIDANCE, but no actionable checklist. Future sessions will repeat same debugging steps.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Current SESSION_GUIDANCE has "Critical Rules" + "Testing Constraints" + "Known Gotchas" sections (all prose, no checklists); galaxy_game PROJECT_GUIDANCE includes checklists for celestial body definition and contract creation
**What needs a second opinion**: Should SESSION_GUIDANCE include checklists for: (a) Hyku version upgrades, (b) Submodule updates, (c) Docker rebuild/testing workflow? Template: pre-upgrade checks, during-upgrade steps, post-upgrade validation.
**Priority**: MEDIUM — high value, low effort
**Status**: OPEN

### 2026-10-13 — Commit message conventions not specified
**What happened**: Upgrade work used commit messages like "fix: Guard Hyku VERSION constants" + "chore: Remove override" + "fix: Add shim". Pattern emerged but not documented anywhere.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Corrected 2026-10-13 (web review): wvu_knapsack does have a CONTRIBUTING.md. Its "Git Workflow" section says only to commit with clear messages, with no type prefixes, no upstream-reference rule and no multi-commit pattern (REPORTED: pasted from GitHub; branch not confirmed). The earlier claim that no CONTRIBUTING rules exist was wrong. agent-tasks has no commit convention guidelines (original check, not re-verified).
**What needs a second opinion**: Decided 2026-10-13 (Tracy): add a "Commit messages" subsection to the knapsack CONTRIBUTING.md (type prefixes; one logical change per commit; name the upstream issue or PR for workarounds; submodule bumps as their own commit). Not decided: whether agent-tasks needs its own convention. Its own commits already use `task:` and `complete:` prefixes (VERIFIED in README "Task Completion Workflow").
**Priority**: LOW — not blocking, improves git log readability
**Status**: OPEN. Next action: run `projects/wvulibraries_knapsack/tasks/backlog/2026-10-13-LOW-DOCS-CONTRIBUTING-COMMIT-MESSAGES.md` (drafted, not dispatched). Mark RESOLVED when its diff is reviewed and committed.

### 2026-10-18 — TASK_TEMPLATE.md synthesis section contradictions (catalog-image containment review)
**What happened**: During catalog-image containment task draft review, four internal contradictions were found in `TASK_TEMPLATE.md` that affect every task file using it:

1. **Synthesis output conflict**: Lines 241 and 280 say to "post synthesis in chat" but lines 123 and 243 say "never paste synthesis in chat."
2. **Step ordering conflict**: Synthesis section (line 241) says "before any commands" while Step 0 (lines 102/107) requires lifecycle commands first — impossible to satisfy both.
3. **Path inconsistency**: the `find` verification command uses `agent-tasks/projects/...` (relative to the clone parent) while the `git mv` commands use `projects/...` (relative to the clone root); intended working directory is unverified.
4. **Body ordering conflict**: Body lists readiness checklist before Dispatch Interface, but header comments say Dispatch goes first.

**Observed in**: galaxy_game project, catalog-image containment task drafts (A/B/C) review session
**What I already checked**: VERIFIED against current `TASK_TEMPLATE.md` — all four contradictions confirmed on disk. Affects every task file that follows this template.
**What needs a second opinion**: (1) What is the correct synthesis output destination — chat or file? (2) Should Step 0 run before or after synthesis reading? (3) Should paths be absolute to clone root or relative? (4) Which ordering is intended — checklist first or dispatch first? **Recommendation**: Schedule a dedicated Claude session for agent-tasks repo to fix the template holistically rather than patching individual task files.
**Priority**: HIGH — every drafted task inherits these contradictions
**Status**: OPEN. Next action: review with a Claude session for the agent-tasks repo; fix at template level, then re-substitute blocks in affected tasks.

---

### 2026-10-13 — Artifact categorization unclear
**What happened**: SESSION_CLOSEOUT.md says "never delete without human approval" for everything. But intermediate synthesis report drafts, debugging notes, and working handoff versions clutter the repo. Unclear what stays vs gets archived.
**Observed in**: agent-tasks project, workflow observation
**What I already checked**: GUARDRAILS.md (no artifact lifecycle rules); SESSION_CLOSEOUT.md section 0 (says don't touch other sessions' files, but doesn't define artifact types)
**What needs a second opinion**: Define artifact categories: (a) Session records (keep): task files, status.md entries, final handoff; (b) Working drafts (archive per approval): intermediate notes, synthesis drafts; (c) Permanent history (never delete): completed task files, merged commits, approved PRs.
**Priority**: MEDIUM — prevents repo bloat
**Status**: OPEN

### 2026-10-13 — WVU Knapsack submodule upgrade lessons should apply to other projects
**What happened**: Discovered several gotchas specific to Hyku 7.1 → 7.2 upgrade: cached Docker images hide timing bugs, initializer load order is critical, third-party gem compatibility changes. These apply to ANY Ruby framework/gem upgrade, not just Hyku.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Added to wvulibraries_knapsack SESSION_GUIDANCE; not yet generalized for agent-tasks or other projects
**What needs a second opinion**: Should agent-tasks ROOT_GUIDANCE.md (or similar) have a generic "Framework/Gem Upgrade Checklist" that all projects can reference? Would prevent re-discovering same lessons on next Node/Rails/React/Python version bump in a different project.
**Priority**: LOW — nice-to-have, improves consistency
**Status**: OPEN


### 2026-10-18 — Handoff conventions undecided (template, folder, naming, multi-day rule)
**What happened**: `TEMPLATE-SESSION-HANDOFF.md` names Perplexity as its author and has six sections (purpose, session summary, tasks worked on, standing facts, next steps, pointers). The two most recent web handoffs (2026-10-15 and 2026-10-18, in `projects/agent-tasks/handoffs/claude-free-web/`) use a different layout ("How to start the next session", "What landed", "Not done yet", closeout, environment notes). The 2026-10-18 handoff took that layout from the 2026-10-15 one, not from the template. Nothing states which layout is the standard. The folder convention (`projects/{project}/handoffs/claude-free-web/`, hyphenated names as in `454e3bc`) and the file naming (`session_handoff_YYYY-MM-DD_TOPIC.md`) are not written in the closeout or session-start docs either, and there is no rule for when a handoff is due. The 2026-10-15 handoff was missed at its first closeout and written on 2026-10-18 at Tracy's prompt, after a session that ran 2026-10-13 to 2026-10-15 with a Session Log entry per day and no handoff.
**Observed in**: agent-tasks project, web review sessions 2026-10-15 and 2026-10-18
**What I already checked**: `WEB_AGENT_SESSION_START.md` (VERIFIED: text attached to the session) names no handoff folder or template, and its "End of session" list does not mention writing a handoff. `SESSION_CLOSEOUT.md` section 4 asks only for a brief statement of maintenance performed and exceptions, with no rule on location, naming, or when a handoff is required (REPORTED: read through a fetch tool, 2026-10-18). `TEMPLATE-SESSION-HANDOFF.md` content is also REPORTED (fetch tool).
**What needs a second opinion**: (1) Update `TEMPLATE-SESSION-HANDOFF.md` to the layout the recent web handoffs use, or require the old layout? (2) State the folder convention and file naming in `SESSION_CLOSEOUT.md` and `WEB_AGENT_SESSION_START.md`. (3) When must a handoff be written, for example at the end of each day of a multi-day session? (4) New file per day, or update the previous one? Related OPEN entry: "Handoff file lifecycle undefined" (2026-10-13), which asks whether the template needs a "NEXT SESSION" section. The recent web handoffs already have one ("How to start the next session").
**Priority**: MEDIUM — affects every multi-session project
**Status**: OPEN

### 2026-10-18 — NEEDS_REVIEW headers and templates differ across projects
**What happened**: Three NEEDS_REVIEW files now have three headers. galaxy_game (`projects/galaxy_game/NEEDS_REVIEW.md`) and this file both open with "flagged for Claude (planning review) or human decision"; galaxy_game then addresses Qwen and this file addresses "Implementation agents". galaxy_game lists five triggers: gitignored or data paths, unverified completion, cross-task dependencies, disagreeing documents, repeated actions. This file lists five different triggers: friction across 3+ sessions, a working tool or pattern that needs documenting, a gap in guidance, contradictory patterns across projects, and a procedure worth recommending elsewhere. The knapsack file (`c07985a`) copies the galaxy header, except that its first trigger names "a gitignored or data path" instead of `data/json-data/`. None of the three templates has a `Priority` field, but all six entries in this file use one. A first version of the knapsack file by another model added an `Owner` line to the template, removed before the commit. The 2026-10-15 handoff listed the missing `Priority` field among the OPEN entries, but no such entry existed until this one. This file was renamed from `needs_review.md` to `NEEDS_REVIEW.md` in `d2c3242`. Eight lowercase references to the old name remain in three files: the 2026-10-15 handoff (3 lines), `projects/agent-tasks/status.md` (3 log lines) and the draft `projects/agent-tasks/tasks/drafts/2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md` (2 lines, in its Dependencies and Notes sections).
**Observed in**: agent-tasks project, web review session 2026-10-18
**What I already checked**: header and template differences between the galaxy_game and knapsack files, and the `Owner` line in the first version of the knapsack file (VERIFIED: Tracy's `diff` of the two header sections). This file's template block (VERIFIED: Tracy's `sed` output, 2026-10-18); its intro, triggers and the `Priority` line on each of its six entries (REPORTED: fetch tool, 2026-10-18). The lowercase references (VERIFIED: `git grep -c 'needs_review\.md'`, which matched only .md files in those three files). The live docs use the uppercase name (VERIFIED: `git grep -c 'NEEDS_REVIEW\.md'` lists `README.md`, `PLANNING_AGENT_SESSION_START.md`, `WEB_AGENT_SESSION_START.md`, `HUMAN_WORKFLOW_GUIDE.md` and `TEMPLATE-SESSION-HANDOFF.md`).
**What needs a second opinion**: (1) One shared header and template for all NEEDS_REVIEW files, or per-project headers? (2) Add `Priority` (and `Owner`?) to the template, since the entries already use `Priority`? (3) The draft's two lowercase paths: fix them when the draft is dispatched? It is another session's file, so they stay for now. The handoff and `status.md` lines are history and stay.
**Priority**: LOW — consistency only; no behaviour depends on it
**Status**: OPEN
