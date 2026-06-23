# Samvera Hyku — Project Status & Task Tracking
**Last Updated:** 2026-06-17

---

## Project Overview
Samvera Hyku — open-source institutional repository platform built on Hyrax and Rails.
Main repo: https://github.com/samvera/hyku (278 open issues)

---

## Current Status
- **Status:** Ready for task assignment from GitHub issue review
- **Last Session:** 2026-06-17 — Initial planning session, reviewed GitHub issues

---

## Active Tasks
- None currently assigned (ready for dispatch)

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
- Follow all agent workflow and commit protocols as documented in agent_guides/samvera_hyku.md
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
