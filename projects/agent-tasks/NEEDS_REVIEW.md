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
**Status**: OPEN. Decided 2026-10-19 (Tracy): file name `<date>-<type>-CLOSEOUT.md` and agent folder `claude-free-web`; applied to five live docs in `533a0c5`. Leftover `-SESSION.md` wording remains in `SESSION_CLOSEOUT.md:22` and `:118`; on 2026-10-20 Tracy paused renaming work, so it is not part of the next doc pass unless she says so. Question (3) DECIDED 2026-10-21 (Tracy): handoff files are written by review agents (web agents in reviewer mode) and planning agents, so the next session has the information; implementation agents write no handoff file, they put findings and closeout in the task file and add a short summary at the end of `status.md`, and the commit plus the task file must hold what a reviewer needs. A `summaries/` report stays valid for research and other cases but is not required every time. Question (4) retention is not yet answered; the proposal on the table is the galaxy_game keep-and-archive rule stated once in `PLANNING_AGENT_CLOSEOUT.md` for all projects.

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
**Status**: OPEN. Decided 2026-10-20 (Tracy): retire `SIMPLE_HANDOFF_TEMPLATE.md` by moving it to `archive/`; the task's own Agent Dispatch Interface is the dispatch prompt. Replace the live references (`HUMAN_WORKFLOW_GUIDE.md:78` and `:211`, `README.md:96` and `:723`, rewrite `README.md:643`, `:711` and the "Agent Handoff Generation Process" section, `projects/agent-tasks/README.md:15` and `:42`, `archive/README.md:16`); leave the two galaxy_game template files for Tracy; the knapsack `SESSION_GUIDANCE.md:342` line goes with the guidance edits. Doc edits not made yet.

### 2026-10-20 — Review gate: two disposition vocabularies and several evidence-label sets
**What happened**: The review gate added on 2026-10-20 names its outcomes two ways. `REVIEW_AGENT_GUIDE.md`, the review-gate section of `TASK_TEMPLATE.md` (from line 284), the review-gate section of `SESSION_CLOSEOUT.md` and `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md` use GO / REVISE / STOP. `REVIEW_AGENT_WORKFLOW.md` §4 and `REVIEW_REPORT_TEMPLATE.md` use PROCEED / PROCEED WITH CAUTION / STOP, while the dispositions table lower down in the same workflow file uses GO / REVISE / STOP. REVISE (fix and resubmit) and PROCEED WITH CAUTION (go ahead with a note) mean different things, so the two sets do not map one to one. Evidence labels also differ: `PLANNING_AGENT_SESSION_START.md:35-37` has VERIFIED / REPORTED / INFERRED, `REVIEW_REPORT_TEMPLATE.md` says every claim must be VERIFIED or REPORTED, `REVIEW_AGENT_GUIDE.md` "Evidence Standards" has direct verification / supplied evidence / dated claims, and the 2026-10-20 planning closeout describes the planning set as VERIFIED / REPORTED / ASSUMED, which the file does not contain.
**Observed in**: agent-tasks project, web review session 2026-10-20 (review artifact `projects/agent-tasks/summaries/2026-10-20-REVIEW-WORKFLOW-UPDATES-HAIKU-COPILOT.md`)
**What I already checked**: all of the above (VERIFIED: fresh read-only clone of origin at `82f9f23`, 2026-10-20; `git grep` for the labels and a full read of the review docs). Not checked: whether any recent review artifact already uses one set or the other.
**What needs a second opinion**: (1) One disposition set for the whole gate. If GO / REVISE / STOP, change `REVIEW_AGENT_WORKFLOW.md` §4 and `REVIEW_REPORT_TEMPLATE.md` (and say where PROCEED WITH CAUTION went); if the PROCEED set, change the four docs above. (2) One evidence-label set, or an explicit mapping (for example, does pasted output from Tracy count as VERIFIED or as "supplied evidence"?). (3) Correct the closeout's "ASSUMED" wording or the file.
**Priority**: MEDIUM — reviewers and implementers will use different words for the same decision
**Status**: OPEN. Decided 2026-10-20 (Tracy): (1) GO / REVISE / STOP everywhere, so `REVIEW_AGENT_WORKFLOW.md` §4 and `REVIEW_REPORT_TEMPLATE.md` change (and the governance plan line); (2) VERIFIED / REPORTED / INFERRED everywhere, with output Tracy pastes labeled REPORTED and its source named, for example `REPORTED (pasted by Tracy, date)`; a local agent may verify when a claim is material, but Tracy running a simple command is fine. The labels change in `WEB_AGENT_SESSION_START.md`, `REVIEW_AGENT_WORKFLOW.md`, `REVIEW_REPORT_TEMPLATE.md` and the `REVIEW_AGENT_GUIDE.md` evidence section. Doc edits not made yet.

