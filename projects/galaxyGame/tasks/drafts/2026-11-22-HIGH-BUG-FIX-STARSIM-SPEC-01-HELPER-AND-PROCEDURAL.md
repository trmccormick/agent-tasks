---
status: draft
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
Task: <agent-tasks>/projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
       git ls-files --error-unmatch projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
     If the source is not tracked, stage the source first:
       git add projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md \
              projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
       test ! -e projects/galaxyGame/tasks/backlog/act02-local-bubble-expansion/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md && test -f projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/galaxyGame/tasks -name "2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md"
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


# TASK: StarSim Spec Part 1 of 4: Helper and Procedural Path
**Status**: DRAFT
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-11-22
**Last Updated**: 2026-11-23
**Author**: Claude (draft for human review)
**Revision 1 (2026-11-23)**: after review of the four-part set at 50b7d8a: startup reading order now defers to the Agent Dispatch Interface; baseline wording says "before creating the new spec file".

Part 1 of 4. These four parts replace the single task `2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md`, split into smaller steps. Do the parts in order, one at a time: all four edit the same spec file.

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Small, spec-only step with exact starting code.
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Startup order**: follow the READ FIRST list in the Agent Dispatch Interface above (GUARDRAILS, project README, SESSION_GUIDANCE, then this task file). That list is the mandatory startup contract and takes precedence over any other order.
2. **Also read**, after those files: `<agent-tasks>/README.md` (EXECUTOR Role section)
3. **This Task File**: Everything below
4. **Before starting**: None. This part creates the spec file.

> Agent MUST read in this order. Do not skip. The synthesis report is saved as an MD file BEFORE starting work.

---

## Context

StarSim (`galaxy_game/app/services/star_sim/procedural_generator.rb`) writes star-system JSON that a UI data loader will read. Generated systems have shown duplicate JSON keys (`"atmosphere"` and `:atmosphere` on the same hash) and duplicate or missing identifiers in the hybrid (known data plus generator) path. This task writes specs only; fixing the generator is a separate, later task. Specs that expose a bug are EXPECTED to fail on current code.

**What this part does**: Create the new spec file with a duplicate-key helper and check that the procedural (no template) path has no key collisions.

**Expected result on current code**: All three examples PASS on current code. They are the helper and regression guards for later parts.

**Relevant Architecture Docs** (context only): `docs/architecture/starsim/OVERVIEW.md`, `docs/architecture/starsim/PROCEDURAL_INTENT.md` (galaxyGame).

---

## Critical Information for This Task

### Credentials (if needed)
None.

### Architecture Gotchas

⚠️ **GOTCHA 1**: Specs only
- Do not change application code. A reproducible, expected failure is a valid result, not a reason to stop, retry or repair. Do not mark examples pending or skipped.

⚠️ **GOTCHA 2**: No local data files
- The spec must not read or write `data/json-data`. Seeds are written to a temporary directory and templates are inline (the generator normally loads templates from local, untracked JSON files, so `load_terraformable_templates` is stubbed in the shared setup).
- Do not copy the existing spec's `allow(File).to receive(:open)` line.

⚠️ **GOTCHA 3**: Report what you did not do
- If you could not run or verify something, say so. Do not report results you did not get.

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

Generated system JSON is not guaranteed to be a valid, consistent contract for the loader. This part: Create the new spec file with a duplicate-key helper and check that the procedural (no template) path has no key collisions.

**Current behavior**: All three examples PASS on current code. They are the helper and regression guards for later parts.

---

## Files Involved

### Primary Files
| File | Purpose |
|---|---|
| `galaxy_game/spec/services/star_sim/procedural_generator_output_contract_spec.rb` | The one spec file (created in this part) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/star_sim/procedural_generator.rb` | Code under test |
| `galaxy_game/spec/services/star_sim/procedural_generator_spec.rb` | Setup conventions for mocks |

### Migration
- [x] No migration needed

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and save your STATUS SYNTHESIS REPORT.
> Do not proceed to the next step until both are done and approved.

- Do not skip steps or reorder them.
- A clean result means reproducible, classified results. Expected failures (see Context) are clean results.
- All RSpec commands use the Docker wrapper shown. The container working directory is already `/home/galaxy_game`; do not `cd` into it. Never fabricate results.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

Follow Step 0 of the Agent Dispatch Interface exactly. Record the outputs in the synthesis report.

### Step 1 — Baseline and environment (before creating the new spec file)

Run from the galaxyGame checkout, then record the output in the report:

```bash
git rev-parse HEAD
git status --short
hostname
```

Capture the baseline of the existing spec, saving full output:

```bash
mkdir -p ~/scratch
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_spec.rb --format documentation 2>&1' > ~/scratch/starsim-baseline.txt; echo "rspec exit status: $?"
tail -40 ~/scratch/starsim-baseline.txt
```
If Docker reports the input device is not a TTY, rerun the identical command without `-it` and say so in the report.

Record the example counts, failure counts, and the exit status.

### Step 2 — Create the spec file

Create `galaxy_game/spec/services/star_sim/procedural_generator_output_contract_spec.rb` with the starting point below. It is an untested sketch based on the existing `procedural_generator_spec.rb`: if a mock raises "received unexpected message", copy the smallest matching `allow(...)` from that spec; if a method name or signature differs, adjust and say so in the report. Do not copy the existing spec's `allow(File).to receive(:open)` line.

```ruby
require 'rails_helper'

