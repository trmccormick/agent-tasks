---
status: backlog
priority: HIGH
type: architecture
system_domain: UNITS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-08/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/2026-08/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md \\
         projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section is complete and accurate.
- [ ] All Step 0-N instructions are clear and actionable.
- [ ] Synthesis report template is provided and copy/paste ready.
- [ ] No placeholder text remains in Implementation Steps.
- [ ] Repository and task paths have been verified.
- [ ] Architecture Gotchas are specific.
- [ ] Acceptance Criteria are measurable.
- [ ] Dependencies and Blocked/Blocks relationships are clear.

---

# TASK: Research Power, Energy, and Generation Data Taxonomy

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: architecture  
**Created**: 2026-08-31  
**Last Updated**: 2026-08-31  

---

## Local Worker Triage Report

- **Template Conformance**: PASS — includes YAML frontmatter and the required Agent Dispatch Interface immediately afterward.
- **Docker Wrapper Check**: PASS — all optional application commands use the Docker wrapper; no bare local RSpec command is specified.
- **MVP Alignment**: VALID — this is a specification-health and data-taxonomy investigation.
- **MVP Impact Note**: Prevents inconsistent power, unit, structure, rig, and module definitions from entering the Galaxy Game data catalog.
- **Action Line**: NEEDS MANUAL REVIEW — confirm the task path and repository mount before local dispatch.

---

## Agent Assignment

**Assigned To**: Qwen local via Copilot  
**Why This Agent**: Requires filesystem, terminal, JSON, Git, and code-reference inspection.  
**Local attempts before cloud**: N/A  
**Supervision Level**: watched carefully  

---

## Prerequisites — READ FIRST

1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. This task file after Step 0 moves it to `active/`

The data repository is expected to be available in the container at:

```text
/home/galaxy_game/app/data/
```

The task-management repository is expected to be available on the host at:

```text
/Users/tam0013/Documents/git/agent-tasks/
```

If either location is unavailable, stop and report the exact path failure.

---

## Context

Galaxy Game uses separate blueprint and operational-data catalogs for ships, structures, units, rigs, modules, components, and resources. The current data appears to contain historical naming drift between `energy`, `power`, and `power_generation`, including possible overlap between `units/energy`, `units/power`, and `units/power_generation`.

A factory structure example establishes that structures may provide `unit_slots` and `module_slots`, while units may themselves contain rigs and modules. The purpose of this task is to inspect the actual filesystem, JSON payloads, loaders, and references so the taxonomy can be corrected without prematurely moving or deleting data.

**This task is research only. Do not modify application data, rename files, move catalog files, or delete directories.**

### Relevant Architecture Docs

Read these before analysis if they exist:

- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions.
- `docs/new_agent/rules/GUARDRAILS.md` — execution and safety rules.
- `docs/new_agent/README.md` — agent workflow and handoff conventions.

If any expected document is missing, record it as a documentation gap. Do not create a replacement document during this task.

---

## Critical Information for This Task

### Credentials

No credentials are required.

### Architecture Gotchas

⚠️ **GOTCHA 1: Do not treat directory names as authoritative entity types.**

- ❌ Wrong: Assume a file is a structure because it is under `power_generation`.
- ✅ Right: Inspect `template`, `metadata.type`, `category`, `subcategory`, IDs, slots, and construction data.
- Why: The current taxonomy may contain files placed under directories created by later conventions.

⚠️ **GOTCHA 2: Do not merge `energy`, `power`, and `power_generation` based on names alone.**

- ❌ Wrong: Rename every `energy` directory to `power`.
- ✅ Right: Compare unit slots, module slots, rig slots, functions, and loader behavior.
- Why: The factory example uses `energy` for unit slots and `power` for module slots, suggesting they may have different meanings.

⚠️ **GOTCHA 3: Do not modify the repository during investigation.**

- ❌ Wrong: Move, rename, delete, rewrite, or reformat JSON files.
- ✅ Right: Produce an evidence-based report and list proposed migrations separately.
- Why: Migration decisions require human review after the complete inventory is available.

⚠️ **GOTCHA 4: Do not treat all reactor outputs as directly comparable.**

- ❌ Wrong: Compare 5 MW thermal directly with 25,000 kW nominal output.
- ✅ Right: Record whether each value is thermal, electrical, gross, or net and identify missing conversion rules.
- Why: The compact reactor and micro-reactor may use different output semantics.

⚠️ **GOTCHA 5: Do not trust filenames to determine blueprint versus operational data.**