### 2026-10-20 — TASK_FILE_LIFECYCLE_VALIDATION.md conflicts with commit, delete, Step 0 and chat rules
**What happened**: `rules/TASK_FILE_LIFECYCLE_VALIDATION.md` (new on 2026-10-20) disagrees with other live rules in five places. (1) Its backlog-to-active and active-to-completed transitions include `git commit` by the agent, while Rule 26 allows an agent to commit only after an explicit human approval and the closeout docs say the human commits and pushes, so a bare `git commit` step has no approval gate. (2) It says "Use `git mv` always. Never `cp`, never plain `mv`", while the `TASK_TEMPLATE.md` dispatch block allows plain `mv` then `git add` for a new untracked file. (3) It says to run `find` before ANYTHING with a task file, while the dispatch block's Step 0 says to run the `git mv` first and read or run nothing else until it is done. (4) It says to paste `find` output in chat, while the dispatch block says chat is for questions only; this extends the OPEN HIGH entry "TASK_TEMPLATE.md synthesis section contradictions" (2026-10-18). (5) Its incident section says two stale untracked copies of the review-artifact task were "removed during closeout validation", while the same file says not to use `rm` and GUARDRAILS Rule 29 requires approval for deletes; the text does not say who approved the removal.
**Observed in**: agent-tasks project, web review session 2026-10-20
**What I already checked**: the rule file, the `TASK_TEMPLATE.md` dispatch block and the `PLANNING_AGENT_CLOSEOUT.md` addition (VERIFIED: read in a fresh read-only clone of origin at `82f9f23`, 2026-10-20). Not checked: whether the removal in (5) was approved, and what the removed copies were (the text gives MD5 values only).
**What needs a second opinion**: (1) May the lifecycle rule tell agents to commit, or should the commit lines become "prepare and stage; human commits"? (2) One rule for new untracked files (`mv` then `git add`, or something else). (3) Which comes first at Step 0, the `find` check or the `git mv`? (4) Was the removal in the incident approved; if so, say so in the file. Sequence with the TASK_TEMPLATE contradictions draft, which also edits the dispatch block.
**Priority**: MEDIUM — the rule says "required for all agents" and contradicts the hard rules
**Status**: OPEN. Decided 2026-10-20 (Tracy): (1) the lifecycle rule follows Rule 26: agents may commit and push when Tracy tells them to, and confirming the command in an agent's own approval prompt (for example Copilot's) counts as approval; (2) a new untracked task file may use `mv` then `git add`, as GUARDRAILS already says; (3) Step 0 is `git mv` first, then the `find` check, as in the dispatch block; (4) stray copies are reported and escalated, and removed only with Tracy's approval, so `GUARDRAILS.md:242` ("remove it before committing") is reworded to match `:660` and Rule 29. Who approved the removal in the incident is still unanswered. Doc edits not made yet.

