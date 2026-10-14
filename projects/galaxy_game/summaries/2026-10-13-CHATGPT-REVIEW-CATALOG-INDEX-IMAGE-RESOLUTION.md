# ChatGPT Review — Catalog Index Image Resolution Task File

**Task reviewed**: `2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md`  
**Reviewer**: ChatGPT (via planning agent)  
**Disposition**: **REVISE** (applied by planning agent)  
**Date**: 2026-10-13  
**Location**: `projects/galaxy_game/tasks/backlog/asset-ui/`

---

## Review Summary

ChatGPT reviewed the working draft and returned a **REVISE** disposition with six proposed revisions. The planning agent applied all six revisions to the task file. This record maps each review point to the amended section.

---

## Revision Mapping

### Revision 1: Require Deterministic Identity Mapping
**Review point**: "Require an existing deterministic blueprint-identity → registry asset_id mapping, supported by code evidence. Prohibit filename inference, fuzzy matching, first-match selection, and RH-400-specific mapping."

**Amended section**: New section **"Prerequisite: Deterministic Identity Mapping"** inserted after Problem Statement and before Current Behavior.

**What it adds**:
- Explicit prerequisite that identity mapping must exist in code before implementation
- Four constraints: deterministic, code-supported, prohibited methods listed, evidence required
- Prohibits filename inference, fuzzy matching, first-match selection, RH-400-specific hardcoded mapping
- Requires showing the exact code path where `blueprint_id` → `asset_id` is resolved

---

### Revision 2: Require Reuse of Existing Safe Serving Mechanism
**Review point**: "Require reuse of the existing safe catalog image-serving mechanism. Distinguish filesystem paths from browser URLs. Require approved-root validation and HTTP verification. Any new route or serving convention requires separate scope approval."

**Amended section**: Step 4 (index view update) expanded with **"Revision 2 — Serving mechanism"** subsection.

**What it adds**:
- Explicit requirement to reuse existing `catalog_image` controller action (no new routes)
- Clear distinction between filesystem paths (absolute, from registry manifest) and browser URLs (relative, for `image_tag`)
- Reference to the controller's approved-root validation (`file_path.to_s.start_with?(catalog_dir.to_s)`)
- Explicit statement that any new route/serving convention requires separate scope approval

---

### Revision 3: Define Registry-First Validated Fallback and No-Image Behavior
**Review point**: "Define registry-first, validated fallback, and explicit no-image behavior. Cover missing files, unusable representations, and unsafe paths. Distinguish ordinary missing data from an architectural runtime blocker."

**Amended section**: **"Desired Behavior"** section completely rewritten as a three-step strict resolution order with subsections for distinguishing missing data types.

**What it adds**:
- Three-step strict resolution order (registry-first → validated fallback → explicit no-image)
- Path traversal validation against approved catalog root at each step
- Clear distinction between "ordinary missing data" (single asset absent) and "architectural runtime blocker" (category-wide or mount-level failure)
- Architectural blockers require documentation and human decision — not silently swallowed

---

### Revision 4: Preserve B2 Development-Time/Runtime Boundary
**Review point**: "Preserve the B2 development-time/runtime boundary. Do not authorize new runtime discovery, filesystem scanning, docs mounts, manifest generation, or registry redesign."

**Amended section**: **"Out of Scope"** section — added bullet point for B2 boundary preservation.

**What it adds**:
- Explicit prohibition on: new runtime discovery, filesystem scanning, docs mounts, manifest generation, registry redesign
- Statement that AssetRegistry is read-only data source at runtime
- No orchestration or file-generation logic belongs in this task

---

### Revision 5: Require Focused Service and HTTP/View Coverage with Actual Test Counts
**Review point**: "Require focused service and HTTP/view coverage, including image retrieval, unsafe-path rejection, fallback, and no-image rendering. Replace the fixed 25-example expectation with actual current test counts."

**Amended section**: Integration Points table (catalog_service_spec.rb row) and Step 5 (tests) both rewritten. AC5 also updated.

**What it adds**:
- Instruction to run `bundle exec rspec ... --dry-run --format documentation` to get actual count before starting
- Five specific test cases (was three): registry path present, registry unavailable fallback, no image available, unsafe path rejection, no-image rendering
- AC5 now references "actual pre-change and post-change counts" instead of fixed "25"
- HTTP-level test for `catalog_image` controller action added

---

### Revision 6: Separate Pre-Implementation Synthesis from Completion Report
**Review point**: "Separate the pre-implementation synthesis from the completion report. The synthesis must evidence identity mapping, runtime metadata source, safe serving, fallback, proposed tests, and unresolved prerequisites. Require a human approval pause before code changes."

**Amended section**: **"Synthesis Report Template"** section completely replaced with **"Synthesis Reports (Revision 6 — Two-phase separation)"**.

**What it adds**:
- **Phase 1 (Pre-Implementation Synthesis)**: Six required evidence items with code references, not assertions. Human approval gate before any code changes.
- **Phase 2 (Completion Report)**: Execution artifact that follows implementation and references Phase 1 findings.
- Explicit statement of what changed from the original template.
- Mandatory human approval pause between Phase 1 and Phase 2.

---

## Task File Status After Revisions

| Field | Value |
|-------|-------|
| `status` | `backlog` (unchanged) |
| Readiness checkboxes | **NOT checked** — evidence/approval still missing per instructions |
| Disposition | **REVISE** (applied; task remains in backlog awaiting human review) |
| File location | `tasks/backlog/asset-ui/2026-10-13-HIGH-BUGFIX-CATALOG-INDEX-IMAGE-RESOLUTION.md` |
| Review record location | `summaries/2026-10-13-CHATGPT-REVIEW-CATALOG-INDEX-IMAGE-RESOLUTION.md` (this file) |

---

## Remaining Ambiguity and Prerequisites

### Ambiguity 1: Registry population at index time
The task references that registry entries may or may not be populated at runtime. The synthesis Phase 1 must confirm this with code evidence. If the registry is empty for all assets, the fallback path becomes the primary path — which is acceptable but should be documented.

### Ambiguity 2: Blueprint ID → asset_id mapping mechanism
The prerequisite section prohibits inference/fuzzy/first-match methods but does not specify the exact lookup mechanism (iterate all entries? use a dedicated lookup table?). This will be resolved in Phase 1 synthesis with code evidence.

### Prerequisite: Actual test count
The task instructs the implementer to run `--dry-run` to get the current test count. The planning agent found approximately 31 `it/describe/context/specify` blocks but did not verify the actual RSpec example count (which may differ due to nested groups). This must be confirmed before dispatch.

---

*Review applied by planning agent. Task remains in backlog awaiting human approval for implementation.*