- ❌ Wrong: Assume every file ending in `_bp.json` is a blueprint.
- ✅ Right: Validate the filename against `id`, `template`, `metadata.type`, and `metadata.template_compliance`.
- Why: `compact_fusion_reactor_l1_bp.json` is named as a blueprint but declares operational-data metadata.

⚠️ **GOTCHA 6: Do not assume structures are the only possible hosts.**

- ❌ Wrong: Enforce a rigid structure → unit-only hierarchy.
- ✅ Right: Inspect whether ships, structures, units, rigs, and modules expose compatible slots.
- Why: A structure generally hosts units but may directly host rigs or modules; units may also contain rigs and modules.

### Multi-Domain / Multi-Tenant Routing

Not applicable. This is a local filesystem and repository investigation.

---

## 🔴 REQUIRED: Status Synthesis Report

Before inspecting application files or running research commands after Step 0, create this file:

```text
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
```

Use this exact structure and replace each instruction with the actual result:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH
**Status**: backlog → active
**Date**: 2026-08-31

### What I'm About to Do

Inventory the actual blueprint and operational-data filesystem, parse the relevant
JSON files, inspect code and loader references, and compare the energy/power/
power_generation taxonomy. The investigation will remain read-only and will
produce evidence for a later migration task.

### Files I'll Reference

| File or path | Purpose | Status |
|---|---|---|
| `/home/galaxy_game/app/data/blueprints/` | Blueprint catalog inventory | pending |
| `/home/galaxy_game/app/data/operational_data/` | Operational-data catalog inventory | pending |
| `/home/galaxy_game/app/data/blueprints/structures/power_generation/` | Structure-level power-generation records | pending |
| `/home/galaxy_game/app/data/blueprints/units/energy/` | Historical unit-level energy path | pending |
| `/home/galaxy_game/app/data/blueprints/units/power/` | Current unit-level power path | pending |
| `/home/galaxy_game/app/data/blueprints/units/power_generation/` | Possible duplicate unit-level path | pending |
| `compact_nuclear_reactor_bp.json` | Compact reactor comparison target | pending |
| `nuclear_micro_reactor_mk1_bp.json` | Micro-reactor comparison target | pending |
| `mars_industrial_nuclear_reactor_bp.json` | Possible Mars reactor overlap | pending |
| `compact_fusion_reactor_l1_bp.json` | Blueprint/operational-data mismatch target | pending |
| `compact_fusion_reactor_l1_data-2.json` | Operational-data comparison target | pending |
| `docs/new_agent/rules/DECISIONS.md` | Locked architecture decisions | pending |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution guardrails | pending |
| Lookup and loader source files found by search | Path and lookup behavior | pending |
| Related specs and fixtures found by search | Compatibility and regression references | pending |

### Prerequisites Completed

- ✅ Step 0: Task file moved to `active/` with `git mv`.
- ✅ Step 0: YAML status updated from `backlog` to `active`.
- ✅ Step 0: Exactly one task-file path verified with `find`.
- ✅ Read the agent-tasks README EXECUTOR section.
- ✅ Read the Galaxy Game project guide.
- ✅ Read this task file.
- ✅ Reviewed architecture gotchas.
- ✅ Confirmed this is a read-only investigation.
- ✅ Confirmed no credentials or multi-tenant domain are required.

### Expected Outcomes

The report will contain an evidence-based inventory of the actual directories and
JSON files, duplicate and near-duplicate candidates, filename/payload mismatches,
loader references, slot semantics, schema differences, and a recommended taxonomy.
No application data or source catalog file will be changed.

### Critical Gotchas I Will Avoid

- ❌ Moving or renaming files during inventory — instead, record proposed moves only.
- ❌ Treating `energy`, `power`, and `power_generation` as equivalent — instead, compare their slot and loader semantics.
- ❌ Treating filename suffixes as authoritative — instead, validate payload metadata.
- ❌ Comparing reactor output values without units — instead, classify thermal, electrical, gross, and net output.
- ❌ Assuming structures can only host units — instead, inspect unit, rig, and module slot support.

---

