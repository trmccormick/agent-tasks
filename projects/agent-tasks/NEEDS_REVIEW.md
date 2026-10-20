# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Implementation agents: add an entry here instead of just noting something in status.md when an escalation trigger below fires. Remove/archive entries once resolved — this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- A workflow procedure produces consistent friction across 3+ sessions (e.g., handoff file proliferation)
- A tool or pattern is working well but needs documentation for future adoption
- A session discovers a gap in guidance that would help future sessions avoid debugging
- Two projects use contradictory patterns for the same task type (standardization needed)
- A procedure worked well — add an entry to recommend it for adoption elsewhere

---

## Entry template

### [DATE] — [topic]
**What happened**: 
**Observed in**: [project/session]
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-10-13 — Handoff file lifecycle undefined
**What happened**: During Hyku 7.2.0 upgrade session, three intermediate handoff files accumulated (`2026-10-13-HYKU-7.2.0-*` variations). No guidance on consolidation, archiving, or which one is "the real" handoff for next session.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: SESSION_CLOSEOUT.md (no consolidation guidance); SESSION_GUIDANCE.md (no handoff lifecycle); galaxy_game NEEDS_REVIEW.md (no equivalent friction reported)
**What needs a second opinion**: (1) Should SESSION_CLOSEOUT.md add a "Handoff Consolidation" step? (2) Should handoff template have a "NEXT SESSION" section? (3) Should old intermediate handoffs be archived or deleted (and when)?
**Priority**: MEDIUM — affects all future multi-session projects
**Status**: OPEN

### 2026-10-13 — SESSION_GUIDANCE checklists missing for routine tasks
**What happened**: Added "Hyku Submodule Upgrades (7.2.0+ specific)" architectural gotchas to SESSION_GUIDANCE, but no actionable checklist. Future sessions will repeat same debugging steps.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Current SESSION_GUIDANCE has "Critical Rules" + "Testing Constraints" + "Known Gotchas" sections (all prose, no checklists); galaxy_game PROJECT_GUIDANCE includes checklists for celestial body definition and contract creation
**What needs a second opinion**: Should SESSION_GUIDANCE include checklists for: (a) Hyku version upgrades, (b) Submodule updates, (c) Docker rebuild/testing workflow? Template: pre-upgrade checks, during-upgrade steps, post-upgrade validation.
**Priority**: MEDIUM — high value, low effort
**Status**: OPEN

### 2026-10-13 — Commit message conventions not specified
**What happened**: Upgrade work used commit messages like "fix: Guard Hyku VERSION constants" + "chore: Remove override" + "fix: Add shim". Pattern emerged but not documented anywhere.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Corrected 2026-10-13 (web review): wvu_knapsack does have a CONTRIBUTING.md. Its "Git Workflow" section says only to commit with clear messages, with no type prefixes, no upstream-reference rule and no multi-commit pattern (REPORTED: pasted from GitHub; branch not confirmed). The earlier claim that no CONTRIBUTING rules exist was wrong. agent-tasks has no commit convention guidelines (original check, not re-verified).
**What needs a second opinion**: Decided 2026-10-13 (Tracy): add a "Commit messages" subsection to the knapsack CONTRIBUTING.md (type prefixes; one logical change per commit; name the upstream issue or PR for workarounds; submodule bumps as their own commit). Not decided: whether agent-tasks needs its own convention. Its own commits already use `task:` and `complete:` prefixes (VERIFIED in README "Task Completion Workflow").
**Priority**: LOW — not blocking, improves git log readability
**Status**: OPEN. Next action: run `projects/wvulibraries_knapsack/tasks/backlog/2026-10-13-LOW-DOCS-CONTRIBUTING-COMMIT-MESSAGES.md` (drafted, not dispatched). Mark RESOLVED when its diff is reviewed and committed.

### 2026-10-18 — TASK_TEMPLATE.md synthesis section contradictions (catalog-image containment review)
**What happened**: During catalog-image containment task draft review, four internal contradictions were found in `TASK_TEMPLATE.md` that affect every task file using it:

1. **Synthesis output conflict**: Lines 241 and 280 say to "post synthesis in chat" but lines 123 and 243 say "never paste synthesis in chat."
2. **Step ordering conflict**: Synthesis section (line 241) says "before any commands" while Step 0 (lines 102/107) requires lifecycle commands first — impossible to satisfy both.
3. **Path inconsistency**: the `find` verification command uses `agent-tasks/projects/...` (relative to the clone parent) while the `git mv` commands use `projects/...` (relative to the clone root); intended working directory is unverified.
4. **Body ordering conflict**: Body lists readiness checklist before Dispatch Interface, but header comments say Dispatch goes first.

**Observed in**: galaxy_game project, catalog-image containment task drafts (A/B/C) review session
**What I already checked**: VERIFIED against current `TASK_TEMPLATE.md` — all four contradictions confirmed on disk. Affects every task file that follows this template.
**What needs a second opinion**: (1) What is the correct synthesis output destination — chat or file? (2) Should Step 0 run before or after synthesis reading? (3) Should paths be absolute to clone root or relative? (4) Which ordering is intended — checklist first or dispatch first? **Recommendation**: Schedule a dedicated Claude session for agent-tasks repo to fix the template holistically rather than patching individual task files.
**Priority**: HIGH — every drafted task inherits these contradictions
**Status**: OPEN. Next action: review with a Claude session for the agent-tasks repo; fix at template level, then re-substitute blocks in affected tasks.

---

