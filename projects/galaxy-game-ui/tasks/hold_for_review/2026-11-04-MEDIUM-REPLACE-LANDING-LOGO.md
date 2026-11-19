---
status: drafts
resume: false
priority: MEDIUM
type: refactor
system_domain: VIEWS / LANDING
mvp_alignment: OTHER
local_worker_safe: true
---

# Replace the Landing Page Logo with the Approved Galaxy Game Asset

## Goal

Update only the landing page's logo/brand presentation to use the approved Galaxy Game logo asset and align its sizing and placement with the current prototype identity.

The landing page has already been ported. This task is not a second landing-page implementation.

## Before editing

1. Read the current UI project guidance, `NEEDS_REVIEW.md`, `SESSION_GUIDANCE.md`, and latest handoff.
2. Inspect the current landing page, application layout, existing image assets, and recent UI commits on `review/qwen-ui-prototype`.
3. Inspect the logo assets already present in the UI repository and the source assets in the main GalaxyGame repository. Do not create a new logo or assume which variant is approved.
4. Check prior design discussion and current project records for the intended logo variant. If the approved variant cannot be established from the available sources, stop and ask Tracy rather than choosing one.

## Scope

- Replace the landing page's current logo or text-only brand mark with the confirmed existing Galaxy Game logo asset.
- Preserve the landing page's existing content, links, and layout.
- Set sensible responsive sizing and accessible alternative text.
- Ensure the logo is not distorted, cropped, or visually inconsistent with the dark/cyan prototype theme.

## Acceptance criteria

- [ ] The approved existing logo asset and its source path are identified in the report.
- [ ] Only the landing page logo/brand treatment and any strictly necessary asset reference are changed.
- [ ] Existing landing page content, navigation, and calls to action remain functional.
- [ ] Logo sizing is responsive and preserves the asset's aspect ratio.
- [ ] Logo is visually checked at desktop and narrow viewport sizes.
- [ ] No duplicate logo asset is added if a suitable current asset already exists.
- [ ] Exact files changed and visual evidence are reported.

## Out of scope

Changing the landing page copy, statistics, navigation behavior, shared header, sign-in screen, generating/editing logo artwork, or changing the main GalaxyGame repository.

## Dependencies

No implementation dependency on the sign-in task. This task requires confirmation of the intended logo variant before dispatch. Human approval is required before implementation and before any commit.