**SYNTHESIS COMPLETE.** Ready to proceed with PRIORITY 1: filesystem and JSON inventory.
```

Save the report before continuing with Steps 1–6. Do not modify catalog data.

---

## Problem Statement

The original directory-generation script defines:

```text
blueprints/structures/power_generation/
blueprints/units/energy/
blueprints/modules/energy/
blueprints/modules/power/
blueprints/rigs/energy/
blueprints/rigs/power/
```

It does not define:

```text
blueprints/units/power/
blueprints/units/power_generation/
```

The current repository reportedly contains a populated `blueprints/units/power/` directory and a separate `power_generation` location containing at least one reactor blueprint. The `energy` directory is no longer visible in the current working tree, creating uncertainty about whether it was renamed, removed, or replaced.

The factory example further shows:

```json
"unit_slots": [
  {"type": "energy", "count": 3}
],
"module_slots": [
  {"type": "power", "count": 2}
]
```

This may indicate that `energy` is an intended unit category while `power` is a module or rig category. That interpretation must be verified against the actual repository.

### Current Behavior

- Multiple names appear to represent related power assets.
- The current filesystem and the early directory-generation script do not agree.
- Reactor blueprints use inconsistent templates and metadata.
- It is unclear whether the new nuclear reactor records are distinct products, variants, or duplicates.
- It is unclear whether loaders use filesystem paths, IDs, categories, or a combination.

### Expected Behavior

The research must establish:

- The actual current directory tree.
- The authoritative entity type of each affected JSON record.
- The semantic meaning of `energy`, `power`, and `power_generation`.
- The containment/slot model for structures, ships, units, rigs, and modules.
- Whether the reactor records should remain separate.
- A safe, evidence-based recommendation for a later migration task.

---

## Files Involved

### Primary Files — read-only; do not edit

| File or path | Purpose |
|---|---|
| `/home/galaxy_game/app/data/blueprints/` | Inventory all blueprint paths |
| `/home/galaxy_game/app/data/operational_data/` | Inventory all operational-data paths |
| `/home/galaxy_game/app/data/blueprints/structures/power_generation/` | Inspect structure-level power records |
| `/home/galaxy_game/app/data/blueprints/units/energy/` | Verify whether the historical unit path exists |
| `/home/galaxy_game/app/data/blueprints/units/power/` | Inspect current power-unit records |
| `/home/galaxy_game/app/data/blueprints/units/power_generation/` | Verify whether a duplicate unit path exists |
| `compact_nuclear_reactor_bp.json` | Compare compact reactor definition |
| `nuclear_micro_reactor_mk1_bp.json` | Compare industrial micro-reactor definition |
| `mars_industrial_nuclear_reactor_bp.json` | Compare possible Mars duplicate |
| `compact_fusion_reactor_l1_bp.json` | Validate filename and payload type |
| `compact_fusion_reactor_l1_data-2.json` | Compare operational-data duplicate |

### Reference Files — read but do not edit

| File or path | Why it is needed |
|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Check locked taxonomy decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Confirm read-only and Docker rules |
| `app/services/` | Find data loaders and lookup services |
| `app/models/` | Find entities and composition relationships |
| `spec/` | Find tests and fixtures referencing affected IDs or paths |
| `config/` | Find data root and loader configuration |
| Existing structure blueprint containing `metal_smelter_facility_bp` | Verify slot and compatibility conventions |
| Original directory-generation script | Compare intended versus current directory structure |

### Migration

- [x] No migration in this task.
- [ ] Migration needed later: create a separate task after this report is reviewed.

---

## Implementation Steps

This task is read-only research. Do not edit data, source code, task files other than the required task status/report lifecycle, or documentation.

### Step 0 — Move task file to active/ and update status

From the `agent-tasks` repository root:

```bash
git mv projects/galaxy_game/tasks/backlog/2026-08/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md \\
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
```

Open the moved file and change:

```yaml
status: backlog
```

to:

```yaml
status: active
```

Verify there is exactly one task file:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \\
     -name "2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md"
```

Paste the output before proceeding.

### Step 1 — Read prerequisites and create synthesis report

Read the required README, project guide, architecture documents, and moved task file in the listed order.

Create:

```text
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
```

Use the synthesis structure defined above. Do not begin filesystem research until the synthesis report has been saved.

### Step 2 — Inventory the actual filesystem

Run the following through the Docker wrapper:

```bash
docker exec web bash -c 'find /home/galaxy_game/app/data/blueprints /home/galaxy_game/app/data/operational_data -type d -print | sort'
```

Then inventory all JSON files:

```bash
docker exec web bash -c 'find /home/galaxy_game/app/data/blueprints /home/galaxy_game/app/data/operational_data -type f -name "*.json" -print | sort'
```

Specifically report whether these paths exist:

```text
/home/galaxy_game/app/data/blueprints/units/energy
/home/galaxy_game/app/data/blueprints/units/power
/home/galaxy_game/app/data/blueprints/units/power_generation
/home/galaxy_game/app/data/blueprints/structures/power_generation
/home/galaxy_game/app/data/operational_data/units/energy
/home/galaxy_game/app/data/operational_data/units/power
/home/galaxy_game/app/data/operational_data/units/power_generation
```

Do not create missing directories.

### Step 3 — Parse and inventory JSON payloads

