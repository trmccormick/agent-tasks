---
date: 2026-10-07
author: Planning Agent (READ-ONLY session)
purpose: Samvera ecosystem re-org analysis for agent-tasks tracking structure
label: every claim is REPORTED (counts and dates from the planning agent's read-only session; project facts from Tracy in chat; rule classifications from uploaded copies). Nothing was re-checked against the live repo in this revision.
revised: 2026-10-08 by Claude (sections 1, 5, 7 rewritten from Tracy's facts; section 6 corrected)
---

# 2026-10-07 PLAN: SAMVERA REORG ANALYSIS

## 1. INVENTORY

Project state below is **Tracy's statement** (REPORTED), not inferred from git recency. "Tracking last touched" is the date of the last commit to that project's folder in agent-tasks and says nothing about whether the project itself is active. Counts and dates are REPORTED from the planning agent's session.

### 1.1 projects/hyku

| Item | Detail |
|---|---|
| Files | 2 total (README.md + status.md only) |
| Tasks folder | 1 file in backlog/ (2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md); no active/ or completed/ |
| Tracking last touched | 2026-09-17 (update Hyku project status: backport blocking on production validation) |
| Project state | **Fold-in candidate.** Tracy believes this folder (the samvera fork) is not needed because `samvera_hyku` is the active Hyku folder. The one backport task (facet-limiting configuration) is recorded in status.md as waiting on production validation. Tracy says it is now **on hold**: a colleague on the Notch8 team, who works with Samvera, may have a solution that works upstream, and the two approaches will be compared (discussed last week). |

### 1.2 projects/samvera_hyku

| Item | Detail |
|---|---|
| Files | 32 total |
| Tasks folder | 16 task files across active/ + backlog/, plus 4 stray .md files directly in tasks/ |
| Tracking last touched | 2026-08-12 |
| Project state | **Active** (Tracy). This is the active Samvera Hyku folder. Its stack versions are not recorded here (add from its Gemfile.lock). The tracking folder is quiet since Aug 12; that is not project inactivity. |
| Last recorded work | Phase 1 GA fix complete on branch `fix/ga-tenant-property-scoping`; Wings/Bulkrax fix deployed Aug 7. |
| Waiting on | The GA fix is Tracy's work for Samvera. Testing it for cross-tenant pollution needs a VM with Google Analytics on both tenants, which the current Hyku product owner is to set up. Phase 2 (manual multi-tenant testing) waits on that. Tracy plans to follow up with him directly, likely next week. |
| Key docs | README.md (domain context guide), notes.md, handoffs/, summaries/ |

### 1.3 projects/samvera_hyrax

| Item | Detail |
|---|---|
| Files | 4 total (README.md + status.md only) |
| Tasks folder | 2 files: backlog/ has 1; no active/ or completed/ |
| Tracking last touched | 2026-10-07 (close issue #7655) |
| Project state | **Active.** Two PRs pushed 2026-10-07 (#7693 M3 based_near fix, #7655 metadata profile version display); issue #7410 investigated. |

### 1.4 projects/wvulibraries_knapsack

| Item | Detail |
|---|---|
| Files | 54 total (largest project folder) |
| Tasks folder | 33 task files: active/ 3, backlog/ 2, completed/ has subdirs |
| Tracking last touched | 2026-10-01 |
| Project state | **Active.** Collections Button UX fix committed Sep 28; YAML facet configuration path resolution fixed Sep 17. WVU-specific Hyku customization layer. |
| Deployment | This repo is the digitalhistory.lib.wvu.edu application (Tracy). The main VM, hyku.lib.wvu.edu, serves two tenants, demo and digitalhistory. The same git repo also runs on hykudev.lib.wvu.edu with a demo tenant only. digitalhistory runs Hyku 7.1.3 / Hyrax 5.2.0. |
| Pending (Tracy) | DevOps has a new `puma.rb` that needs to be applied properly to this repo as an override. It currently sits in the `hyrax-webapp` folder, which is the wrong location. It works on the VM, but it needs to live in the knapsack's override layer. Probably not a task yet. |
| Key docs | README.md, UPSTREAM_CONTRIBUTION_AUDIT.md, handoffs/, summaries/ |

### 1.5 projects/wvulibraries_acda_portal

| Item | Detail |
|---|---|
| Files | 10 total |
| Tasks folder | 6 files: active/, backlog/, completed/ all exist |
| Tracking last touched | 2026-07-17 is the last meaningful session; the folder's most recent commit (2026-10-01) was a cross-project archive. |
| Project state | **Live and active** (Tracy). Educational site at congressarchives.org (dev: congressarchivesdev.lib.wvu.edu). |
| What it is | A Samvera application closer to Hydra-head than Hyku. It harvests records from partner sites through Bulkrax. WVU's records were first served by a Hydra-head site (mcppc.lib.wvu.edu); the same data is now served by the Hyku instance at digitalhistory.lib.wvu.edu, and the old URL redirects. It talks to Fedora 6 directly through ActiveFedora: no Valkyrie, no Wings. Its Bulkrax version differs from samvera_hyku's, so fixes must not be copied across without checking. Tracy describes it as overengineered for its need. |
| Watch item (Tracy) | Cloudflare was reported earlier as blocking thumbnails. That appears resolved; it is a concern to monitor, not an open problem. Harvesting from the WVU side is working. WVU records were cleared from ACDA and reimported after the WVU harvest source changed; the overall work is still in progress. |
| Modernization plan | `MODERNIZATION.md` in `wvulibraries/hydra_acda_portal_public` (link to it; do not copy it). The plan is a proposal and is in flux. As written it proposes PostgreSQL + ActiveRecord (`CongressionalRecord` replaces `Acda`), ActiveStorage for images, thumbnails, PDF, audio and video, good_job replacing Sidekiq/Redis, Solr and Blacklight staying, and Fedora removed in a later phase after a fallback. The document calls itself a proposal with decision date TBD, and Tracy confirms there is no real target for the update yet. The only work done so far is replacing Sidekiq with good_job, by a junior developer, as the starting point; whether it is deployed is not recorded. |
| Tracking status | agent-tasks calls the first phases 1a (good_job) and 1b (PostgreSQL), blocked on DevOps approval for a dev VM maintenance window. Those names differ from the plan's six phases. |

### 1.6 projects/wvulibraries_authentication

| Item | Detail |
|---|---|
| Files | 25 total |
| Tasks folder | 23 files across active/ + backlog/ |
| Tracking last touched | 2026-06-27 (last meaningful activity June 19, Seq 7 Phase 1 centralized logging complete) |
| Project state | **Active, live, stable** (Tracy). A simple internal app that lets library staff issue temporary accounts to patrons for computer use. Quiet tracking is not inactivity. |

### 1.7 projects/wvulibraries_databases

| Item | Detail |
|---|---|
| Files | 21 total |
| Tasks folder | 8 files: active/, backlog/, completed/ |
| Tracking last touched | 2026-10-06 |
| Project state | **Active; the Rails 7 update is paused** (Tracy). It is paused on CSS issues in the main navigation menu on the admin side (not the public side). Most were resolved this week, and the rest were passed to the lead frontend developer at WVU Libraries for final polish. The last tracked item was the Bootstrap 5 offcanvas left-edge shading seam. |

### 1.8 projects/wvu-moonshot

| Item | Detail |
|---|---|
| Files | 11 total |
| Tasks folder | 6 files: backlog/ + active/ |
| Tracking last touched | 2026-09-16 (archive old backlog tasks to superseded folder) |
| Project state | **Paused** (Tracy). It was built for a one-off iPad event check-in, and the university canceled the event. Incomplete prototype, tested by a few people; unbuilt: is_alumni flag, education-history display, manual-entry form. A reusable starting point for a future event app. Keep in place; do not archive or delete. |
| Tracking gap | The folder's status.md still says "ready for dispatch", because the cancellation was never recorded. That is why the planning agent judged it live. |

---

## 2. HYKU vs SAMVERA_HYKU: OVERLAP AND UNIQUE CONTENT

### What Overlaps (REPORTED from file content)

| Aspect | samvera_hyku | hyku |
|---|---|---|
| **Subject** | Samvera Hyku platform work — GA multi-tenant fixes, Wings/Bulkrax issues, Bootstrap datepicker deprecation, facet limiting investigation | WVU Knapsack → Hyku upstream backport (facet limiting config specifically) |
| **Wings::ModelRegistry** | Fix implemented and deployed Aug 7 | Not referenced |
| **GA (Google Analytics)** | Phase 1 monkey-patch complete, Phase 2 Hyrax proposal in backlog | Not referenced |
| **Facet limiting** | Investigated as upstream Wings defensive check | Backport task (2026-09-17) is the downstream consumer waiting on Knapsack production validation |
| **Bootstrap datepicker** | Investigation task Aug 5 | Not referenced |

### What Is Unique to Each

**samvera_hyku holds:**
- GA multi-tenant implementation (Phase 1 code + branch `fix/ga-tenant-property-scoping`) — REPORTED: 5 new files on branch
- Wings::ModelRegistry Bulkrax fix — REPORTED: deployed Aug 7
- Bootstrap datepicker locale deprecation investigation — REPORTED
- Three dated backlog tasks from Jul-Aug era — REPORTED
- Domain context guide (README.md) explaining multi-tenancy model — REPORTED
- Technical notes (notes.md) — REPORTED

**hyku holds:**
- Single backport task for facet limiting config — REPORTED: 1 file in backlog/
- Explicit dependency on WVU Knapsack production validation as blocker — REPORTED from status.md
- Intended as the bridge between Knapsack work → upstream Hyku PR — REPORTED

### Which Holds Live Work?

**wvulibraries_knapsack** holds the live, deployed work (WVU-specific customizations). The Knapsack IS the deployed WVU instance — it's where changes are tested and verified before any upstream contribution. 

**samvera_hyku** holds the research/implementation context for upstream contributions. It's where agent sessions document findings that may become PRs to samvera/hyku.

**hyku** is essentially an empty shell awaiting a single task — it exists only as a staging folder for one backport.

---

## 3. MINIMAL ON-DEMAND PROJECT-FOLDER PATTERN

An agent pulling a new repo should create this structure from scratch:

```
projects/<repo-name>/
├── README.md           # Domain context guide (agent-created, populated from codebase)
├── status.md           # Project status & task tracking (agent-created, minimal header)
├── tasks/
│   ├── active/         # Current work — task files moved here by agent per TASK_TEMPLATE.md
│   ├── backlog/        # Assigned but not yet started
│   └── completed/      # Finished tasks organized by YYYY-MM subfolder
├── handoffs/           # Session continuity notes (created as needed)
└── summaries/          # Synthesis reports from agent sessions (created as needed)
```

**Rules for agent creating the folder:**
1. Start with README.md + status.md only (REPORTED: Hyrax and hyku follow this minimal pattern)
2. Create tasks/ with active/, backlog/ subdirs automatically (REPORTED: all projects use this)
3. handoffs/ and summaries/ created on first need, not upfront (REPORTED: some projects have them, some don't)
4. NO docs/ or notes/ folders unless the project's codebase has agent-specific artifacts that belong in tracking
5. File count after creation: 2 files + 2 empty subdirs = ~4 entries

**This keeps new project folders lean (matching Hyrax/hyku pattern) and expands organically as sessions produce artifacts.**

---

## 4. CROSS-PROJECT FEATURE TRACKING (Hyrax + Hyku + Knapsack)

A feature spanning Hyrax (upstream engine), Hyku (multi-tenant platform), and Knapsack (WVU customization) needs a tracking model that reflects the dependency chain without creating parallel workstreams.

### Option A: Centralized Task File with Sub-Tasks

**Concept**: Single master task file in the project folder where the feature originates (typically Knapsack, since that's where WVU-specific work lives). The task references upstream Hyrax/Hyku tasks.

```
projects/wvulibraries_knapsack/tasks/active/2026-10-XX-FEATURE-WIDE-X.md
├── Prerequisite: projects/samvera_hyrax/backlog/... (if upstream Hyrax change needed)
├── Sub-task A: Knapsack implementation (this task)
├── Sub-task B: Hyku override if needed → hyku/tasks/backlog/...
├── Sub-task C: Hyrax upstream PR → samvera_hyrax/tasks/backlog/...
```

**Pros**: Single source of truth; Tracy manages one master task. Clear dependency chain in YAML header.
**Cons**: Knapsack status.md becomes a hub for cross-project coordination (may be noisy). Agent working on Hyrax sub-task must context-switch between folders.
**Best for**: Features where Knapsack is the primary driver and upstream changes are secondary adapters.

### Option B: Linked Task Files Per Project with Dependency Header

**Concept**: Each project creates its own task file, linked via cross-references in YAML frontmatter. A coordinating agent (Claude/Strategist) tracks the overall feature.

```
projects/wvulibraries_knapsack/tasks/active/2026-10-XX-FEATURE-X-KNAPSAK.md
  depends_on: projects/samvera_hyrax/backlog/...  (in YAML header)

projects/samvera_hyku/tasks/active/2026-10-XX-FEATURE-X-HYKU.md  
  depends_on: projects/wvulibraries_knapsack/active/... (in YAML header)
```

**Pros**: Each project's agent sees only their relevant task. Clean separation of concerns. Matches existing multi-phase pattern (GA fix had Phase 1 in hyku, Phase 2 in hyrax backlog).
**Cons**: Requires cross-referencing discipline; no single status view without reading all three.
**Best for**: Features where each layer requires substantial independent work and different agent expertise.

### Recommendation (planning agent's; not decided by Tracy, see section 7)

Use **Option B** as the default pattern — it already exists (GA multi-tenant fix: Phase 1 in samvera_hyku, Phase 2 in samvera_hyrax backlog). It maps to the architectural dependency chain and lets each project's status.md show its own progress independently. Use Option A only for small features where upstream changes are trivial adapters of Knapsack work.

---

## 5. ARCHIVE CANDIDATES (List Only — Move Nothing)

Only one folder is a candidate, and nothing is moved without Tracy's approval.

| Folder | Proposed action | Needs from Tracy |
|---|---|---|
| **projects/hyku** | Fold into `samvera_hyku`: carry the one backport task (`2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md`) into `samvera_hyku/tasks/backlog/`, then move the `hyku` folder to an archive location. Never delete. | The backport is on hold, not dropped, so carry the task over marked on hold (pending the comparison with the Notch8 colleague's upstream-compatible solution). Confirm before the folder moves. |

**Do NOT archive** (Tracy's statements):
- **projects/wvulibraries_knapsack**: active; the digitalhistory.lib.wvu.edu application and WVU's Hyku customization layer.
- **projects/samvera_hyrax**: active upstream work.
- **projects/samvera_hyku**: active; the active Samvera Hyku folder.
- **projects/wvulibraries_databases**: active; the Rails 7 update is paused pending final frontend polish.
- **projects/wvulibraries_acda_portal**: live site; the modernization is only a proposal so far, with the Sidekiq-to-good_job swap as the starting point.
- **projects/wvulibraries_authentication**: live, stable internal app.
- **projects/wvu-moonshot**: paused, not retired. Keep in place as a starting point for future event apps.

**State lines proposed (not applied).** First line of each project's status.md: `Project state: active | paused | archived` plus one sentence of reason. Proposed for the three the planning agent misjudged:
- moonshot: `Project state: paused — event canceled by the university; incomplete prototype kept as a starting point for future event apps.`
- authentication: `Project state: active — live internal app; quiet tracking is not inactivity.`
- acda_portal: `Project state: active — live site; modernization plan in flux (see MODERNIZATION.md in hydra_acda_portal_public).`

---

## 6. GOVERNANCE RULES: CLASSIFICATION FROM GUARDRAILS.MD

### Table 1 — Rules and governance rules inside `rules/GUARDRAILS.md` (36 rows)

Legend:
- **UNIVERSAL** = applies to every project and every agent.
- **GALAXY-SPECIFIC** = applies only to the Galaxy Game repo or its container setup; belongs in projects/galaxy_game/SESSION_GUIDANCE.md, not in root.
- **ROUTING PREFERENCE** = about which agent or model to use; belongs in the agent notes file, not in rules.
- **STALE** = written for a tool or model no longer used; candidate to retire.
- **MERGED** = tombstone, content lives in another rule.
Evidence: every row is **REPORTED**. Classifications were written by the planning agent and then corrected in Claude's review of 2026-10-07, which used uploaded copies of GUARDRAILS.md, README.md, ROUTING_LOGIC.md, AGENT_ROUTING.md and TASK_TEMPLATE.md. Nothing was re-checked against the live repo, and the Rule 11 `find` result is the planning agent's own report. "-" in the Conflict ref column means no known conflict with the register rows (A1–E).

| Rule or requirement | Source (file + heading) | Class | One-line reason | Conflict ref |
|---|---|---|---|---|
| Rule 0 — Tool Availability | GUARDRAILS.md / "Tool Availability" | STALE | Written for the Continue tool surface; no longer applicable. | B6 |
| Rule 1 — Docker exec pattern | GUARDRAILS.md / "Rule 1 — Docker" | UNIVERSAL | Container name and working directory are Galaxy-specific parameters. Galaxy-specific part moves to projects/galaxy_game/SESSION_GUIDANCE.md. | - |
| Rule 1a — Container Lifecycle Management | GUARDRAILS.md / "Rule 1a — Container Lifecycle Management" | UNIVERSAL | "Containers assumed always running" applies to any Docker project. | - |
| Rule 2 — Database Migrations | GUARDRAILS.md / "Rule 2 — Database Migrations" | UNIVERSAL | Rails generator examples target Galaxy stack path. Galaxy-specific part moves to projects/galaxy_game/SESSION_GUIDANCE.md. | - |
| Rule 3 — No Full RSpec Suites | GUARDRAILS.md / "Rule 3 — RSpec Execution" | UNIVERSAL | Explicitly "AGENTS MUST NEVER RUN FULL SUITES" for all projects. | - |
| Rule 3a — Mandatory Pre-Execution Check | GUARDRAILS.md / "Rule 3a" | UNIVERSAL | "Before running ANY RSpec command" applies universally. | - |
| Rule 7 — RSpec Output Format | GUARDRAILS.md / "Rule 7 — RSpec Output" | UNIVERSAL | Verbatim failure reporting; no Galaxy content. | - |
| Rule 10 — Host vs Container Path Prefixes | GUARDRAILS.md / "Rule 10" | UNIVERSAL | Says "Applies to all agents on all projects." | - |
| Rule 11 — Atomic Documentation (TASK_OVERVIEW.md) | GUARDRAILS.md / "Rule 11" | STALE | TASK_OVERVIEW.md exists only in archive/ and projects/galaxy_game/deprecated/; not live. | - |
| Rule 12 — Task File Lifecycle | GUARDRAILS.md / "Rule 12" | UNIVERSAL | Backlog→active→completed protocol applies everywhere. Galaxy-specific part moves to projects/galaxy_game/SESSION_GUIDANCE.md. | A2, D4, E |
| Rule 13 — Handoff Commands | GUARDRAILS.md / "Rule 13" | UNIVERSAL | Strategic context vs. active sub-task distinction is cross-project. | D5 |
| Rule 13a — Handoff Format Discipline | GUARDRAILS.md / "Rule 13a" | UNIVERSAL | Minimal handoff rule; example project is Galaxy but text is universal. | D5, E |
| Rule 14 — Code Payload Protocol | GUARDRAILS.md / "Rule 14" | UNIVERSAL | `[CODE_PAYLOAD:]` marker for all file changes. | - |
| Rule 15 — Financial Constants | GUARDRAILS.md / "Rule 15" | GALAXY-SPECIFIC | Lunar loss rates, GCC peg, SCC surcharge — Galaxy domain logic. Galaxy-specific part moves to projects/galaxy_game/SESSION_GUIDANCE.md. | - |
| Rule 16 — No Parallel RSpec Runners | GUARDRAILS.md / "Rule 16" | UNIVERSAL | One RSpec at a time applies to any project. | - |
| Rule 17 — Synthesis Before Implementation | GUARDRAILS.md / "Rule 17" | GALAXY-SPECIFIC | References `BaseUnit` and Galaxy domain model. Galaxy-specific part moves to projects/galaxy_game/SESSION_GUIDANCE.md. | open: MAG-2/MAG-4 allow synthesis optional for low-risk; Tracy must decide whether to keep mandatory gate or generalize |
| Rule 18 — Integration Specs Quarantined | GUARDRAILS.md / "Rule 18" | UNIVERSAL | Integration test discipline applies anywhere. | - |
| Rule 19 — Stop Conditions | GUARDRAILS.md / "Rule 19" | UNIVERSAL | General stop conditions apply everywhere. | - |
| Rule 20 — Local Model Fabrication Prohibition | GUARDRAILS.md / "Rule 20" | UNIVERSAL | Applies to all local models; no Galaxy content. Note: fabrication ban overlaps Rule 20a. Tool list in this rule is STALE (written for Continue). | B6 |
| Rule 20a — Evidence Basis for Material Claims | GUARDRAILS.md / "Rule 20a" | UNIVERSAL | Explicitly "Applies to all agents, all roles, and all supervision tiers." | - |
| Rule 21 — Qwen3.5 Triage Phase Requirements | GUARDRAILS.md / "Rule 21" | ROUTING PREFERENCE | Agent-name-specific triage template; concept reusable but content is agent-tied. | B4, B6 |
| Rule 22 — Continue Model Scope Limits | GUARDRAILS.md / "Rule 22" | STALE | Written for the Continue tool surface; no longer applicable. | B6 |
| Rule 23 — Token Conservation (Escalation Ladder) | GUARDRAILS.md / "Rule 23" | STALE | Fixed local-first escalation ladder; conflicts with MAG-3. | B1 |
| Rule 24 — Perplexity Workflow Integration | GUARDRAILS.md / "Rule 24" | ROUTING PREFERENCE | Assigns Perplexity role specific to Galaxy agent stack; advisory routing, not hard rule. | B1 |
| Rule 25 — No File Recreation From Scratch | GUARDRAILS.md / "Rule 25" | UNIVERSAL | Universal edit-in-place rule; no Galaxy content. | - |
| Rule 26 — No Autonomous Git Commit or Push | GUARDRAILS.md / "Rule 26" | UNIVERSAL | "Applies to all agents, all roles, all supervision tiers." | A4, A5 |
| Rule 27 — MERGED INTO RULE 10 | GUARDRAILS.md / "Rule 27" | MERGED | Tombstone; content merged into Rule 10. Candidate to remove from table. | - |
| Rule 28 — Never Bypass a Gitignore Boundary | GUARDRAILS.md / "Rule 28" | UNIVERSAL | "Applies to all agents, all roles, all supervision tiers, all projects." | - |
| Rule 29 — Stale Active Task Protocol | GUARDRAILS.md / "Rule 29" | UNIVERSAL | Describes stale-task handling across parallel agent lanes; applies to all projects. | A1, A2 |
| Rule 30 — Summary/Synthesis Artifacts Location | GUARDRAILS.md / "Rule 30" | UNIVERSAL | "Applies to all agents … all projects." | - |
| MAG-1 — Task File as Execution Contract | GUARDRAILS.md / "MAG-1" | UNIVERSAL | Defines task-file authority; applies to all projects. | A1, A2 |
| MAG-2 — Human-Controlled Dispatch and Synthesis Authority | GUARDRAILS.md / "MAG-2" | UNIVERSAL | Human authority boundaries apply everywhere. | C1/C2 (synthesis gate) |
| MAG-3 — Capability- and Availability-Based Agent Routing | GUARDRAILS.md / "MAG-3" | UNIVERSAL | Controlling routing rule; applies to all projects. | B1, B2, B3 |
| MAG-4 — Blocking Dependency Management & Parallelization | GUARDRAILS.md / "MAG-4" | UNIVERSAL | Applies to all multi-agent work regardless of project. | - |
| MAG-5 — Agent Preferences as Guidance, Not Rules | GUARDRAILS.md / "MAG-5" | UNIVERSAL | Preferences are guidance, not rules; applies to every project. | - |
| MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md | GUARDRAILS.md / "MAG-6" | UNIVERSAL | Restricts what guidance may override; universal constraint. | - |

---

### Table 2 — Requirements outside `rules/GUARDRAILS.md` (17 rows)

| Rule or requirement | Source (file + heading) | Class | One-line reason | Conflict ref |
|---|---|---|---|---|
| Synthesis gate (mandatory for all executor tasks) | README.md / "EXECUTOR Role" § "Synthesis-Gated Workflow" | UNIVERSAL | All executable tasks require synthesis report before implementation. | C1 vs C2: MAG-2/MAG-4 say optional for low-risk; open decision for Tracy (MAG-2/MAG-4 already allow optional for low-risk work) |
| Agent Dispatch Interface (mandatory section in every task file) | TASK_TEMPLATE.md / "Agent Dispatch Interface (Required)" | UNIVERSAL | Copy-paste startup contract; "NOT optional scaffolding." | D3: conflicts with light-default workflow that allows direct Tracy-directed work without a task file |
| 27B / 35B hierarchy (27B primary, 35B heavy) | ROUTING_LOGIC.md / "The One Rule" + header | ROUTING PREFERENCE | 27B handles everyday tasks; 35B for heavy work on Ryzen. Applies to one model version only; belongs in the agent notes file as a dated entry, not in rules or the routing doc. | B4: inconsistent with AGENT_ROUTING's qwen3.5 baseline and TASK_TEMPLATE's generic Qwen language |
| GitHub Copilot budget constraint (≤15 %/week, 60 %/month max) | rules/AGENT_ROUTING.md / "Cross-Project GitHub Copilot Budget Strategy" | ROUTING PREFERENCE | Keeps premium usage within bounds across all projects. | - |
| Two-local-failures escalation rule | ROUTING_LOGIC.md / "Escalation Protocol" (Type 1 + Type 2) | STALE | Cloud agent after two local failures in fresh sessions. | B3: conflicts with MAG-3 which prohibits fixed routing ladders |
| "Local first" hand-off philosophy | GUARDRAILS.md / "Rule 23" + rules/AGENT_ROUTING.md / "Hard Rules" (Local First) | STALE | Local agents before cloud; advisory principle, not hard rule. Written for tool surface no longer used. | B1/B3: conflicts with MAG-3's prohibition on fixed routing hierarchies |
| `git mv` required for task moves | ROUTING_LOGIC.md / "Hard Rules" → "git mv required" + TASK_TEMPLATE.md (AGENT_DISPATCH_INTERFACE STEP 0) | UNIVERSAL | Task lifecycle requires tracked-file renames via git mv. | A1/A2: README's Step 2 allows `rm -f` duplicates, MAG-1 says escalate to Tracy |
| One task per local session | ROUTING_LOGIC.md / "Hard Rules" → "One task per local session" | UNIVERSAL | Prevents context accumulation and tool failures. | - |
| No JSON commits | Appears in three files across the project | UNIVERSAL | All file changes as prose; no JSON payloads allowed. | - |
| Supervised host-only git | GUARDRAILS.md / Rule 26 + README.md / "Hard Rules" | UNIVERSAL | All git commits require human supervision on the host. | A4, A5 |
| README Planning-Agent-Only Workflow | README.md / "Planning Agent Workflow" (D1) | STALE | Superseded by the new design: a planning agent reads actual files and runs git checks. README text says the planning agent does not. | D1 |
| "No Continue sidebar" requirement | README.md / "Hard Rules" + rules/AGENT_ROUTING.md | STALE | Written for the Continue tool surface no longer used. | B6 |
| README "Symlinked Task Repo" section | README.md / "Symlinked Task Repo" | GALAXY-SPECIFIC | docs/new_agent symlink exists only in the Galaxy repo; belongs in projects/galaxy_game/SESSION_GUIDANCE.md. | E |
| Handoff max length (9 lines in GUARDRAILS; 2–4 lines in README; ~10 lines in README handoff section) | GUARDRAILS.md / "Rule 13a" (9 lines); README.md / EXECUTOR Phase 2 (2–4 lines); README.md / Planning Agent Workflow §4 (~10 lines) | UNIVERSAL | Handoff format limit; three different values across files; pick one. | D5 |
| No multi-project cross-pollination | README.md / "Hard Rules" → "No Multi-Project Cross-Pollination" | UNIVERSAL | Keep context isolated to the active project unless a blueprint says otherwise. | A6: Hyrax→Hyku→Knapsack linked features need an exception |
| README Stale Active Task Protocol (4-step: review completion report, check summaries, verify git log, move or revert) | README.md / "Hard Rules" → "Stale Active Task Protocol" + GUARDRAILS.md / "Rule 29" | UNIVERSAL | Parallel-lane stale-task handling. Conflicting `rm -f` is in README Task Completion Workflow Step 2 (A1), not the protocol itself. | A1: duplicate-deletion conflict in README Step 2, not the protocol; also conflicts with MAG-1 escalation when two copies exist |
| README Task Completion Workflow (Step 1 git mv, Step 2 find-dedup, Step 3 update status.md, Step 4 commit in chat, Step 5 push after approval) | README.md / "EXECUTOR Role" § "Task Completion Workflow" Steps 1–6 | UNIVERSAL | Full executor post-work lifecycle; the `git commit` in Step 5 conflicts with Rule 26. | A4: agent self-commits vs. Rule 26 no-autonomous-commit |

---

## 7. OPEN DECISIONS FOR TRACY

**Answered since the first draft (recorded from Tracy):**
- `hyku` folder: believed not needed, because `samvera_hyku` is the active one. Its one backport task is on hold, not dropped (see item 1).
- `wvulibraries_authentication`: live, stable; not an archive candidate.
- `wvu-moonshot`: paused (event canceled by the university); keep in place.
- ACDA Portal: live; the modernization proposal has no decided target yet, and the only work done is the Sidekiq-to-good_job swap.

**Still open:**

1. **The backport task in `hyku`.** Answered: still wanted, but on hold while Tracy's solution is compared with the Notch8 colleague's upstream-compatible one. Remaining: when the folder is folded in, carry the task into `samvera_hyku/tasks/backlog/` marked on hold, and confirm that is right.
2. **State line convention.** Confirm the `Project state:` first line for every project status.md (proposals in section 5).
3. **Stack-versions line.** Confirm a stack-versions line in each project README (persistence layer, Rails, Hyku/Hyrax/Bulkrax versions from Gemfile.lock), given that ACDA and samvera_hyku differ.
4. **No renaming of existing folders.** The `samvera_*` / `wvulibraries_*` prefixes differ; the proposal is to leave existing folders as they are and name new on-demand folders after the repo.
5. **Cross-project feature tracking.** Option A (one master task), Option B (linked per-project tasks), or a single feature file listing repos, order and PR links. Section 4 shows the planning agent's options and recommendation; Tracy has not decided.
6. **ACDA tracking.** Link to `MODERNIZATION.md` in `hydra_acda_portal_public` instead of copying it, and record that it has no decided target and that the only work done is the Sidekiq-to-good_job swap. Reconcile the 1a/1b phase names with the plan's six phases. Record the Cloudflare thumbnail concern (reported earlier, appears resolved, monitor) and the clear-and-reimport of WVU records in the ACDA status.md, since the tracking folder says nothing about either.
7. **Stray files in `samvera_hyku/tasks/`.** Four .md files sit directly in tasks/ (`facet_label_defaults_investigation.md`, `investigate_wings_modelregistry_fix.md`, `upstream-wings-defensive-checks.md`, plus one dated task). Move to backlog/ with dated names (parked cleanup, not part of the workflow work).
8. **samvera_hyku tracking.** Record in the samvera_hyku status.md that the Phase 2 cross-tenant GA test is waiting on the Hyku product owner to set up a VM with GA on both tenants, and that Tracy plans to follow up next week.
9. **databases tracking.** Record in the wvulibraries_databases status.md that the Rails 7 update is paused pending final CSS polish of the admin-side navigation menu by the lead frontend developer.
10. **knapsack tracking.** Record in the wvulibraries_knapsack status.md, as a pending item and probably not yet a task, that DevOps's new `puma.rb` needs to be applied as a proper override in the knapsack. It currently sits in the `hyrax-webapp` folder, which is the wrong location, though it works on the VM.

---

*This document is analysis only. No repository files other than this one were changed by it.*
*Every claim is REPORTED: counts and dates come from the planning agent's read-only session, project facts from Tracy in chat, and rule classifications from uploaded copies of the rule files. Nothing was re-checked against the live repo in the 2026-10-08 revision.*
