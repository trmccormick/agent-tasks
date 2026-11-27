---
status: draft
resume: false
priority: MEDIUM
type: documentation
system_domain: MARKET_DASHBOARD
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Fill `[project_name]`, `[project]`, `[SUBFOLDER]` and `[FILENAME]` in the file when the task is drafted, so the block carries no other placeholders at dispatch; the only substitution made at dispatch is `<agent-tasks>`. If the project's backlog is flat (no subfolders), omit the `[SUBFOLDER]/` segment from the paths. Change nothing else.

```
You are **Implementation Agent**.

Project: EVE Dashboard
Task: <agent-tasks>/projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md

STEP 0 — VERIFY, STAGE IF NEEDED, THEN MOVE WITH GIT MV:
  Run from the agent-tasks repository root.

  1. Inspect the source Git state:
       git status --short -- projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
       git ls-files --error-unmatch projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     If the source is not tracked, stage the source first:
       git add projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     Preserve all pre-existing changes; do not discard or overwrite them.

  2. Move the task:
       git mv projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md \
              projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md

  3. Verify the move:
       git status --short
       git ls-files --error-unmatch projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
       test ! -e projects/eve-dashboard/tasks/backlog/market/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md && test -f projects/eve-dashboard/tasks/active/2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
     Confirm the source is absent, destination exists, and Git tracks the destination.

  4. Open the active task file and change YAML status from backlog to active. Verify the updated status.

  5. Record Git-state inspection, any staging command, git mv, verification outputs, and YAML status change in the synthesis report. Do not begin investigation or implementation until Step 0 and the initial synthesis report are complete.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when moving the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv.
  - New/untracked file: git add the source first, then git mv the source to the destination.
  - Never use plain mv or cp for task lifecycle moves.
  - Never leave stale copies in the source folder.
  - Verify with: find agent-tasks/projects/eve-dashboard/tasks -name "2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md"
    Exactly ONE result should exist. Record this output in the synthesis/evidence report.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/eve-dashboard/README.md
  3. <agent-tasks>/projects/eve-dashboard/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting investigation.
  Summaries path: <agent-tasks>/projects/eve-dashboard/summaries/
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

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/market/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/market/` with `status: backlog`.

See `PLANNING_AGENT_SESSION_START.md` § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

- [ ] Acceptance criteria are clear, measurable, and evidence-based
- [ ] Scope is bounded to open-orders restoration inventory and directly relevant integrations
- [ ] Dependencies and related tasks are checked against `status.md`
- [ ] No contradictions remain between task sections or with the dispatch contract
- [ ] Repository-specific paths and branch state are verified by the assigned local investigator
- [ ] Reviewer disposition is GO
- [ ] Human explicitly approves moving this task to backlog

**Once ALL boxes are checked:** the human may move this task from `tasks/drafts/` to `tasks/backlog/market/` and change `status: draft` to `status: backlog`.

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface is complete and accurate; only `<agent-tasks>` remains for dispatch-time substitution
- [ ] Step 0 and read-only inspection instructions are clear
- [ ] Synthesis report template is complete and copy/paste ready
- [ ] No unresolved placeholders remain outside the permitted dispatch substitution
- [ ] Referenced paths have been checked in the local clone
- [ ] Architecture gotchas and prohibited actions are explicit
- [ ] Acceptance criteria are measurable
- [ ] Dependencies and blocked/blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

## Agent Assignment (Human-filled, not seen by agents)

- **Assigned To**: Qwen local via GitHub Copilot (primary)
- **Why This Agent**: Repository-aware read-only inspection of the local clone and both existing refs is required.
- **Local attempts before cloud**: N/A — local primary assignment
- **Supervision Level**: watched carefully (first inventory pass)

Do not dispatch until reviewer GO and separate human approval. No web research is assigned to Qwen in this task.

## Prerequisites — READ FIRST (Sequential Order)

1. `<agent-tasks>/rules/GUARDRAILS.md`
2. `<agent-tasks>/projects/eve-dashboard/README.md`
3. `<agent-tasks>/projects/eve-dashboard/SESSION_GUIDANCE.md` (if present)
4. `<agent-tasks>/projects/eve-dashboard/wiki/market/README.md`
5. This task file

