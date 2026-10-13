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
**What I already checked**: No commit.md or CONTRIBUTING.md rules found in wvulibraries_knapsack root; agent-tasks has no commit convention guidelines
**What needs a second opinion**: Should a CONTRIBUTING.md or commit.md be added to wvulibraries_knapsack documenting: (a) type prefixes (fix: / chore: / docs: / etc.), (b) when to mention upstream PR/issue numbers, (c) pattern for multi-commit fixes (e.g., VERSION override across 4 commits)?
**Priority**: LOW — not blocking, improves git log readability
**Status**: OPEN

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

