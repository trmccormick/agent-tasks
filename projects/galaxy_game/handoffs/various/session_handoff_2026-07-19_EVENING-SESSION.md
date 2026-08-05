Session Handoff — Galaxy Game — 2026-07-19
Workflow change tested this session (start here)
New structure: Qwen (persistent, local) does implementation, maintains status.md (full log) and NEEDS_REVIEW.md (short escalation list). Claude reviews NEEDS_REVIEW.md entries and makes judgment calls, rather than receiving full transcripts by default. Role-definition doc for Claude was written this session — paste it first, before this handoff.
First test of next session: does Tracy relay NEEDS_REVIEW.md (short) instead of pasted terminal output (long)? If yes, workflow is working as intended.

Open items (in NEEDS_REVIEW.md, unresolved)
Unit Sprite Extraction Visual Validation Gap — sprites for the Unit Layer Rendering task (galaxyGame commit 9ee37c6b) are misaligned/malformed at cells distant from grid origin (sprite_00 fine, sprite_15 badly cropped). Root cause: source image is an AI-generated collage, not a precision-gridded sprite sheet — grid-math extraction can't fix this, needs proper asset regen. RSpec (18/18 passing) never validated visual content, only structure — this is why it shipped as "complete" without catching the problem.
Decision needed: (a) mark sprites placeholder + gate Layer 5 rendering behind a feature flag, (b) commit to proper asset generation (fits the existing "Phase 2 Asset Registry" work already flagged as a future project), or (c) something else. Recommend (a) short-term + (b) long-term, but this is Tracy's call.

Resolved this session (confirmed, not just claimed)

Precursor Mission chain (Tasks A–D): Profile, Manifest, Rake Phase 1, Rake Phase 2 all complete and Docker-validated for real (not just self-reported). Manifest now uses yield_formula + variance_range instead of hardcoded delivery constants, per design correction mid-session.
Data-path bug pattern: data/json-data/ (correct, gitignored, Docker-mounted) vs galaxy_game/data/json-data/ (wrong, tracked, NOT mounted) — hit and fixed 3+ times this session across Profile, Manifest, and a stray orbital_em_skimmer_mid.json. Now documented as a standing thing to check on any task touching JSON data.
CanonicalMapService 'jungle' alias — investigated twice, confirmed NOT a bug (terminal filename value vs. lookup key confusion). Closed for good with a code comment added to prevent re-flagging.
RSpec regressions triaged: planetary_monitor_spec.rb:171 — real bug, root cause found (test data elevation needs 2D grid, was given 1D), fix identified but not yet applied. orbital_shipyard_service_spec.rb:129 — false alarm, stale baseline entry, actually passing (25/25).
ProcurementService research correction: earlier report recommended the wrong fix pattern (NpcPriceCalculator/facility-lookup). Corrected — the right fix delegates to PrecursorCapabilityService, which already exists and works via celestial-body sphere-data queries. Correction note attached to both the report and the refactor task draft.
Craft exhaust → atmosphere feedback: confirmed NOT implemented despite being originally-intended design. Real gap, drafted as a standalone feature task, independent of the precursor mission chain.
Phase 4 wiki (14-section structure): fully verified and committed (commit fcbd6958), including the two-pass verify-then-fix cycle that caught what earlier "complete" claims missed.

Still open, not yet started

planetary_monitor_spec.rb:171 fix (root cause known, fix not applied)
CanonicalMapService bug-verification — never independently confirmed the 4 bugs (this was actually two different services under discussion this session — this refers to the original 4 flagged bugs from the CanonicalMapService task spec itself, separate from the 'jungle' NEEDS_REVIEW investigation)
3 drafted-but-inactive tasks in drafts/2026-07-17/: elevation-in-biome-classification research, biome-visual-variety design note, craft-exhaust-atmosphere feature (now has more detail from this session's confirmed-gap finding — worth updating the draft before activating)
Rake Phase 1 fallback-path — was fixed once, re-verify it's still clean after Phase 2 touched the same file (last checked clean, but worth a final confirm)


For next session

Paste the role-definition doc, then this handoff.
Check NEEDS_REVIEW.md for the sprite decision — that's the one thing actively waiting on Tracy.
Everything else above is tracked; pick up wherever's highest priority.