### 2026-10-20 — New workflow guides: duplicate versions, repeated sections, stale details
**What happened**: `MODEL_SELECTION_GUIDE.md` (87 lines) and `PLANNER_ROLE_BOUNDARIES.md` (176 lines) exist at the repo root, and different, longer versions exist as `docs/MODEL_SELECTION_GUIDE.md` (244) and `docs/PLANNER_ROLE_BOUNDARIES.md` (256). Neither version links to the other. The `docs/` versions came in `9f2ae23` and the root versions in `82f9f23`; the 2026-10-20 planning closeout lists only the `docs/` ones. Both `docs/` files call the human "Timothy" (`docs/MODEL_SELECTION_GUIDE.md:5`, `docs/PLANNER_ROLE_BOUNDARIES.md:110`) and `docs/MODEL_SELECTION_GUIDE.md` recommends "Claude 3.5 Sonnet"; I did not check whether those are current. `docs/MODEL_SELECTION_GUIDE.md:238` also lists `SIMPLE_HANDOFF_TEMPLATE.md` as the "Minimal dispatch format", a new live reference to the file the OPEN entry "SIMPLE_HANDOFF_TEMPLATE.md conflicts with the TASK_TEMPLATE.md dispatch block; likely retired" proposes to retire. Separately, `REVIEW_AGENT_GUIDE.md` repeats its "Scope of Review", "Reviewer Authority" and commit-authority sections and `REVIEW_AGENT_WORKFLOW.md` repeats the commit chain, although both say not to duplicate shared rules; the guide's "Last Updated" is still 2026-10-06 and its configuration notes list private LAN addresses (10.6.186.x) in a public repo.
**Observed in**: agent-tasks project, web review session 2026-10-20
**What I already checked**: file sizes, headings, opening lines, commit dates and cross-references (VERIFIED: fresh read-only clone of origin at `82f9f23`, 2026-10-20). I read the `docs/` guides only to their opening sections, so their content beyond that is unreviewed.
**What needs a second opinion**: (1) Which version of each guide stays, and should the other move to `archive/`? (2) Correct "Timothy" and the model names. (3) Remove the repeated sections from the review docs, update the guide's date, and decide whether the LAN addresses belong in a public repo. (4) Include the `docs/MODEL_SELECTION_GUIDE.md:238` reference in the SIMPLE_HANDOFF retirement.
**Priority**: LOW — clutter and stale detail, no behaviour depends on it
**Status**: OPEN. Decided 2026-10-20 (Tracy): keep and fix `docs/PLANNER_ROLE_BOUNDARIES.md` (remove "Timothy" and model names, reword commit authority per the lifecycle decision); archive the root `PLANNER_ROLE_BOUNDARIES.md` and both `MODEL_SELECTION_GUIDE.md` files; carry the budget idea to `notes/AGENT_NOTES.md` as a dated REPORTED entry, worded as guidance: there is no fixed agent ladder, the local planning agent may suggest a model by need and by session time left, and the choice is Tracy's preference. Also decided: remove the configuration block at `REVIEW_AGENT_GUIDE.md:121-139` (Copilot and Continue setup, LAN addresses); Continue is installed but unused, so remove the "No Continue Sidebar" line at `README.md:660`, leave the historical `rules/AGENT_ROUTING.md`, and archive `rules/TRIAGE_GUIDE.md` whole; add a one-line note under Continue in `AGENT_NOTES.md`. Not yet decided: the repeated sections in the review docs and the guide's "Last Updated" date. Doc edits not made yet.

### 2026-10-21 — WORKFLOW GOVERNANCE: Planning Agent diagnostic findings checkpoint gap (affects all multi-agent projects)
**What happened**: During Galaxy Game UI CSS compilation diagnosis, Planning Agent (Copilot) correctly diagnosed the root cause (Tailwind v4 source not compiling) and proposed a fix. Instead of creating a task file and waiting for human GO, Planning Agent executed the fix directly: modified startup.sh, compiled CSS, restarted containers, verified output. This pattern violates the authority boundary between "Planning Agent (diagnose + recommend)" and "Implementation Agent (execute)".

**Observed in**: Galaxy Game UI project, Planning Agent session 2026-10-21 (trigger: CSS compilation issue during remediation phase).

**System-wide impact**: ALL multi-agent projects. Affects how Planning Agents handle diagnostic findings in all future sessions across all six active projects (galaxy-game-ui, galaxy_game, samvera_hyku, wvu_knapsack, wvu_auth, acda_portal).