### 2026-10-13 — Artifact categorization unclear
**What happened**: SESSION_CLOSEOUT.md says "never delete without human approval" for everything. But intermediate synthesis report drafts, debugging notes, and working handoff versions clutter the repo. Unclear what stays vs gets archived.
**Observed in**: agent-tasks project, workflow observation
**What I already checked**: GUARDRAILS.md (no artifact lifecycle rules); SESSION_CLOSEOUT.md section 0 (says don't touch other sessions' files, but doesn't define artifact types)
**What needs a second opinion**: Define artifact categories: (a) Session records (keep): task files, status.md entries, final handoff; (b) Working drafts (archive per approval): intermediate notes, synthesis drafts; (c) Permanent history (never delete): completed task files, merged commits, approved PRs.
**Priority**: MEDIUM — prevents repo bloat
**Status**: OPEN

### 2026-10-13 — WVU Knapsack submodule upgrade lessons should apply to other projects
**What happened**: Discovered several gotchas specific to Hyku 7.1 → 7.2 upgrade: cached Docker images hide timing bugs, initializer load order is critical, third-party gem compatibility changes. These apply to ANY Ruby framework/gem upgrade, not just Hyku.
**Observed in**: wvulibraries_knapsack project, implementation session
**What I already checked**: Added to wvulibraries_knapsack SESSION_GUIDANCE; not yet generalized for agent-tasks or other projects
**What needs a second opinion**: Should agent-tasks ROOT_GUIDANCE.md (or similar) have a generic "Framework/Gem Upgrade Checklist" that all projects can reference? Would prevent re-discovering same lessons on next Node/Rails/React/Python version bump in a different project.
**Priority**: LOW — nice-to-have, improves consistency
**Status**: OPEN


### 2026-10-18 — Handoff rules conflict across docs (file name, folder name, when required, retention)
**What happened**: Updated 2026-10-19 after reading origin at `649f34c`; the first version of this entry said no doc names a handoff folder or file name, which was wrong (I had read only two session docs). The live docs give three file-name patterns: `session_handoff_YYYY-MM-DD_<TOPIC>.md` in `HUMAN_WORKFLOW_GUIDE.md:143` and `PLANNING_AGENT_SESSION_START.md:307` (both added by Tracy on 2026-10-12, `237ead1f`), `<date>-<type>-SESSION.md` in `SESSION_CLOSEOUT.md:21` and `WEB_AGENT_SESSION_START.md:45`, and `<date>-<type>-CLOSEOUT.md` in `PLANNING_AGENT_CLOSEOUT.md:59`. All use the folder `projects/<project>/handoffs/<agent>/`, but the agent folder differs: the docs give `claude-web` (and `haiku-copilot`, `qwen-planning`), while the folders on disk are `claude-free-web` and `haiku-copilot` under `projects/agent-tasks/handoffs/`. On when a handoff is due, `SESSION_CLOSEOUT.md:9,19` says every research, implementation and web session MUST write one, and `PLANNING_AGENT_CLOSEOUT.md:55` says the same for every planning session. Tracy's position on 2026-10-19: handoffs are needed so sessions can share work; the concern is housekeeping, since they may not be needed forever. Layout also differs: `TEMPLATE-SESSION-HANDOFF.md` has six sections and a "Session Handoff — <Project> — date" title, while the 2026-10-15 and 2026-10-18 web handoffs use "How to start the next session", "What landed", "Not done yet", closeout and environment notes. Separately, `README.md:582` uses "handoff" for the 2-4 line dispatch message (`SIMPLE_HANDOFF_TEMPLATE.md`), a different thing from a session handoff. The 2026-10-15 handoff was missed at its first closeout and written on 2026-10-18 at Tracy's prompt, after a session that ran 2026-10-13 to 2026-10-15.
**Observed in**: agent-tasks project, web review sessions 2026-10-15, 2026-10-18 and 2026-10-19
**What I already checked**: the line references above (VERIFIED: fresh read-only clone of origin at `649f34c`, `git grep -n -i handoff`, 2026-10-19; the 2026-10-12 attribution VERIFIED by `git blame`). The folder listing (VERIFIED: `ls projects/*/handoffs/`). Retention already has a rule, but only in galaxy_game: `projects/galaxy_game/HOUSEKEEPING.md:16` says keep the newest handoff per agent and anything an open task references, and everything else is archive-eligible after 30 days (VERIFIED, same clone). `PLANNING_AGENT_CLOSEOUT.md:79` runs a housekeeping slice only if the project has a `HOUSEKEEPING.md`. Because the rule keeps the newest handoff per agent, a project idle for weeks or months still keeps its last handoff (my reading of that line, not tested). The layout comparison with the template is REPORTED (fetch tool, 2026-10-18); the title line of the template is VERIFIED.
**What needs a second opinion**: (1) DECIDED 2026-10-19 (Tracy): standardize on `<date>-<type>-CLOSEOUT.md`. Still to do: change the other four references (`HUMAN_WORKFLOW_GUIDE.md:50` and `:143`, `PLANNING_AGENT_SESSION_START.md:307`, `SESSION_CLOSEOUT.md:21`, `WEB_AGENT_SESSION_START.md:45`); settle the `<type>` values (`PLANNING_AGENT_CLOSEOUT.md:62` lists PLANNING, RESEARCH, IMPLEMENTATION; web review sessions have none); existing `session_handoff_*` and `-SESSION.md` files are history and keep their names. (2) One agent folder name per agent, for example `claude-web` or `claude-free-web`. (3) Keep "every session MUST write a handoff", or require one only when the session leaves work for another session or agent (Tracy's stated need)? (4) Extend the galaxy_game retention rule (newest per agent, plus anything an open task references, rest archive-eligible after 30 days) to all projects, and in which doc should it live? (5) Update `TEMPLATE-SESSION-HANDOFF.md` to the layout the recent web handoffs use, or require the old layout? (6) New file each time, or update the previous one when the next session continues the same work? Related OPEN entry: "Handoff file lifecycle undefined" (2026-10-13), which asks whether the template needs a "NEXT SESSION" section; the recent web handoffs already have one.
**Priority**: MEDIUM — affects every multi-session project
**Status**: OPEN. Question (1) decided 2026-10-19; the doc edits are not made yet

### 2026-10-18 — NEEDS_REVIEW headers and templates differ across projects
**What happened**: Three NEEDS_REVIEW files now have three headers. galaxy_game (`projects/galaxy_game/NEEDS_REVIEW.md`) and this file both open with "flagged for Claude (planning review) or human decision"; galaxy_game then addresses Qwen and this file addresses "Implementation agents". galaxy_game lists five triggers: gitignored or data paths, unverified completion, cross-task dependencies, disagreeing documents, repeated actions. This file lists five different triggers: friction across 3+ sessions, a working tool or pattern that needs documenting, a gap in guidance, contradictory patterns across projects, and a procedure worth recommending elsewhere. The knapsack file (`c07985a`) copies the galaxy header, except that its first trigger names "a gitignored or data path" instead of `data/json-data/`. None of the three templates has a `Priority` field, but all six entries in this file use one. A first version of the knapsack file by another model added an `Owner` line to the template, removed before the commit. The 2026-10-15 handoff listed the missing `Priority` field among the OPEN entries, but no such entry existed until this one. This file was renamed from `needs_review.md` to `NEEDS_REVIEW.md` in `d2c3242`. Eight lowercase references to the old name remain in three files: the 2026-10-15 handoff (3 lines), `projects/agent-tasks/status.md` (3 log lines) and the draft `projects/agent-tasks/tasks/drafts/2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md` (2 lines, in its Dependencies and Notes sections).
**Observed in**: agent-tasks project, web review session 2026-10-18
**What I already checked**: header and template differences between the galaxy_game and knapsack files, and the `Owner` line in the first version of the knapsack file (VERIFIED: Tracy's `diff` of the two header sections). This file's template block (VERIFIED: Tracy's `sed` output, 2026-10-18); its intro, triggers and the `Priority` line on each of its six entries (REPORTED: fetch tool, 2026-10-18). The lowercase references (VERIFIED: `git grep -c 'needs_review\.md'`, which matched only .md files in those three files). The live docs use the uppercase name (VERIFIED: `git grep -c 'NEEDS_REVIEW\.md'` lists `README.md`, `PLANNING_AGENT_SESSION_START.md`, `WEB_AGENT_SESSION_START.md`, `HUMAN_WORKFLOW_GUIDE.md` and `TEMPLATE-SESSION-HANDOFF.md`).
**What needs a second opinion**: (1) One shared header and template for all NEEDS_REVIEW files, or per-project headers? (2) Add `Priority` (and `Owner`?) to the template, since the entries already use `Priority`? (3) The draft's two lowercase paths: fix them when the draft is dispatched? It is another session's file, so they stay for now. The handoff and `status.md` lines are history and stay.
**Priority**: LOW — consistency only; no behaviour depends on it
**Status**: OPEN

### 2026-10-18 — Two closeout docs now both cover implementation sessions
**What happened**: Commit `352215b` added `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` at the repo root (VERIFIED: `git log --oneline --name-status 31925e9..0ca29be`). Its header says it is for implementation sessions only and that research and review sessions may use `SESSION_CLOSEOUT.md` instead (REPORTED: commit page read through a fetch tool, 2026-10-18). `SESSION_CLOSEOUT.md` was not changed in that range (VERIFIED: same command), and its header still says it is for research and implementation sessions, and review sessions (REPORTED: fetch tool, 2026-10-18). Both files carry the same rules, for example explicit-path staging and no edits to earlier `status.md` entries, so the text now lives in two places. The dispatch-interface block in the `589b5c5` draft still says "Closeout: follow SESSION_CLOSEOUT.md only" (REPORTED: fetch tool).
**Observed in**: agent-tasks project, web review session 2026-10-18
**What I already checked**: only the content of both files as read through a fetch tool (REPORTED). I did not run `git grep -n 'IMPLEMENTATION_AGENT_SESSION_CLOSEOUT'`, so I do not know which docs point at the new file. The closeout was split once before: `SESSION_CLOSEOUT.md` and `PLANNING_AGENT_CLOSEOUT.md` on 2026-10-11 (`b44b6c3`, `6462d0f`, `f5260b8`; REPORTED: `status.md` "Recent Closures").
**What needs a second opinion**: (1) Is this a deliberate second split, like the planning-agent one? (2) If yes: narrow the `SESSION_CLOSEOUT.md` header to research and review sessions, and update the places that name it for implementation agents (the dispatch interface in `TASK_TEMPLATE.md`, which the `589b5c5` draft already plans to edit, so sequence the two; plus `README.md` and `WEB_AGENT_SESSION_START.md` if they point at it). (3) If no: which file is the standard for implementation sessions, and should the other be removed?
**Priority**: MEDIUM — decides which procedure implementation agents follow
**Status**: OPEN

### 2026-10-20 — SIMPLE_HANDOFF_TEMPLATE.md conflicts with the TASK_TEMPLATE.md dispatch block; likely retired
**What happened**: Two documents define the prompt an agent receives at dispatch, and they disagree. The Agent Dispatch Interface block in `TASK_TEMPLATE.md` (lines 89-133) is mandatory in every task file and says agents receive its exact text as the startup contract. It runs Step 0 first (`git mv` backlog to active, set `status: active`, paste both outputs, read nothing else until then), then reads the task file, saves the synthesis report under `summaries/`, and closes out with `SESSION_CLOSEOUT.md`. `SIMPLE_HANDOFF_TEMPLATE.md` tells the agent to read `README.md` and `GUARDRAILS.md` first, then the task file, and then "move it", with no `git mv`, no pasted output and no closeout line. `HUMAN_WORKFLOW_GUIDE.md:78` offers both as alternatives. The `TASK_TEMPLATE.md` header comment calls its block "2-4 lines", but the block is about 25. A web review session built a dispatch prompt from the simple template on 2026-10-19; Tracy judged it the wrong format on 2026-10-20 and said the simple-handoff style is most likely no longer used.
**Observed in**: agent-tasks project, web review session 2026-10-19 to 2026-10-20
**What I already checked**: line numbers and the two blocks (VERIFIED: fresh read-only clone of origin at `5eb5a6e`, 2026-10-20). Live docs that name `SIMPLE_HANDOFF_TEMPLATE.md` (VERIFIED: `git grep`): `HUMAN_WORKFLOW_GUIDE.md:78` and `:211`; `README.md:96`, `:643`, `:711`, `:723`; `projects/agent-tasks/README.md:15` and `:42`; `projects/wvulibraries_knapsack/SESSION_GUIDANCE.md:342`; `projects/galaxy_game/PROJECT_TASK_TRACKING_TEMPLATE.md:288` and its copy under `generic-task-commands/`; `archive/README.md:16` (calls it a "variant … decide if needed"). Older planning documents and one backlog task (`2026-07-03-MEDIUM-AUDIT-STANDARDIZE-AGENT-TASK-WORKFLOW.md`) also name it. The file was last changed in `ab6547e` (2026-10-10, path placeholder). The two 2026-09-17 proposal documents wanted it designated the standard dispatch format; they are marked PROPOSAL and I did not verify that either was adopted. Task files containing an Agent Dispatch Interface block (VERIFIED counts): 57 backlog, 62 completed, 4 active, 8 draft. 49 task files contain the simple template's literal "READ FIRST:" wording; I did not check which block they carry. Not checked: whether any agent is still dispatched with the simple prompt.
**What needs a second opinion**: (1) Retire `SIMPLE_HANDOFF_TEMPLATE.md`? Tracy leans yes (2026-10-20, "most likely"; not a final decision). If yes, move it to `archive/` (no deletes, GUARDRAILS Rule 29) and update the live references above. (2) If it stays, align its startup order and closeout line with the `TASK_TEMPLATE.md` block. (3) The "Agent Handoff Generation Process" section of `README.md` (about lines 580-660) and `HUMAN_WORKFLOW_GUIDE.md:78` describe the 2-4 line style and need rewriting either way. (4) Fix the "2-4 lines" comment in `TASK_TEMPLATE.md`. Sequence this with the draft `2026-10-18-HIGH-DOCUMENTATION-TASK-TEMPLATE-INTERNAL-CONTRADICTIONS.md`, which also edits the dispatch block. Related OPEN entry: "TASK_TEMPLATE.md synthesis section contradictions" (2026-10-18).
**Priority**: MEDIUM — decides what every dispatched agent is told to do first
**Status**: OPEN. Deferred: out of scope for the 2026-10-19 to 2026-10-20 web review session.