Parse every JSON file under the blueprint and operational-data roots. Report all syntax errors without modifying the files.

Extract these fields where present:

```text
path
filename
template
id
name
entity_type
unit_type
category
subcategory
metadata.type
metadata.template_compliance
operational_data_reference
operational_data_id
item_produced.id
unit_slots
rig_slots
module_slots
compatible_units
compatible_rigs
compatible_modules
recommended_units
recommended_rigs
recommended_modules
```

Use the following read-only parser command or an equivalent command:

```bash
docker exec web bash -c 'ruby -rjson -e '\''Dir["app/data/**/*.json"].sort.each do |file|
  begin
    data = JSON.parse(File.read(file))
    puts "#{file}\\t#{data["id"]}\\t#{data["template"]}\\t#{data.dig("metadata", "type")}"
  rescue JSON::ParserError => error
    warn "JSON_ERROR\\t#{file}\\t#{error.message}"
  end
end'\'''
```

Save the machine-readable inventory as:

```text
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-31-power-data-taxonomy-inventory.json
```

Do not write anything into `app/data`.

### Step 4 — Compare power and energy taxonomy evidence

Search the application, configuration, tests, and data for:

```bash
docker exec web bash -c 'rg -n -i "energy|power|power_generation|power generation|unit_slots|rig_slots|module_slots|compatible_units|compatible_rigs|compatible_modules" app config lib spec data 2>/dev/null'
```

Search for affected reactor identifiers:

```bash
docker exec web bash -c 'rg -n -i "compact_nuclear_reactor|nuclear_micro_reactor|mars_industrial_nuclear_reactor|compact_fusion_reactor|operational_data_reference|power_specs" app config lib spec data 2>/dev/null'
```

Determine whether the code treats these as:

- Filesystem paths.
- Entity types.
- Domains.
- Functional categories.
- Slot types.
- Resource IDs.
- Compatibility types.
- Migration aliases.

Document evidence for each conclusion.

### Step 5 — Compare nuclear reactor records

Compare these records by payload, not filename:

```text
compact_nuclear_reactor_bp.json
nuclear_micro_reactor_mk1_bp.json
mars_industrial_nuclear_reactor_bp.json
compact_fusion_reactor_l1_bp.json
compact_fusion_reactor_l1_data-2.json
```

For the fission reactors, compare:

```text
entity/template type
reactor architecture
thermal versus electrical output
mass
volume
dimensions
production time
construction facility
materials
fuel handling
technology requirements
skills and tools
variants
maintenance
environmental constraints
operational data references
item IDs
```

Explicitly identify whether these are:

- Separate products.
- Product variants.
- Historical duplicates.
- Unit versus structure definitions.
- Component versus unit definitions.
- Blueprint versus operational-data duplicates.

Report the output units exactly as written. Do not convert thermal to electrical output unless the data explicitly supplies the required efficiency.

Also inspect whether `mars_optimized` in the compact reactor overlaps with `mars_industrial_nuclear_reactor_bp.json`.

### Step 6 — Inspect composition and structure conventions

Locate the factory structure blueprint by ID:

```bash
docker exec web bash -c 'rg -l "metal_smelter_facility_bp" app data spec config 2>/dev/null'
```

Inspect its use of:

```text
unit_slots
module_slots
rig_slots
compatible_units
compatible_modules
compatible_rigs
recommended_units
recommended_modules
recommended_rigs
```

Determine whether:

- Structures normally host units.
- Structures may directly host rigs or modules.
- Units may host rigs or modules.
- Ships use the same slot system.
- Slot `type` values are category names, path names, or arbitrary compatibility labels.
- `energy` and `power` are intentionally different in the composition model.

### Step 7 — Prepare the research report

Write the final report to:

```text
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
```

The report must contain:

1. Executive findings.
2. Actual directory tree.
3. Directory existence table.
4. Complete affected JSON inventory.
5. Duplicate and near-duplicate candidates.
6. Reactor comparison.
7. Blueprint versus operational-data mismatches.
8. Energy versus power evidence.
9. Structure/unit/rig/module composition evidence.
10. Loader and code references.
11. JSON syntax-validation results.
12. Recommended canonical taxonomy.
13. Proposed migration plan for a future task.
14. Files requiring human decisions.
15. Documentation gaps.

The proposed migration plan must be advisory only. Do not apply it in this task.

### Step 8 — Verify read-only scope

Run:

```bash
git status --short
```

Confirm that no files under the Galaxy Game data catalog, application source, specs, or configuration were modified.

Confirm that exactly one task file exists:

```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \\
     -name "2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md"
```

