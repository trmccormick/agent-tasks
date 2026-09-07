---
status: backlog
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template provided
- [ ] All file paths are verified to exist — **NOT DONE**, Claude has no filesystem access; Step 1 is exactly that verification
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are Implementation Agent.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/asset-ui/2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
Confirm whether this file is tracked in git first (git ls-files <path>) —
if tracked, use git mv; if untracked, use mv then git add the final
path. Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis
until this is done.

LIFECYCLE: backlog → active → completed
Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-06-HIGH-FEATURE-ASSET-GENERATION-STANDALONE-EXECUTION.md"
Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
Chat is for questions only — never paste synthesis into chat.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Asset-Generation Tooling — Standalone Execution + Real Invocation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-09-06

---

## Context
A verification pass on the Phase 1 Asset Generation Rails-runtime → development-time tooling migration (`tools/asset_generation/`: `prompt_compiler.rb`, `profile_resolution_engine.rb`, `composition_refinery.rb`) found two real gaps, not just an unverified test claim:

1. **No standalone execution environment exists.** The tooling directory is not mounted into the `web` Docker container, and there is no `Gemfile` anywhere (project root or `tools/asset_generation/`) that would let RSpec run against it on the host either. The three converted specs (`profile_resolution_engine_spec.rb`, `prompt_compiler_spec.rb`, `composition_refinery_spec.rb`) have never actually been executed since the migration — not because of a wrong command, but because no runnable environment exists at all.

2. **The actual intended use case has never been demonstrated.** Tracy's original expectation for this tooling: read a unit's blueprint + operational_data + visual_definition and generate a real prompt string to hand to ChatGPT or Gemini for image generation. Nothing produced so far confirms this actually works end-to-end — the migration only refactored the three classes' internal dependencies. There is no confirmed script, rake task, or CLI entry point that a person can actually run to get a real prompt out.

A separate task drafted to just "run the existing tests" was correctly identified as moot — its premise (an existing, discoverable test environment) was already checked and found false. This task replaces it with the actual missing work.

## Problem Statement
1. Give `tools/asset_generation/` a real, standalone way to run its RSpec suite (own `Gemfile`/bundler setup, or a documented host-Ruby invocation — whichever fits how this tooling is meant to be used day-to-day).
2. Confirm or build an actual entry point (script/rake task/CLI) that takes real blueprint + operational_data + visual_definition file paths for one unit (e.g. RH-400, since its art already exists) and produces a real, complete prompt string as output — demonstrating the tool does what it was built for, not just that its classes are individually well-formed.

**Current state**: Three refactored classes with specs that have never run; no confirmed way to invoke the tool for its actual purpose.
**Expected output**: The three specs actually run (real pass/fail count), AND a real prompt is generated end-to-end from real input files and shown as evidence.

## Architecture Gotchas

⚠️ GOTCHA 1: Before deciding on a Gemfile or Docker mount, just try running it — ruby tools/asset_generation/prompt_compiler.rb or rspec tools/asset_generation/spec/ directly on the host (Ruby's already installed) or via docker compose exec web ruby ... without a permanent mount. If it only needs stdlib + rspec, the fix is documenting that one command in the README, not building new infrastructure. Only add a Gemfile or a Docker mount if the try-it-first step reveals a genuine unmet dependency.

⚠️ **GOTCHA 2**: This is explicitly NOT the same as "the specs pass." A tool with clean, tested internal classes but no way to actually invoke it end-to-end has not delivered what was asked for. Both parts of the Problem Statement are required — don't stop at part 1 and report success.

⚠️ **GOTCHA 3**: Use RH-400 as the real test case for the end-to-end demonstration — its blueprint, operational_data, and visual_definition (and its existing production art) are already the established reference case for this asset pipeline elsewhere in the project. Don't invent a different test unit.

⚠️ **GOTCHA 4**: Per standing guardrail — do not report "verified" or "complete" from static inspection alone. Both the spec run and the end-to-end prompt generation need real executed output shown as evidence.

## Files Involved
| File | Purpose |
|---|---|
| `tools/asset_generation/prompt_compiler.rb`, `profile_resolution_engine.rb`, `composition_refinery.rb` | Existing migrated classes — read, don't redesign |
| `tools/asset_generation/spec/*.rb` | Existing specs — get them running, don't rewrite them |
| `tools/asset_generation/README.md` | Should be updated with the actual working invocation instructions once confirmed |
| New: `tools/asset_generation/Gemfile` (or equivalent) — [FILL IN exact approach after Step 1 research] | Standalone execution setup |
| New or existing: an entry-point script — [FILL IN — confirm whether one already exists partially before building a new one] | The actual "generate a prompt" invocation |

## Acceptance Criteria
- [ ] `tools/asset_generation/` has a real, working standalone execution setup
- [ ] All three specs actually executed with real pass/fail counts shown
- [ ] A real entry point exists that takes RH-400's actual blueprint/operational_data/visual_definition files and produces a complete, real prompt string
- [ ] The generated prompt is shown as evidence (paste the actual output)
- [ ] README updated with the real, confirmed invocation instructions
- [ ] No architecture redesign, no new Asset Registry, no changes to Visual Definitions/Profiles/Render Templates — this is about making the existing tooling runnable and usable, not rebuilding it

## Stop Conditions
- If building the standalone environment reveals the three classes have real bugs beyond environment setup (not just "can't run"), stop and report — don't silently expand scope into fixing unrelated logic
- If RH-400's actual data files are missing or don't match what the tooling expects, report that gap rather than fabricating substitute data

## Dependencies
**Blocked by**: none
**Blocks**: any future confidence that this migration is genuinely complete and usable
**Related**: the original Phase 1 migration work; the superseded verify-tests-only task (premise found false, this task replaces it)

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
HANDOFF SUMMARY: [standalone execution working y/n] | [specs run, real counts] | [real prompt generated y/n, shown as evidence]