**What I already checked**: Workflow docs (VERIFIED: PLANNING_AGENT_SESSION_START.md §1 says "do research and preparation, not implementation"; GUARDRAILS.md Rule 26 restricts commits to human-approved work; SESSION_CLOSEOUT.md defines review gates; no explicit checkpoint rule found requiring Planning Agents to create task files for diagnostic findings before executing fixes).

**Root cause**: PLANNING_AGENT_SESSION_START.md doesn't describe the diagnostic-findings workflow. Gap allows Planning Agents to skip the required step: diagnose → **task file creation** → human GO → Implementation Agent executes.

**What needs a second opinion**: Core workflow governance question: What is the explicit checkpoint that prevents Planning Agents from becoming de-facto implementation executors? Current docs allow diagnosis and recommendation but lack a written "stop here, create a task file" rule for infrastructure fixes.

**Recommended solution**: Update three core workflow documents with insertion-ready text blocks:
1. **PLANNING_AGENT_SESSION_START.md** — Add "Diagnostic Findings Workflow" section (checkpoint: diagnose → task file → wait for GO)
2. **rules/GUARDRAILS.md** — Add "Planning Agent Authority (MAG-P)" rule (define what Planning Agents may/may not do)
3. **TASK_TEMPLATE.md** — Add "Planning Agent Checkpoint" criterion (define when task files are required; what infrastructure fixes trigger it)

**Priority**: MEDIUM — affects all future Planning Agent sessions; preserves human authority over approval gates and prevents role drift

**Status**: OPEN. Task file created 2026-10-21 at `projects/agent-tasks/tasks/backlog/2026-10-21-MEDIUM-PLANNING-AGENT-DIAGNOSTIC-WORKFLOW.md` with insertion-ready text blocks ready for Claude workflow governance review session. Not a project-specific fix; a system-wide workflow governance adjustment applicable to all projects.