RSpec.describe StarSim::ProceduralGenerator, 'output contract', type: :service do
  # Paths of hashes that hold two keys equal after to_s (for example :atmosphere and
  # "atmosphere"). JSON.generate writes both keys, and parsers keep only the last one.
  def key_collisions(obj, path = '$')
    case obj
    when Hash
      own = obj.keys.map(&:to_s).tally.select { |_, n| n > 1 }.keys.map { |k| "#{path}.#{k}" }
      own + obj.flat_map { |k, v| key_collisions(v, "#{path}.#{k}") }
    when Array
      obj.each_with_index.flat_map { |v, i| key_collisions(v, "#{path}[#{i}]") }
    else
      []
    end
  end

  let(:templates) { [] }
  let(:mock_atmosphere_generator) { instance_double('AtmosphereGeneratorService') }
  let(:mock_hydrosphere_generator) { instance_double('HydrosphereGeneratorService') }
  let(:mock_material_lookup) { instance_double('Lookup::MaterialLookupService') }
  let(:mock_planet_name_service) { instance_double('Naming::PlanetNameService') }
  let(:generator) do
    described_class.new(nil, mock_atmosphere_generator, mock_hydrosphere_generator,
                        mock_material_lookup, mock_planet_name_service)
  end

  before do
    allow_any_instance_of(described_class).to receive(:load_terraformable_templates).and_return(templates)
    allow(mock_atmosphere_generator).to receive(:generate_composition_for_body).and_return(
      'composition' => { 'N2' => { 'percentage' => 78.0 } }, 'pressure' => 1.0, 'total_atmospheric_mass' => 5.0e18
    )
    allow(mock_hydrosphere_generator).to receive(:generate).and_return('total_water_mass' => 1.4e21, 'surface_coverage' => 0.71)
    allow(FileUtils).to receive(:mkdir_p) # the constructor creates the output folder
    allow_any_instance_of(NameGeneratorService).to receive(:generate_system_name).and_return('Test System')
    allow_any_instance_of(NameGeneratorService).to receive(:generate_star_name).and_return('Test Star')
    allow(mock_planet_name_service).to receive(:generate_planet_name).and_return('Test Planet')
  end

  describe 'key_collisions helper' do
    it 'finds a symbol/string key collision' do
      expect(key_collisions({ 'a' => { 'x' => 1, x: 2 } })).to eq(['$.a.x'])
    end

    it 'finds nothing in a clean hash' do
      expect(key_collisions({ 'a' => [{ 'x' => 1 }] })).to eq([])
    end
  end

  describe 'procedural path (no templates)' do
    it 'has no key collisions anywhere' do
      system = generator.generate_system_seed(num_stars: 1, num_planets: 3)
      expect(key_collisions(system)).to eq([])
    end
  end
end
```

### Step 3 — Run the new file

```bash
mkdir -p ~/scratch
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/star_sim/procedural_generator_output_contract_spec.rb --format documentation 2>&1' > ~/scratch/starsim-spec-01-run.txt; echo "rspec exit status: $?"
tail -40 ~/scratch/starsim-spec-01-run.txt
```
If Docker reports the input device is not a TTY, rerun the identical command without `-it` and say so in the report.

Run it twice more and confirm the same results. Record pass or fail for each example with the exact failure message.

### Final Step — Synthesis Report

Save the final stage of the synthesis report with the results above and request reviewer assessment. Do not commit.

---

## Acceptance Criteria
- [ ] `galaxy_game/spec/services/star_sim/procedural_generator_output_contract_spec.rb` exists and `git status` in galaxyGame shows no other changed file
- [ ] The baseline of `procedural_generator_spec.rb` was captured before the file was created, with example counts and exit status
- [ ] galaxyGame `git rev-parse HEAD`, `git status --short` and the machine name are in the report
- [ ] Three examples exist: two helper examples and one procedural-path example
- [ ] Results are the same across three runs; all three PASS (any failure is reported with its exact message, not fixed)
- [ ] No application, seed, or data file was modified
- [ ] The report states which machine ran the specs

---

## Stop Conditions — escalate to user immediately if:
- A load or syntax error, or a missing constant, that you cannot fix by editing the spec file
- Results differ across the three runs
- Other specs start failing
- The sketch cannot be adapted without changing application code
- Any architectural decision is required
- (Not a stop condition: a reproducible, expected failure of the new examples.)

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
git mv projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md projects/galaxyGame/tasks/completed/[YYYY-MM]/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md

# New/untracked file: stage the source first, then move with Git
git add projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md
git mv projects/galaxyGame/tasks/active/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md projects/galaxyGame/tasks/completed/[YYYY-MM]/2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md

git commit -m "chore: move 2026-11-22-HIGH-BUG-FIX-STARSIM-SPEC-01-HELPER-AND-PROCEDURAL.md to completed/"
```

---

---

## Documentation
- [x] No doc changes needed
- [ ] Report only: if you find a doc gap, list it in the completion report. Do not create docs or task files.

---

## Human Decisions (do not decide)

- **DECISION PENDING (Tracy)**: how to land specs that fail by design (known-bug tag, or hold the commit until the generator fix task is ready). Until decided, no commit of failing examples.
- **DECISION PENDING (Tracy)**: backlog subfolder (`act02-local-bubble-expansion` is a placeholder choice).

---

## Dependencies
**Blocked by**: none
**Blocks**: Part 2 of 4 (`...STARSIM-SPEC-02-TEMPLATE-PATH`)
**Related tasks**: `2026-11-20-HIGH-BUG-FIX-STARSIM-OUTPUT-CONTRACT-SPECS.md` (to be superseded by these four parts)

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
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found that were not in the task]

### Follow-up tasks needed
[List only; do not create files]

### Lessons learned
[What worked, what did not]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
