# WVU Libraries Knapsack — Project Status & Task Tracking
**Last Updated:** 2026-06-17

---

## Project Overview
Knapsack — WVU Libraries resource management and digital collection system (Hyku/Hyrax-based).

**Context**: Prototype branches support experimentation with custom features and architectural patterns. 
- **LLM in Core Products**: LLM integration will NOT be incorporated into core Hyku/Samvera products (organizational decision).
- **Future AI Products**: AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion). Knapsack experimentation may align with future directions.
- **Prototypes**: clover-test may inform architecture patterns; ollama_testing is independent local experimentation.

---

## Current Status
- **Status:** Multiple branches active; main branch stable, prototype branches in progress
- **Active Branches:**
  - `main` — Stable; pagination + featured collections features deployed
  - `clover-test` — Clover IIIF viewer integration (CSS/view work remaining)
  - `ollama_testing` — Ollama vision model for alt-text generation (Valkyrie rewrite needed)
  - `alt-text-views-only` — (TBD)
- **Last Session:** 2026-06-17

---

## Active Tasks

### Branch: clover-test
1. **2026-06-17-HIGH-FEATURE-COMPLETE-CLOVER-TEST-BRANCH.md**
   - Fix featured collections grid layout (CSS not rendering)
   - Test Flipflop feature flag per-tenant activation
   - Status: Ready for assignment to local agent

### Branch: ollama_testing
2. **2026-06-17-MEDIUM-FEATURE-COMPLETE-OLLAMA-VISION-ALT-TEXT.md**
   - Rewrite AiMetadataBehavior for Valkyrie (ActiveFedora → Valkyrie migration)
   - Integrate vision service with Bulkrax importer
   - Backfill existing FileSet objects with alt-text
   - Status: Ready for assignment to local agent

---

## Completed This Session (2026-06-17)
- ✅ Fixed pagination settings in catalog (per_page=[6,12,24,48,96], default=12)
- ✅ Increased featured collections limit (6 → 15)
- ✅ Resolved clover_viewer feature flag error handling (Valkyrie mode)
- ✅ Fixed Wings::ModelRegistry error in flexible mode
- ✅ Added delegated_attributes method to Document model (Valkyrie compatibility)
- ✅ Moved all changes from hyrax-webapp to knapsack (decorator pattern)
- ✅ Created comprehensive task tracking structure in agent-tasks repo

---

## Backlog
- [To be populated as new work is identified]

---

## Prototype Branches Overview

**clover-test**: Clover IIIF Viewer integration
- Feature flag: `Flipflop.enabled?(:clover_viewer)`
- Per-tenant control: Admin dashboard → Features tab
- Status: Infrastructure ready; CSS/view debugging needed

**ollama_testing**: Ollama Vision for Alt-Text
- Model: moondream via Ollama (POST /api/generate)
- Feature: Auto-generate archival alt-text (125 chars) for images/PDFs
- Blocker: AiMetadataBehavior needs Valkyrie rewrite (currently uses deprecated ActiveFedora)
- Integration: Bulkrax importer post-import hook

**alt-text-views-only**: (TBD)
- Purpose: To be documented

---

## Task Order & Priority Notes

1. **HIGH** - Clover-test: Fix featured collections grid layout first (blocking UI presentation)
2. **MEDIUM** - Ollama_testing: Valkyrie rewrite (longer task, experimental feature)

All task management lives in `/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/`

---

## Special Warnings & Conventions

- **Knapsack Pattern**: NEVER modify files in `hyrax-webapp/` directly. Use decorators in knapsack `app/`, `lib/`, or `config/initializers/`.
- **Valkyrie Mode**: HYRAX_FLEXIBLE=true — Use Valkyrie query service for file metadata, not ActiveFedora API.
- **Flipflop Integration**: Feature flags gracefully handle missing definitions (rescue StandardError).
- **Asset Pipeline**: After CSS changes, restart stack with `sh down.sc.local.sh && sh up.sc.local.sh` (rebuilds images).

---

## Session Notes
- Comprehensive task tracking initialized (2026-06-17)
- Two prototype branches with detailed task specs created for local agent assignment
- Main branch validated with pagination + featured collections features
- Ready for distributed work via local agents
- See agent_guides/wvulibraries_knapsack.md for domain context