### 2026-10-22 — Draft-gate commit 442a462: conflicts with earlier decisions, and decisions recorded
**What happened**: Commit `442a462` added the lifecycle drafts → review → backlog → active → completed (Rule 12, `TASK_TEMPLATE.md`, `PLANNING_AGENT_SESSION_START.md` Rule 7 and Step 1.5, `REVIEW_AGENT_GUIDE.md` draft-review section, root `MODEL_SELECTION_GUIDE.md` capability table) after Haiku placed eight galaxy-game-ui tasks directly in `active/` during a workflow test (`74b9f65`; the prototype files stay as they are, Tracy's choice). Findings from the diff: Rule 7 and the Step 2 procedure let a planner commit and push on its own; Rule 12 still lists `drafts` as a backlog subfolder and says backlog has no date folders while `TASK_TEMPLATE.md:10` says `backlog/{YYYY-MM}/`; "remove it before committing" (stray copy) and "paste find output" remain; the new DRAFT TASK REVIEW format has no evidence labels and differs from `REVIEW_REPORT_TEMPLATE.md`; `SIMPLE_HANDOFF_TEMPLATE.md` got 8 new lines and the lifecycle diagrams still show a `[handoff]` step.
**Observed in**: agent-tasks workflow test 2026-10-22 (Haiku, new-task creation); review of origin at `442a462`
**What I already checked**: VERIFIED: the `442a462` diff of the six docs, and no `backlog/drafts` folder exists in any project (origin tree). Not read: the 509-line task file added in the same range, and Tracy's failure analysis.
**What needs a second opinion**: (1) Draft-review output: use `REVIEW_REPORT_TEMPLATE.md` with a draft-review variant and VERIFIED / REPORTED / INFERRED labels, or keep the new format? (2) Do the Rule 12 lines about stray-copy removal and pasting `find` output get reworded with the lifecycle task?
**Priority**: MEDIUM — the draft gate is the main fix for planners self-approving tasks
**Status**: OPEN. Decided 2026-10-22 (Tracy): (a) a planner commits and pushes drafts when Tracy tells it to, so web review agents can read the repo, and the task stays in `drafts/` until she approves; reword Rule 7 and Step 2. (b) Root `MODEL_SELECTION_GUIDE.md` stays as a living capability reference (no ranking or fixed ladder, dated and labelled rows); the `docs/` copy is archived; this replaces the 2026-10-20 decision to archive both copies. (c) `SIMPLE_HANDOFF_TEMPLATE.md` is retired (the dispatch block is the minimal handoff), including its new lines and the `[handoff]` steps. (d) `backlog/` holds approved tasks only: remove `drafts` from the Rule 12 backlog subfolder list and `{YYYY-MM}` from `TASK_TEMPLATE.md:10`.

### 2026-10-22 — Implementation Agent Docker container execution rule not prominently documented
**What happened**: During Task 1 (MockDataHelper enhancement) implementation for galaxy-game-ui, Qwen (implementation agent) ran `ruby -c` syntax check on host system instead of inside Docker container. This created version-mismatch risk: host Ruby version differs from container Ruby version, so syntax checks passing on host might fail silently in container at runtime. Agent self-corrected when pointed out (`docker exec galaxy_game ruby -c /home/galaxy_game/app/services/mock_data_helper.rb`), but the rule should be documented prominently enough to prevent repeating this pattern across projects and sessions.

**Observed in**: galaxy-game-ui project, implementation session 2026-10-22 (Qwen, Task 1 - MockDataHelper).

**Project-wide lesson**: For galaxy-game-ui specifically (and any containerized Rails project), the rule is:
- File editing tools work via volume mount (`/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/...` on host syncs to `/home/galaxy_game/...` in container)
- ALL Ruby/Rails/system commands MUST run inside container: `docker exec galaxy_game <command>`
- Container app root is `/home/galaxy_game/` (NOT `/home/galaxy_game/galaxy-game-ui/`)
- Do NOT run Ruby commands on host system

**System-wide applicability**: This affects all containerized projects in the agent-tasks multi-project structure. Currently only galaxy-game-ui uses Docker; if samvera_hyku or wvu_knapsack adopt containers, this pattern applies.

**What I already checked**: The galaxy-game-ui Dockerfile.dev line `ADD ./galaxy-game-ui /home/galaxy_game` (VERIFIED: maps host `./galaxy-game-ui/*` to container `/home/galaxy_game/*`); the docker-compose volume mount `./galaxy-game-ui:/home/galaxy_game` (VERIFIED); Qwen's correction to `docker exec galaxy_game ruby -c /home/galaxy_game/app/services/mock_data_helper.rb` (VERIFIED: syntax check passed in container).

**What needs a second opinion**: (1) Should this rule be added to GUARDRAILS.md as a numbered rule (e.g., "Rule 30: Container Execution")? (2) Should galaxy-game-ui have a project-specific DOCKER_RULES.md or similar? (3) Should it be added to the galaxy-game-ui PROJECT_README or QWEN_HANDOFF? (4) Should it be emphasized in the `TASK_TEMPLATE.md` Agent Dispatch Interface block for containerized projects (conditional on project config)?

**Recommended solution**: One or more of:
- Add "Rule 30: Container Execution" to `rules/GUARDRAILS.md` (general principle, applies to all containerized projects)
- Add "Docker Execution Rule" section to `projects/galaxy-game-ui/README.md` or `projects/galaxy-game-ui/DOCKER_RULES.md` (project-specific implementation)
- Update galaxy-game-ui `QWEN_HANDOFF.md` section to include Docker rule prominently (agent-specific context)
- Add a conditional note in `TASK_TEMPLATE.md` Agent Dispatch Interface for containerized projects

**Priority**: MEDIUM — prevents version-mismatch bugs in containerized projects; affects galaxy-game-ui immediately and other containerized projects in future

**Status**: OPEN. Discovery checkpoint: Qwen self-corrected and learned the rule. No additional implementation issues observed after correction. Needs human decision on documentation placement and emphasis level.