Read in order. Do not infer local working-tree state from GitHub or prior handoffs.

## Context

EVE Dashboard previously had a multi-character Market open-orders feature on `local/improvements`. The current working branch is intended to remain `local/docker-customizations`, preserving its completed Docker/environment work. This task inventories the existing implementation, compares relevant code and prior findings, and provides evidence for wiki refinement and narrowly scoped follow-on tasks; it does not implement or redesign the feature.

**Relevant intent baseline**: `projects/eve-dashboard/wiki/market/README.md`. Code and branch evidence must be distinguished from design intent and historical task claims.

## Critical Information for This Task

### Credentials

No credentials are required. Do not read, print, copy, or report secrets, tokens, credential values, or `.env` contents. Do not make authenticated or unauthenticated live ESI requests.

### Architecture Gotchas

1. **Current branch is the base.** `local/improvements` is reference evidence only; do not merge or cherry-pick it.
2. **Per-order location matters.** Identify the actual source of each order's location/region/station/structure data. Do not assume Jita or substitute a universal market location.
3. **Character orders are not regional orders.** Trace the authenticated character open-orders endpoint separately from regional order-book endpoints.
4. **Open orders are not sales history.** Keep wallet-journal market sales, mining ledger, and price history outside initial restoration.
5. **Settings disclosure is app-wide.** Do not infer that Market order records are sent to Fuzzwork from the Settings disclosure alone; trace the Market and price-provider call chains.
6. **Read-only means no runtime side effects.** Do not start services or make live API calls to prove behavior.

### Multi-Domain / Multi-Tenant Routing

N/A. This is a local single-user application inventory; do not run the application.

## 🔴 REQUIRED: Status Synthesis Report (Before Investigation)

Create `projects/eve-dashboard/summaries/2026-11-26-MARKET-RESTORATION-INVENTORY.md` before investigation, after completing the task lifecycle move in Step 0. Update the same report after investigation. Because this is a read-only task, Stage 3 records report validation, closeout, and confirmation that no application-repository files were changed; no implementation is authorized.

### Synthesis Report Template

```markdown
# Market Restoration Inventory — Synthesis Report

**Task**: 2026-11-26-MEDIUM-DOCUMENTATION-MARKET-RESTORATION-INVENTORY.md
**Status**: draft → backlog → active → completed (actual transitions only)
**Date**: YYYY-MM-DD

## Stage 1 — Initial understanding and Step 0
- Record exact agent-tasks repository path and application repository path.
- Record current branch, HEAD SHA, reference branch SHA, and whether each inspection uses committed trees, working-tree changes, or both.
- Record working-tree status and identify relevant uncommitted/untracked files without altering them.
- Record the exact lifecycle move, YAML status update, verification commands, and single task-file search result.
- State objective, boundaries, constraints, unknowns, and planned evidence.

## Stage 2 — Investigation findings
- Record branch comparison, paths/functions, line references, and evidence labels.
- Separate direct inspection, historical claims, and unverified/runtime-only behavior.
- Include data-flow chains, branch matrix, legacy-task findings, capability matrix, wiki-heading recommendations, and unresolved decisions.
- Propose follow-on task sequence with goal, dependency, file boundary, and verification for each task.
- State that no application code or runtime state was changed.

## Stage 3 — Report validation and closeout
- Validate report completeness against every deliverable and acceptance criterion.
- Record exact read-only verification commands and results.
- Confirm no application-repository files were changed and list any agent-tasks lifecycle/report/closeout changes.
- Record review status and remaining human decisions. Do not commit, push, or mark complete without the required review GO and explicit human authorization.

## What I'm About to Do
A read-only comparison of the current and reference branches, tracing open orders and only directly relevant shared integrations. Success means a path/function-backed inventory that can support wiki updates and small follow-on tasks without changing application files.

## Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| Market wiki README | Intent baseline | pending |
| Current branch Market/integration files | Current implementation | pending |
| Reference branch Market files | Historical implementation evidence | pending |
| Four legacy Market task records | Historical findings | pending |

## Prerequisites Completed
- [ ] Step 0 lifecycle move and YAML update recorded
- [ ] GUARDRAILS.md read
- [ ] Project README and SESSION_GUIDANCE.md read (if present)
- [ ] Market intent wiki read
- [ ] This task read

## Expected Outcomes
An evidence-backed inventory and minimal, dependency-ordered restoration task proposal.

## Critical Gotchas I Will Avoid
- No branch switching, runtime execution, live ESI calls, or secret disclosure.
- No Jita assumption and no conflation of character open orders with regional orders or wallet sales.
- No implementation, wiki edits, or changes to existing task lifecycle/status.

**SYNTHESIS COMPLETE.** Ready to begin read-only investigation.
```