Expected result: one path under `tasks/active/`.

No RSpec run is required because this task changes no application code. If any RSpec command is run for additional validation, it must use:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Do not run the full suite.

### Step 9 — Human review gate

Do not rename, move, delete, rewrite, commit, or push catalog files.

Do not create a migration task automatically.

Stop after saving the reports and ask for human review if:

- The loader semantics contradict the apparent folder taxonomy.
- A shared schema or base loader requires an architectural decision.
- The existing data contains duplicate IDs.
- The reactor records cannot be distinguished without gameplay decisions.
- The current task path or Docker data path is unavailable.
- Any source or data file was modified accidentally.

---

## Acceptance Criteria

- [ ] Task file is moved from backlog to active with `git mv`.
- [ ] YAML status is changed from `backlog` to `active`.
- [ ] `find` confirms exactly one task-file copy.
- [ ] Required README and architecture documents are read, or missing documents are reported.
- [ ] Actual blueprint and operational-data directory trees are recorded.
- [ ] Existence of `energy`, `power`, and `power_generation` paths is explicitly reported.
- [ ] Every affected JSON file is parsed successfully or reported with an exact syntax error.
- [ ] Duplicate IDs and near-duplicate reactor names are identified.
- [ ] The compact and micro nuclear reactor records are compared across the required dimensions.
- [ ] The Mars reactor and compact reactor’s Mars variant are compared.
- [ ] Blueprint/operational-data filename and payload mismatches are reported.
- [ ] Structure, unit, rig, and module slot behavior is documented from actual repository evidence.
- [ ] Loader and lookup references are documented.
- [ ] Machine-readable inventory is saved to the summaries directory.
- [ ] Final synthesis report is saved to the summaries directory.
- [ ] No application data, source code, specs, or configuration files are modified.
- [ ] No migration is performed.
- [ ] No full RSpec suite is run.

---

## Stop Conditions — escalate to user immediately if

- The task file cannot be moved with `git mv`.
- More than one task-file copy remains after Step 0.
- The Docker container or data path is unavailable.
- Any requested architecture document is missing and its absence affects the investigation.
- A loader uses conflicting definitions of `energy`, `power`, or `power_generation`.
- Duplicate IDs would make a migration destructive.
- Reactor output units cannot be interpreted without a gameplay decision.
- Any source or data file is modified accidentally.
- The research requires changing a shared schema or loader.
- The investigation would require renaming or deleting files.

---

## Commit Instructions

No application or catalog changes are authorized.

If only the required task lifecycle and summary files changed, do not commit until human review approves the research report.

Git commands must run on the host, never inside Docker:

```bash
git status --short
git diff --stat
git diff -- projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
git diff -- projects/galaxy_game/summaries/2026-08-31-power-data-taxonomy-inventory.json
```

Do not run:

```bash
git add .
git commit
git push
```

without explicit human approval.

After approval, use specific paths only:

```bash
git add projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md \\
        projects/galaxy_game/summaries/2026-08-31-power-data-taxonomy-inventory.json \\
        projects/galaxy_game/tasks/active/2026-08-31-HIGH-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md

git commit -m "docs: research power and energy data taxonomy"
```

Do not move the task to completed until the human accepts the research report.

---

## Documentation

- [ ] No documentation update is authorized in this research task.
- [ ] Flag missing or contradictory taxonomy documentation in the final report.
- [ ] Create a separate documentation task later if the taxonomy decision requires permanent project documentation.

---

## Dependencies

**Blocked by**: none  
**Blocks**: future power/energy catalog migration task  
**Related tasks**: none currently identified  

---

## Completion Report

*Filled in by the implementing agent after completion.*

**Completed by**: Qwen local via Copilot  
**Completion date**: 2026-08-31  
**Final test result**: RSpec not run; JSON validation and read-only verification completed

### What was changed

- `projects/galaxy_game/summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md` — research findings.
- `projects/galaxy_game/summaries/2026-08-31-power-data-taxonomy-inventory.json` — machine-readable inventory.
- Task lifecycle file moved from `tasks/backlog/2026-08/` to `tasks/active/`.

### Issues discovered

Record exact directory, schema, duplicate, loader, and documentation issues discovered during the investigation.

### Follow-up tasks needed

Record future migration, schema-normalization, loader, or documentation tasks. Do not create those task files during this task.

### Lessons learned

Record what the filesystem, templates, loaders, and slot conventions reveal about future data-generation tasks.

---

## Handoff Summary

HANDOFF SUMMARY: Read-only inventory of energy/power/power_generation blueprints and operational data completed | no catalog migration performed | human review of synthesis report required before creating migration tasks
