# Samvera Hyku — Project Status & Task Tracking
**Last Updated:** 2026-07-22

---

## Project Overview
Samvera Hyku — open-source institutional repository platform built on Hyrax and Rails.
Main repo: https://github.com/samvera/hyku (278 open issues)

---

## Current Status
- **Status:** Multi-tenant GA fix split into two-phase parallel approach. Phase 1 (urgent override) ready for executor. Phase 2 (upstream PR) ready after Phase 1 completion.
- **Last Session:** 2026-07-22 — Qwen identified critical architecture issue, created synthesis, planning adjusted approach
- **Agent Notes**: See `notes.md` for technical discoveries and contextual findings from work sessions
- **Synthesis Complete**: [2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md](summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md) — Documents both approaches
- **Phase 1 Task**: [2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md](tasks/active/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md) — ACTIVE, ready for executor
- **Phase 2 Task**: [2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYRAX-PR.md](tasks/backlog/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYRAX-PR.md) — BACKLOG, after Phase 1

---

## Active Tasks
 — Two-Phase Parallel Implementation

#### PHASE 1: Hyku Override (PERMANENT FIX — ACTIVE) ✅ Ready for Dispatch
- **Task File**: [2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md](tasks/active/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md)
- **Approach**: Create monkey-patch overrides in Hyku to fix multi-tenant analytics
- **Timeline**: Ready NOW (this Hyku instance)
- **Scope**: This is the **PERMANENT solution** for multi-tenant GA support in Hyku
- **Why Hyku?**: Multi-tenancy is a Hyku feature, not a Hyrax feature (Hyrax is single-tenant)
- **Deliverables**: 
  - Override modules for Ga4 service
  - Override code for analytics controllers
  - RSpec tests for tenant isolation
  - Multi-tenant manual testing
- **Estimated Work**: ~3-4 hours
- **Status**: ✅ Ready for Qwen executor agent dispatch

#### PHASE 2: Hyrax Proposal (DISCUSSION/PROPOSAL — BACKLOG) ⏳ After Phase 1
- **Task File**: [2026-07-22-MEDIUM-DOC-GA-HYRAX-FLEXIBILITY-PROPOSAL.md](tasks/backlog/2026-07-22-MEDIUM-DOC-GA-HYRAX-FLEXIBILITY-PROPOSAL.md)
- **Approach**: Create GitHub issue in samvera/hyrax proposing optional `property_id` parameter
- **Timeline**: No rush — this is a background discussion
- **Scope**: Community proposal, not a blocker
- **Purpose**: Suggest that Hyrax consider optional `property_id` parameter for flexibility
- **Possible Outcomes**:
  - ✅ If Hyrax accepts: Future versions could skip override
  - ✅ If Hyrax declines: Hyku keeps override (correct approach for multi-tenancy)
- **Estimated Work**: ~30-45 minutes (create issue, not implementation)
- **Status**: 📋 BACKLOG — After Phase 1 completion + approval

### Why This Architecture Is Better

| Phase | Purpose | Location | Timeline | Urgency |
|-------|---------|----------|----------|---------|
#### Critical Architecture Correction (2026-07-22)
**Finding**: Hyrax is single-tenant, so GA fix belongs **permanently in Hyku**, not upstream.

**Corrected Approach**:
- **Phase 1**: Permanent Hyku override for multi-tenant GA (correct architectural location)
- **Phase 2**: Optional proposal to Hyrax (would simplify Phase 1, but not required)

**Impact**: Phase 1 is the PERMANENT solution. No need to remove it later.A-MULTITENANT-IMPLEMENTATION-OPTIONS.md](summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md)
- Documents both Option A (upstream) and Option B (override)
- Comparison table of approaches
- Recommendation for parallel strategy