In chat, provide only the report path and a concise status. Do not paste raw command output.

## 🔴 REVIEW GATE — STOP BEFORE COMMITTING (MANDATORY)

This task is read-only and is not expected to modify application code. Nevertheless, do not commit or push any report/task/closeout changes until a reviewer has issued GO and the human has explicitly authorized the specific Git operation. A reviewer GO does not itself authorize commit or push; commit and push require separate human authorization. Follow `SESSION_CLOSEOUT.md` for closeout and report any required `status.md` update.

## Problem Statement

The earlier Market feature must be restored without losing the current branch's Docker/environment work or importing obsolete assumptions. Before implementation tasks are drafted, the team needs a verified comparison of the two local branches, the original open-orders data path, the relevant current infrastructure, and the existing Market task history.

**Current behavior**: Not established by this inventory task; inspect source and distinguish source-level behavior from runtime behavior.  
**Expected outcome**: A read-only evidence report sufficient to update the Market wiki and draft narrow implementation tasks.

## Files Involved

### Primary Files — inspect only; do not edit

| File or path | Purpose | Branch/status |
|---|---|---|
| `local/docker-customizations` application tree | Current base implementation | Verify locally |
| `local/improvements` application tree | Reference Market implementation | Verify locally |
| `projects/eve-dashboard/wiki/market/README.md` | Market intent baseline | Read only |
| `projects/eve-dashboard/tasks/active/2026-10-01-CRITICAL-BUG-MARKET-ORDERS-EMPTY-PAGE.md` | Prior open-orders investigation | Verify actual location |
| `projects/eve-dashboard/summaries/2026-10-01-MARKET-BUG-SYNTHESIS.md` | Prior investigation evidence | Verify actual location |
| `projects/eve-dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3-MINING-AND-MARKET-SALES.md` | Separate mining/wallet-sales scope | Verify actual location |
| `projects/eve-dashboard/tasks/backlog/2026-09-04-HIGH-FEATURE-PHASE3B-PRICE-TRACKING.md` | Separate price-history scope | Verify actual location |

### Reference Files — read only

Inspect relevant app config, ESI client, sync orchestration, character/token handling, feature toggles, DB helpers/schema, Market modules/templates/static assets, routes, tests, Settings disclosure, Fuzzwork integrations, and directly connected price consumers. Record exact paths and functions discovered rather than assuming this list is exhaustive.

### Migration

N/A — no schema or application changes are permitted.

## Scope and Required Investigation

Read-only inspection only. First locate and record the application repository path and inspect the existing local refs. Do not assume the paths or branch SHAs from this task are current.

### Allowed commands

Use read-only inspection commands only, for example:
- `pwd`, `ls`, `cat`, `grep`
- `git status --short`, `git rev-parse`, `git log`, `git show`, `git diff`, `git ls-tree`
- Read-only inspection of tracked files at already available refs, such as `git show <ref>:<path>`

Record exact commands and relevant outputs in the synthesis report. Do not use commands that contact a remote or change repository state.

### Prohibited actions

Do not run `git checkout`, `switch`, `restore`, `fetch`, `pull`, `worktree add`, `rebase`, `merge`, `cherry-pick`, `reset`, `stash`, `clean`, `commit`, or `push` during investigation. Do not modify, stage, or delete application files. Do not run Docker, the application, a database, migrations, tests, package installs, build commands, or scripts. Do not make live ESI/API/network requests. Do not inspect or reproduce secret values. Never copy credentials, tokens, cookies, private keys, or `.env` values into the report. Do not edit the Market wiki, existing tasks, `status.md`, or other project records; only the task lifecycle move/YAML and the required synthesis/closeout artifacts are permitted.

