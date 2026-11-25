---
status: active
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project_name]`, `[project]`, `[SUBFOLDER]` and `[FILENAME]` in the file when the task is drafted, so the block carries no other placeholders at dispatch; the only substitution made at dispatch is `<agent-tasks>`. If the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: Galaxy Game
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md \
              projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
       test ! -e projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md && test -f projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/galaxyGame/README.md
  3. <agent-tasks>/projects/galaxyGame/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/galaxyGame/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Update the report after Step 0/before investigation, after investigation/before implementation, and after implementation/verification.
  In chat, provide only the report path and concise status; do not paste the report or raw command output.

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.


---

---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## � DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/act02-local-bubble-expansion/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

---


# TASK: StarSim Output Contract Specs (tests only)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-11-20
**Last Updated**: 2026-11-21
**Author**: Claude (draft for human review)

**Revision 1 (2026-11-21)**: author revision after a document-only technical review (Perplexity, REVISE). Changes: expected-failure handling, baseline sequencing, evidence capture, definitions for identifier scope and seed preservation, hybrid and template stimuli, script-call wording, suite and completion-authority wording, report-only documentation gaps. Not changed: dispatch interface block and other template text, scope, pending human decisions. **Revision 2 (2026-11-21)**: final author edits after independent reviews (document-only reviews: REVISE then GO; repository-read review of this draft at agent-tasks `2c23fe2` and galaxyGame `08f10fa`: GO). Changes: `status` set to `backlog` on Tracy's instruction; the confirmed script call and the additional mixed-key sites added to the notes below. No other text changed; the dispatch interface block is unchanged.

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Well-specified, spec-only task with explicit paths; local Qwen is the default executor.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `<agent-tasks>/README.md` (EXECUTOR Role section)
2. **Project Guide**: `<agent-tasks>/projects/galaxyGame/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. The synthesis report is saved as an MD file BEFORE starting work.

---

## Context

StarSim (`galaxy_game/app/services/star_sim/procedural_generator.rb`) writes star-system JSON that the UI prototype's data loader will read. Test runs on 2026-11-20 produced systems with duplicate JSON keys (two `"atmosphere"` keys on template-derived terrestrial planets), and the hybrid (known data + generator) completion path produced a duplicate identifier and seed bodies without identifiers. The parts of this task are small and independent: this task only writes the specs that define "fixed". The generator fix is a separate, later task.

Generated systems are prototype inputs. Only Sol is a fixed, hand-authored system; real systems (for example Alpha Centauri) are completed from a known-data seed; the rest are fully procedural. The specs must not assume any particular generated content.

**Relevant Architecture Docs** — read before starting:
- `rules/GUARDRAILS.md` (agent-tasks) — execution rules
- `docs/architecture/starsim/OVERVIEW.md` and `docs/architecture/starsim/PROCEDURAL_INTENT.md` (galaxyGame) — StarSim intent; context only

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials (if needed)
None.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Mixed symbol and string keys
- ❌ Wrong: check only the serialized JSON text, or only one key type
- ✅ Right: walk the generated Ruby hash recursively and fail if any hash has two keys that are equal after `to_s` (for example `:atmosphere` and `"atmosphere"`), AND serialize with `JSON.generate` and parse it back
- Why: template data is loaded with string keys, then the generator assigns symbol keys (`planet_data[:atmosphere] = ...`). `JSON.generate` writes both; parsers silently keep the last one.

⚠️ **GOTCHA 2**: Specs must not depend on local `data/json-data` files
- ❌ Wrong: read or write seed or generated files under `data/json-data` from the spec
- ✅ Right: build seed hashes inline and write any seed file to a temporary directory. The generator loads its terraformable templates from `GalaxyGame::Paths::TEMPLATE_PATH` (`alien_world_templates_v1.1.json`, then `v1.json`); per the author's tree check at `08f10fa` those JSON files are not tracked in git, so on a fresh clone the template list may be empty. The template-path examples must therefore supply an inline template (for example by stubbing `load_terraformable_templates`) instead of relying on local template files. Verify this in Step 1 and report if it cannot be done.
- Why: specs must pass on a fresh clone and must not read or write the local data tree.

⚠️ **GOTCHA 3**: Two hybrid completion methods exist
- `generate_hybrid_system_from_seed` is name-based (hard-coded to Proxima Centauri b). `generate_hybrid_system_from_seed_generic` is data-driven. At galaxyGame `08f10fa`, `scripts/generate_hybrid_system.rb` line 27 calls `pg.generate_hybrid_system_from_seed(seed_path)` (the name-based method), confirmed by a repository-read review; re-confirm against the current code.
- Specs target the generic method. Confirm and report which method each entry point calls; do not change the script.