### Previous Research (Completed, Reference)
- **[2026-06-26] Root Cause Analysis** [#2985 & #2995](tasks/active/2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md)
  - All tenants share same GA property ID from ENV
- **[2026-06-26] Validation** [PALNI/PALCI Issue #611](tasks/active/2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md)
  - Production case: 248+ phantom entries, 46 cross-tenant ID overlap
  - 248+ phantom entries per tenant, 100% data loss for one tenant

---

## Recently Completed Research Phase

### ✅ GA Multi-Tenant Issues Analysis (2026-06-26 to 2026-07-07)
- **Research Completed**: Root cause analysis of Samvera issues #2985 & #2995
- **Deliverables**: 3 detailed task files + validation documents + implementation handoff
- **Real-World Validation**: PALNI/PALCI Issue #611 proves cross-tenant data commingling in production
- **WVU Knapsack Testing**: Cross-tenant event bleed test performed (result: architectural issue confirmed)
- **Reviewer Approval**: Rob approved Fix #1 (tenant-specific GA property IDs), rejected Fix #2
- **Status**: READY FOR IMPLEMENTATION PHASE
- **Next**: Developer picks up implementation handoff for Phase 1 (~3-4 hours work)

---

## Current Sprint (2026-06-23)
**Added to Sprint**: 
- #2989 — PDF Viewer/Download Button Toggles (ALREADY RESOLVED - verify only)
- #2990 — Batch Edit Descriptions Formatting Regression (ACTIVE IN BACKLOG)

---

## Recent Backlog Updates

### Accessibility Issues (High Priority - Hyku 7 Release)
Most recent issues are accessibility-related from automated scans and manual testing:

**Color Contrast & Visual:**
- #3092 — Color contrast does not meet minimum requirement (Jun 3, 2026)
- #3089 — Text is clipped when resized (Jun 3, 2026)
- #3077 — Color contrast: Bulkrax Field Mappings (Jun 1, 2026)
- #3076 — Color contrast: Dashboard navigation (Jun 1, 2026)
- #3054 — Text/background color contrast on Show Pages (May 12, 2026) [Has linked PR]

**Interactive Elements:**
- #3091 — Interactive element does not meet minimum size/spacing (Jun 3, 2026)
- #3087 — Text clipped in Appearance > Fonts dropdown lists (Jun 2, 2026)
- #3086 — Visible label vs accessible label mismatch in TinyMCE editor (Jun 2, 2026) [hyku 7]
- #3085 — Alt text missing on "Collections" and "Works" icons when dashboard resized (Jun 2, 2026) [hyku 7]
- #3060 — Interactive element on File Set size/spacing issues (May 18, 2026) [hyku 7]

**ARIA & Screen Reader:**
- #3090 — Link missing text alternative (Jun 3, 2026)
- #3051 — Advanced Search dropdown missing descriptive text label (May 11, 2026) [hyku 7]
- #3050 — aria-labelledby attribute must point to IDs in same document (May 10, 2026)
- #3049 — Headings should not be empty (May 10, 2026) [hyku 7]
- #3047 — FileSets Thumbnail Image Link missing text alternative (May 8, 2026) [hyku 7]

**Forms & Tables:**
- #3078 — Form field missing label: Bulkarax Field Mappings (Jun 1, 2026)
- #3082 — Table cell missing context on Features page (Jun 1, 2026)

### Bug Fixes
- #3072 — Catalog Search Page Results Get Invalid Query Parameters (May 26, 2026) [Bug label]
- #3048 — TypeError in Hyrax::Stats#file (May 10, 2026) [hyku 7]
- #3024 — Intermittent routing error on works edit route (Apr 18, 2026)

### Feature Requests & Tasks
- #3035 — Add Optional Clover IIIF Viewer Support to Hyku (Apr 30, 2026) [Assigned: trmccormick]
- #3031 — Request: Read-only access to Jobs Dashboard for repository admins (Apr 27, 2026)
- #3042 — Check if "File Format Recommendations" page covers viewer-related questions (May 6, 2026) [documentation]

### Configuration & Validation
- #3012 — Identify minimum configuration for Hyku 7 m3 profile validation (Apr 14, 2026)

---

## Task Order & Priority Notes
- All task management lives in `/Documents/git/agent-tasks/projects/samvera_hyku/tasks/`
- Follow agent workflow rules as documented in agent-tasks repo

**Priority Recommendations:**
1. **Accessibility fixes for Hyku 7 release** — Issues labeled "hyku 7" should be prioritized
2. **Bug fixes affecting core functionality** — #3048 (TypeError), #3024 (routing error)
3. **Feature enhancements** — IIIF viewer support (#3035), Jobs Dashboard access (#3031)

---

## Special Warnings & Conventions
- Follow all agent workflow and commit protocols as documented in projects/samvera_hyku/README.md
- Do NOT modify core Hyku code directly; use decorators or overrides for customizations
- All version control operations must be executed on the host node (not inside Docker)
- Targeted testing only — never run full RSpec suite

---

## Session Notes
**2026-06-17:** Initial planning session completed:
- Reviewed GitHub issues page (https://github.com/samvera/hyku/issues)
- Identified 278 open issues, majority are accessibility-related from recent audits
- WVU Knapsack-specific tasks moved to separate project folder (`wvulibraries_knapsack/`)
- Ready for task assignment and handoff generation

**Next Steps:** Select specific issue(s) to create formal task files in `tasks/backlog/`