### Investigation steps

1. Record the application repository path, current branch, `HEAD` SHA, `local/improvements` SHA, and working-tree status. State whether evidence comes from committed trees or working-tree content. If relevant uncommitted or untracked files exist, label them explicitly; do not alter them. If either ref is absent or ambiguous, stop and ask.
2. Compare current and reference branches for Market modules, templates/static assets, schema/persistence helpers, ESI scope/token handling, sync integration, feature toggles/preferences, navigation, routes/API payloads, and tests. Identify current-only, reference-only, shared, and diverged paths.
3. Trace the character open-orders path in each branch where source permits: character/token selection → authenticated character-orders request → success/failure/empty response distinction → persistence → route/API → UI rendering. Provide a file:function chain for every hop. Mark any runtime-only behavior unverified.
4. Identify the exact source of each order's location/region/station/structure fields and how the UI presents it. Explicitly verify whether location is derived per order; do not infer from a default market hub.
5. Inspect the four legacy records listed in Files Involved. Separate open-orders defects from mining ledger, wallet-journal sales, price history, Jita-specific assumptions, and claims not proven by the historical evidence. Do not alter their status or contents.
6. Re-check each prior empty-page finding against source available on the two refs. Classify as confirmed on current branch, confirmed only on reference branch, no longer applicable, or unverified/runtime reproduction required.
7. Trace only the price paths that directly bear on Market restoration: the old Market page's own price/margin inputs, ESI market-price fallback, and Fuzzwork calls/consumers that could be confused with Market. For each hop provide file:function → file:function, cache/table where used, and branch presence. Explicitly answer whether Market sends order-level or character-specific data to Fuzzwork; if source does not prove the complete path, say unverified.
8. Inspect adjacent features and external services only for concrete touchpoints that affect the Market page or shared sync/price infrastructure. This is a shallow inventory, not a general application audit. Include wallet, mining, Notes, PI Planner, Assets/Wealth, zKillboard, EVE-Scout, and Pixel Nexus only where a direct code dependency, shared data source, or Settings disclosure interaction is found. Do not trace unrelated data flows. For each relevant touchpoint, state its role and why it matters or does not matter to initial restoration.
9. Record code-level ESI infrastructure used by the relevant paths: request headers/User-Agent, compatibility-date configuration, cache/ETag handling, route-specific rate-limit handling, error-limit handling, pagination, retry, sync coordination, and UI events. Record what the code implements and exact paths/functions. Do not research current CCP policy, current external documentation, API change history, or current SDE guidance; no web research is assigned here.
10. List potential future Market capabilities (historical orders, corporation orders, regional order books/history, global prices, structure markets, market groups) as one row each in a capability table. For each row give only apparent code/ESI availability, required scope/role if directly evident from code, initial-scope status, and a decision-needed flag. No deeper endpoint/policy research or feature analysis.
11. Build an ordered, minimal follow-on task sequence. Each proposed task must state: goal, dependency, likely file boundary, and verification method. Keep implementation tasks small enough for one local Qwen assignment. Do not create the tasks.
12. Map findings and recommendations to headings in the existing Market wiki. Do not edit the wiki. Identify conflicts between code and documented intent for human resolution.

### Evidence labels

Every report finding must carry one of these labels:
- **Direct inspection** — exact file/function/line or command output inspected in this session.
- **Historical claim** — statement found in an older task, summary, handoff, or prior report, not independently established by current source.
- **Unverified** — source does not establish behavior, runtime reproduction would be required, or the relevant local ref/path is unavailable.

Do not describe historical claims or unverified behavior as confirmed facts.

## Acceptance Criteria

