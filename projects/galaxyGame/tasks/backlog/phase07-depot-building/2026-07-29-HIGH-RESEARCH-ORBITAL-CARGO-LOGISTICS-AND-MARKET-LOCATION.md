---
status: backlog
priority: HIGH
type: research
system_domain: ECONOMICS
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-07-29
last_updated: 2026-07-29
---

## ⚡ Minimal Handoff (Copy this to send to agent)
```text
You are **Implementation Agent** — this is a TRACE/RESEARCH-ONLY task.
No code changes, no migrations, no new models. Output is a written
decision + recommendation for a follow-up implementation task.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/research/2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md \
         projects/galaxy_game/tasks/active/2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-29-HIGH-RESEARCH-ORBITAL-CARGO-LOGISTICS-AND-MARKET-LOCATION.md"
    Only ONE result should exist.

READ FIRST (after Step 0): Task file contains all context and research questions.

CRITICAL: Save findings as MD file to summaries folder BEFORE closing out.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-07-29-RESEARCH-ORBITAL-CARGO-LOGISTICS.md
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Research — Orbital Cargo Logistics & Market Location (buy vs. physical transfer)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-29
**Last Updated**: 2026-07-29

---

## Context

**Origin**: This replaces `2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md` (now in `backlog/superseded/`), which proposed adding polymorphic ownership to `Market::Marketplace` so individual structures could each have their own marketplace. That approach is no longer believed correct — significant scope creep and reframing happened during 2026-07-29 review, summarized below.

**What's already confirmed, not up for debate**:
- `Settlement::BaseSettlement` already has `has_one :marketplace`. `Settlement::OrbitalSettlement < BaseSettlement`, so orbital settlements already inherit ONE marketplace at the settlement level. This part of the original task's gap is closed — no polymorphic Marketplace ownership is needed.
- `Inventory` uses `belongs_to :inventoryable, polymorphic: true` — individual structures can and do have their own separate `Inventory` records, distinct from the settlement's own inventory. This is the real source of the remaining gap: goods can be tracked as physically located in a specific structure's inventory, but a settlement-level marketplace has no inherent concept of "which structure actually holds this."

**The real gap, reframed through discussion**:
- Buying something should be a pure ownership change — no location constraint, should work from anywhere, against the settlement's single marketplace.
- *Loading* a purchased/owned item onto a craft is where location matters: for orbital settlements, a craft should only be able to load items that physically sit in the specific structure it's currently docked at. Owned items sitting in a *different* structure within the same orbital settlement are not loadable until either the craft relocates, or some cargo-logistics mechanism moves the goods to where the craft is.
- Surface settlements are explicitly different: it's assumed there's implicit internal transport (road/tram/etc.) between any storage building and the landing pad, so no such location check applies there. This distinction — orbital structures are physically disconnected, surface settlement buildings are not — is the reason the restriction exists for orbital and not surface.
- A candidate mechanism to bridge the orbital gap without forcing every buyer's own craft to physically relocate: **shuttles that move cargo between structures within an orbital settlement** (analogous to surface settlement's implicit internal transport, but modeled explicitly with transit time since orbital structures don't get that assumption for free). This was floated but NOT decided — see research questions below.

**Why this needs research before an implementation task is written**: multiple open design questions (see below) meaningfully change the scope and shape of any eventual implementation. Given ChatGPT/Gemini session time is a limited resource, this should be one focused research session, not resolved live during implementation.

---

## Research Questions (this is the actual deliverable)

1. **Does the craft-docked-at-structure restriction need to exist as a hard rule at all, once/if shuttles exist?** I.e., does a shuttle system fully replace the dock-location check (cargo can always be summoned to wherever the craft is, given enough time), or does it sit alongside the dock check as a faster alternative to physically relocating the craft yourself?

2. **What is a shuttle, mechanically?** NPC-run automated logistics (no player-piloted craft involved), a player-ownable/pilotable craft type, or a fully abstract timer/animation with no underlying craft object at all? Each has very different implementation cost.

3. **What's a reasonable transit-time model for cargo movement between structures in the same orbital settlement?** Fixed time per structure-pair, distance/orbital-mechanics-based, or a flat simplified MVP constant? Should scale with the game's existing sense of "how far apart are things in the same orbital settlement," if that concept already exists anywhere in the codebase.

4. **Does this interact with any existing craft/docking mechanics already built?** Check the actual codebase (not just design docs) for an existing "craft docked at X" concept — search for docking-related associations/state on Craft models, and any existing cargo-transfer methods, before assuming this needs to be built from scratch. If something already exists, this task should reuse/extend it, not duplicate it.

5. **Does the buy/transfer split (buy = ownership change only, transfer = physical loading, gated by location for orbital) match how purchases work anywhere else in the codebase today?** Check `Market::Marketplace`, `Inventory#add_item`/`#remove_item`, and any existing order/transaction logic for how ownership vs. physical possession is currently modeled, if at all. This may already partially exist and just need extension, or may need to be introduced as a new concept.

---

## Explicitly Out of Scope for This Task
- No code changes, no migrations, no new models or associations
- No implementation of shuttles, docking checks, or transfer logic
- Output is a written recommendation + a clearly scoped follow-up implementation task description (do not create that follow-up task file — describe what it should contain, Tracy will review before it's filed)

---

## Acceptance Criteria
- [ ] All 5 research questions above answered with actual codebase evidence (grep/read results), not just design reasoning
- [ ] Clear recommendation: does the dock-location restriction need shuttles to be viable, or can it ship first without them (shuttles as a later addition)?
- [ ] Recommended shape for a follow-up implementation task, scoped small enough to avoid the scope creep that killed the original marketplace-on-structure task
- [ ] Findings saved to summaries/ as specified above

---

## Stop Conditions — escalate to user immediately if:
- Existing craft/docking code turns out to already implement something close to this, in which case the whole framing of this research task may need to change
- The buy/transfer ownership split turns out to conflict with how purchases already work elsewhere in the codebase (would be a bigger architectural question than this task's scope)

---

## Dependencies
**Blocked by**: none
**Blocks**: any future orbital cargo/marketplace implementation task
**Related tasks**: `backlog/superseded/2026-04-16-HIGH-FEATURE-MARKETPLACE-ON-STRUCTURE.md` (superseded by this task — read for full context/decision trail, not for its proposed solution)

---

## Completion Report
*Filled in by the implementing agent after completion*