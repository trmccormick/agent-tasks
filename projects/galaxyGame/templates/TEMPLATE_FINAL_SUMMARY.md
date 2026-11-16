# TEMPLATE — FINAL_SUMMARY.md (For Claude Review)

**Date**: YYYY-MM-DD  
**Original Task**: `/tasks/review/[TASK_FILENAME].md`  
**For Review By**: Claude  
**Research Phase**: Completed by Qwen before Gemini synthesis

---

## Executive Summary

[1-2 sentences: What task is being refined? Why? What's the key outcome?]

This refined task incorporates findings from pre-synthesis research phase (Qwen) to ensure it addresses actual codebase state and overlaps with recent work.

---

## Refined Task (Gemini's Proposal Based on Research)

[Gemini's full proposed replacement task - ready to move to active/backlog/phase folder]

---

## Research Used

**Research Location**: `/refactored-task-files/YYYY-MM-DD/qwen-research/`

**Research Files**:
- [List files Qwen created]
- [List any findings/validation reports]

**Key Research Findings**:
- [Summary of what Qwen discovered]
- [Any blockers identified]
- [Prerequisites confirmed or changed]

---

## How Refined Task Differs from Original

| Aspect | Original | Refined | Why |
|--------|----------|---------|-----|
| Scope | [Original scope] | [Refined scope] | [Reason] |
| Dependencies | [Original deps] | [Refined deps] | [Reason] |
| Approach | [Original approach] | [Refined approach] | [Reason] |

---

## Original Task (Reference)

**Location**: `/tasks/review/[TASK_FILENAME].md` (will move to `/tasks/deprecated/` after approval)

**Status**: To be superseded by refined task above

---

## Phase Routing Decision

**Refined task destination**:
- [ ] Phase 5 Luna MVP (active work on luna_mission.rake)
  - Phase: 5a/5b/5c?
  - Destination: `/tasks/active/phase5/[TASK_NAME].md`
- [ ] Phase 6+ (queued for after Phase 5)
  - Phase: 6/7/8/9/10/11/12/13/14+?
  - Destination: `/tasks/backlog/phase6+/[TASK_NAME].md`
- [ ] Design/Reference (preserve game design concepts)
  - Destination: `/docs/new_agent/projects/galaxy_game/design/[TASK_NAME].md`

**Rationale**: [Why this routing?]

---

## Claude's Decision Needed

- [ ] **Approve Phase Routing + Refined Task**: Move refined task to designated destination, save original to reference archive
- [ ] **Request Changes**: [Specific feedback on phase routing or task content]
- [ ] **Blocker**: [What's missing or wrong]

---

## Next Steps After Approval

✅ **If approved**:
- Original task → `/tasks/reference/[TASK_FILENAME].md` (for design concept archival)
- Refined task → Designated destination:
  - Phase 5: `/tasks/active/phase5/[NEW_TASK_FILENAME].md`
  - Phase 6+: `/tasks/backlog/phase6+/[NEW_TASK_FILENAME].md`
  - Design: `/docs/new_agent/projects/galaxy_game/design/[NEW_TASK_FILENAME].md`
- Day folder → Archived in `/refactored-task-files/YYYY-MM-DD/` as audit trail

---

## Audit Trail

All planning, research, and draft work preserved in:  
`/refactored-task-files/YYYY-MM-DD/`
- ANALYSIS.md (planning phase)
- GEMINI_BRIEF.md (gemini research request)
- qwen-research/ (research findings)
- gemini-draft/ (this file + draft task)