- [ ] Application repository path, current branch/HEAD SHA, reference branch SHA, and working-tree status are recorded.
- [ ] Inspection clearly distinguishes committed-tree evidence from dirty/untracked working-tree evidence.
- [ ] Branch comparison lists relevant Market paths and classifies each as current-only, reference-only, shared, or diverged.
- [ ] Character open-orders flow is traced with a file:function chain per hop, or exact gaps are identified.
- [ ] Per-order location provenance and presentation are identified or explicitly marked unverified.
- [ ] Relevant ESI scope, feature toggle, DB, sync, route, UI, and test integration points are identified.
- [ ] ESI code-level behavior relevant to those paths (headers, configured compatibility date, caching, rate-limit/error handling, pagination/retry if present, sync/events) is recorded without external policy research.
- [ ] Market price inputs and Fuzzwork/ESI price paths directly relevant to Market are traced; order-level/character-specific transmission to Fuzzwork is verified or marked unverified.
- [ ] Adjacent capabilities and external services are included only when a direct Market/shared-infrastructure touchpoint exists.
- [ ] Future capabilities appear as one-row-per-capability entries with no deep analysis and clear decision-needed flags.
- [ ] Four legacy records are assessed and their claims classified without changing their files or lifecycle status.
- [ ] Wiki update recommendations are mapped to existing wiki headings; conflicts and human decisions are explicit.
- [ ] Each proposed follow-on task has a goal, dependency, file boundary, and verification method.
- [ ] No application-repository files, wiki pages, existing tasks, or project status files are changed.
- [ ] Only permitted agent-tasks lifecycle/report/closeout artifacts are changed; all changes are itemized.
- [ ] Report findings use the Direct inspection / Historical claim / Unverified evidence labels.

## Stop Conditions — escalate immediately

- Either required branch/ref is missing, ambiguous, or points to an unexpected commit.
- The application repository path cannot be located.
- The working tree is dirty in a way that prevents a reliable comparison or creates risk of confusing user changes with committed code.
- Required evidence would require runtime execution, a live ESI/API request, web research, credentials, or another prohibited action.
- Source code materially contradicts the Market wiki or reveals a design decision not covered by it.
- A secret or personal data exposure is encountered; do not copy it, and report only the file/path and nature of the risk.
- A referenced legacy record cannot be located; record the missing path and ask rather than substituting another task.

Do not resolve stop conditions by changing branches, cleaning the tree, running services, or broadening scope. Preserve state and request human direction.

## Commit Instructions

No commit or push is part of this task. The report/task lifecycle/closeout changes require reviewer GO and explicit human authorization before Git operations. If authorized later, stage only the specifically approved task/report/closeout paths; never use `git add .`.

## Documentation

- [ ] No wiki or application documentation edits in this task.
- [ ] Produce wiki update recommendations in the synthesis report, keyed to existing Market wiki headings.
- [ ] If external ESI/SDE policy research is still wanted, recommend a separate web-capable research task; do not perform that research here.
- [ ] Do not create that separate task unless requested and reviewed through the normal draft workflow.

## Dependencies

**Blocked by**: None.  
**Blocks**: Drafting narrow Market restoration implementation tasks.  
**Related tasks**: The four legacy Market records listed above; inspect actual locations and status, but do not alter them.  
**Wiki reference**: `projects/eve-dashboard/wiki/market/README.md`.

## Progress / Resume

Update this block and the synthesis report if the session stops before completion.

- **Done so far**: Draft task prepared for read-only inventory; awaiting review GO and human approval.
- **Next step**: After approval and dispatch, complete Step 0, create Stage 1 synthesis report, then perform the bounded inspection.
- **Last verified fact (with command)**: Draft exists at the path above; verify local repository state at execution time.
- **Local-only commits**: Record from `git log` when task starts; do not assume.
- **Owning session / window label**: Qwen local via Copilot, when dispatched.

## Completion Report

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  
**Final result**: Read-only inventory complete / blocked at stop condition  
**Evidence basis**: Direct inspection / historical claim / unverified — identify source.

### What was completed
- [Inventory/report sections completed]

### Issues discovered
- [Missing refs, dirty-tree ambiguity, source/wiki conflicts, or none]

### Follow-up tasks needed
- [Proposed task sequence; do not create tasks]

### Lessons learned
- [Relevant restoration and evidence-handling lessons]

## Handoff Summary

HANDOFF SUMMARY: [synthesis report path] | [branches/areas inspected] | [remaining human decision or next action]

## Human Authority

Tracy retains authority over priorities, scope, task approval, dispatch, implementation, commits, pushes, and lifecycle changes. This draft does not authorize Qwen dispatch, wiki edits, application changes, or creation of follow-on tasks.