⚠️ **GOTCHA 4**: Randomness
- ❌ Wrong: specs that depend on `rand` and sometimes pass
- ✅ Right: stub `rand` / `TERRAFORMABLE_CHANCE` (currently 0.4) so the template path and the procedural path are each forced deterministically, and do the same for the hybrid examples (the per-star fill uses `rand` for orbit distances)
- The existing `procedural_generator_spec.rb` builds the generator with injected `instance_double` services and stubs file operations; follow that convention. A stubbed atmosphere generator that returns a hash is enough to reproduce the duplicate-key collision, because the collision comes from the key types, not the content.

⚠️ **GOTCHA 5**: Tests only
- Do not fix application code in this task. Specs that expose the known problems are EXPECTED to fail on current code. Do not mark them pending or skip them. A confirmed, reproducible failure of an expected contract example is a valid result, not a reason to stop, retry or repair.

---

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation or Implementation)

After completing the dispatch Step 0 move and YAML status update, but before investigation commands, browser navigation, or application-file changes, create the initial **synthesis report** as a Markdown file. Step 0 Git commands are the only commands permitted before this initial report. Update the same report after investigation and before implementation, then again after implementation and verification.

**Synthesis Report Template** (save as MD file; do not paste the report or raw command output into chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: [name from filename]
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### Stage 1 — Initial understanding and Step 0
[Record exact Step 0 Git commands/results, source/destination verification, YAML status, task objective, boundaries, unknowns, and planned evidence.]

### Stage 2 — Investigation findings (complete before implementation)
[Record verified source paths, investigation/reproduction evidence, root cause or unresolved status, proposed correction, exact edit paths, and test plan. Obtain human approval of the proposed approach before implementation.]

### Stage 3 — Implementation and verification
[After implementation, append actual changes, exact test commands/results, runtime evidence, acceptance disposition, documentation updates, and remaining issues.]

### What I'm About to Do
[2-3 sentences: the goal, verification method, and success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output recorded in this report)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

In chat, provide only the report path and a concise status update. Do not start implementation until the investigation report is complete and the human has approved the proposed approach.

---

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

**You MAY NOT commit, push, or mark any task step as "complete" until BOTH of the following conditions are met:**

1. **Reviewer has issued a GO disposition** — a reviewer has assessed your work and disposed it as one of: `GO`, `REVISE`, or `STOP`. See REVIEW_AGENT_GUIDE.md for reviewer authority definitions.
2. **Human authorization** — Requires an explicit human message that authorizes commit (for example, 'commit it') and an explicit human message that authorizes push (for example, 'push it'). Authorization to commit does not imply authorization to push, and authorization to push does not imply authorization to commit.

Confirming a command in an agent's own approval prompt counts as authorization only after a reviewer GO is on record and the human explicitly authorizes that particular operation; before a GO, a click does not authorize commit or push.

### What This Means In Practice

| You CAN do | You CANNOT do |
|---|---|
| Implement changes per task instructions | Commit/push/mark-complete without both conditions above |
| Run tests and verify your changes locally | Assume reviewer GO is sufficient to proceed to commit |
| Create synthesis and evidence artifacts | Proceed to the next task step if review disposition is REVISE or STOP |
| Report results and request review | Override a reviewer's REVISE or STOP disposition |

### Review-Gate Workflow

1. **Complete all implementation and verification steps** listed below this section
2. **Create evidence artifacts** (test output, synthesis reports)
3. **STOP** — save all evidence in the synthesis/evidence report. In chat, provide only the report path and a concise update stating: *"Implementation and verification are complete. Requesting reviewer assessment."* Do not paste the report or raw command output into chat.
4. **Wait for reviewer disposition** (GO / REVISE / STOP)
5. If **REVISE**: address the listed issues, re-test, return to step 3
6. If **STOP**: do not proceed; await human guidance
7. If **GO**: wait for an explicit human message that authorizes commit. Push needs a separate, later human message that authorizes push.
8. Once **both** conditions are satisfied: proceed to Commit Instructions below

> **This checkpoint is NOT optional.** It applies to all implementation tasks regardless of size or perceived risk. The reviewer does not commit directly; only the human authorizes commits.

---

---

## Problem Statement

Generator output is not guaranteed to be a valid, consistent JSON contract for the loader.

Known problems (line numbers from galaxyGame `08f10fa`; verify before relying on them):
1. **Duplicate keys.** `procedural_generator.rb` ~lines 558 and 642 set `planet_data[:atmosphere]` on hashes that may already hold `"atmosphere"` (template planets, via `generate_from_template`). Same mixed-key pattern for `:hydrosphere` and `:biosphere`; confirm whether those also collide. The same symbol-key assignments for `:atmosphere` and `:hydrosphere` also appear at about lines 809/812, 865/868 and 918/921 (other body-type builders); check whether those hashes can also already hold string keys. Investigation only, not an asserted bug.
2. **Hybrid identifiers.** `generate_hybrid_system_from_seed_generic` (~line 169): seed bodies are tagged `from_seed` but never given an `identifier`; the per-star fill (~lines 230 to 240) calls `generate_planet_identifier(identifier, @planet_counter + 1)` without incrementing `@planet_counter`, so two generated planets can share an identifier (observed: two bodies named `AC-01-b`).
3. **Seed preservation.** The generic method must keep every seed body's fields (for example `notable_traits`) unchanged.

**Current behavior**: serialized systems can contain duplicate keys; hybrid output can contain missing and duplicate identifiers.
**Expected behavior**: no duplicate keys at any depth; every body and moon has a unique non-empty `identifier`; seed bodies are preserved.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/spec/services/star_sim/procedural_generator_output_contract_spec.rb` | NEW spec file (only file you create) | whole file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/star_sim/procedural_generator.rb` | Code under test: `generate_from_template`, `generate_terrestrial_planets`, `generate_hybrid_system_from_seed_generic` |
| `galaxy_game/spec/services/star_sim/procedural_generator_spec.rb` | Existing conventions for building the generator in specs |
| `galaxy_game/spec/services/star_sim/system_generator_service_spec.rb` | Related existing coverage |
| `scripts/generate_hybrid_system.rb` | Which hybrid method it calls (report only) |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and save your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- For this task a clean result means reproducible, classified results. Expected contract failures (see Step 3) are clean results.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

Follow Step 0 of the Agent Dispatch Interface exactly (git mv from `backlog/act02-local-bubble-expansion/` to `active/`, set `status: active`, verify exactly one copy exists with `find`). Record the outputs in the synthesis report.

### Step 1 — Investigate and capture the baseline (no edits to the application repository)

First capture the baseline BEFORE creating any file. Save output outside the galaxyGame repository, in `~/scratch` (create it if missing):

```bash
mkdir -p ~/scratch
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_spec.rb 2>&1' > ~/scratch/starsim-baseline.txt; echo "rspec exit status: $?"
```

If Docker reports the input device is not a TTY, rerun the identical command without `-it` and note that in the report. Record the example and failure counts, the exit status, and the identity of any failing examples.

Then read the reference files. Confirm or correct problems 1 to 3 against the current code. Check whether `:hydrosphere` and `:biosphere` collide too (investigation only, not an asserted bug). Verify the template-loading and hybrid-entry-point questions above. Confirm whether generator setup reads anything under the local data tree beyond the templates. Optional: confirm whether `planet_data[:magnetosphere_strength]` read from a string-keyed hash is always nil; only if confirmed, plan one extra spec. Record findings and the planned spec list in Stage 2 of the synthesis report and wait for human approval.

### Step 2 — Write the specs (after approval)

Create the single new spec file. One small example each (a failing example may stop at its first failed assertion; state which later assertions were not reached).

Definitions used below:
- **Identifier scope**: every entry in `stars`, every body in every `celestial_bodies` category, and every nested moon. Uniqueness is checked across that whole set.
- **Seed preservation**: every key and value present on a seed body is present and equal in the output. Adding `from_seed` and an `identifier` is allowed; report any other added or changed key without failing on it.
- **Non-vacuity**: assert the population under test is non-empty before asserting a property of it.
1. Procedural system (any seed): recursive hash walk finds no hash with two keys equal after `to_s`.
2. Template path forced (inline template, stubbed randomness): at least one planet has `from_template == true`, and every such planet has exactly one atmosphere key.
3. Procedural system: `JSON.generate` then `JSON.parse` succeeds, and every body and moon has a unique, non-empty `identifier` across the system.
4. Hybrid generic, temporary seed (inline hash written to a tmp dir, stubbed randomness): three stars, Proxima b with `notable_traits` including `"Ancient Jumpgate Relic"`, Proxima c (both with `parent_star` set to Proxima's identifier). Alpha Centauri A and B have no planets in the seed, so the per-star fill must add at least two generated planets; assert that at least two generated planets exist before asserting uniqueness. Assert both Proxima planets are present with seed preservation as defined above; every entry in the identifier scope has a non-empty `identifier`; all identifiers are unique; `from_seed` is true on seed bodies and not on generated ones.
5. Hybrid output passes the same duplicate-key check as example 1.

### Step 3 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_output_contract_spec.rb 2>&1 | tail -40'
```

Capture full output (not a tail) outside the repository, with the exit status:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_output_contract_spec.rb --format documentation 2>&1' > ~/scratch/starsim-contract-run-1.txt; echo "rspec exit status: $?"
```

Run it three times (run-1, run-2, run-3) and confirm the same examples fail or pass for the same causes (incidental output such as timing may differ). Record pass or fail for each example with the exact failure message. Then rerun `spec/services/star_sim/procedural_generator_spec.rb` the same way and compare with the Step 1 baseline: same counts and the same failing examples, if any.

Verification succeeds when results are reproducible and classified. Expected contract failures are not retried or repaired and do not trigger the "same failure persists" stop rule. Stop for: load or syntax errors, missing constants, differing results across the three runs, new failures in other specs, or being unable to exercise the intended code path.

Expected (not guaranteed; report actuals): examples 1, 2 and the identifier assertions of example 4 fail on current code; example 3 and example 5 likely pass.

### Step 4 — Synthesis Report (before committing anything)

Save the final stage of the synthesis report (Stage 3) with the results above and request reviewer assessment. Do not commit.

---

## Acceptance Criteria
- [ ] One new spec file exists at the path above; `git status` in the galaxyGame repository shows no other changed file (task moves and synthesis reports in the agent-tasks repository are outside this boundary)
- [ ] The baseline was captured before the spec file was created
- [ ] Examples 1 to 5 exist as described; no example is skipped, pending, or conditional
- [ ] Example outcomes and failure causes are the same across three consecutive runs
- [ ] For each example the report states pass or fail on current code, with the exact failure message, taken from the saved full output, and notes any assertions not reached
- [ ] `procedural_generator_spec.rb` has the same counts and the same failing examples (if any) as the Step 1 baseline
- [ ] The report states which hybrid method `scripts/generate_hybrid_system.rb` calls (confirmed by reading it)
- [ ] No seed, `data/json-data`, or application files modified, and the spec does not read or write the local data tree (template-path examples use an inline template)
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger). The report classifies new-spec failures as expected contract failures and lists any other failure separately. Task completion is authorized by Tracy separately from any commit or push.

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies
- A spec cannot be written without changing application code
- (Not a stop condition: a reproducible, expected failure of the new contract examples.)

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Before running ANY of the commands below, confirm:
> - [ ] Reviewer has issued GO disposition (not just pending)
> - [ ] Human has explicitly authorized the command I am about to run (commit or push) in a message that names it
> - [ ] Both conditions documented in your completion report
>
> If either condition is unmet, DO NOT proceed. Wait for the missing authorization.

Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md projects/galaxyGame/tasks/completed/2026-11/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md

# New/untracked file: stage the source first, then move with Git
git add projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md
git mv projects/galaxyGame/tasks/active/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md projects/galaxyGame/tasks/completed/2026-11/2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md

git commit -m "chore: move 2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md to completed/"
```

---

---

## Documentation
- [x] No doc changes needed
- [ ] Flag doc gap: if `docs/architecture/starsim/` does not describe the output JSON contract, list it in the completion report. Report only: do not create the doc and do not create a task file.

---

## Human Decisions (do not decide)

- **DECISION PENDING (Tracy)**: how to land specs that fail by design (for example tag as known-bug, or hold the commit until the fix task is ready). Until decided, no commit.
- **DECISION PENDING (Tracy)**: backlog subfolder for promotion (`act02-local-bubble-expansion` is a placeholder choice).

---

## Dependencies
**Blocked by**: none
**Blocks**: StarSim output contract fix (not yet drafted; to be drafted from this task's Stage 2 evidence)
**Related tasks**: `galaxy-game-ui/tasks/drafts/2026-11-19-HIGH-FEATURE-LANDING-PAGE-SYSTEM-OVERVIEW.md` (UI work paused pending generator contract)

---

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
