# Stale Task Audit Workflow

**Status**: Revision 2 — supersedes `PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md` and
`RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md`
**Purpose**: Work through the stale/review-folder backlog and turn each task into either
(a) an archived record of already-completed work, or (b) an updated task file routed to
the correct phase folder, preserving any design intent still worth keeping.
**Role Mapping**: This workflow does **not** introduce a new role. Auditing stale tasks is
**STRATEGIST** work per `README.md` ("create and update project task files," "triage").
Do not dispatch agents as "Planning Agent (Research Phase)" — dispatch them as STRATEGIST
and point them here.

**Local vs. Web Agents — Two Separate Constraints, Not One**: Steps 1–2 below require
direct filesystem and codebase access — reading task files, grepping code, checking
`status.md`, checking commit history.

- **Filesystem access**: Both local Ollama agents *and* GitHub Copilot agents can touch
  the filesystem. Pure web-chat agents (Gemini, Claude, Perplexity) cannot — they only
  see what's pasted or uploaded into the session.
- **"Local agent" in this doc specifically means Qwen run via the GitHub Copilot custom
  agent config** (tool-use verified, terminal access confirmed). It does not mean Continue
  — Continue is still installed but not part of the active workflow, and a Continue
  session cannot run commands or grep the codebase, only read files it's given. If Steps
  1–2 are ever dispatched to a Continue session by mistake, they will silently fail to
  do the filesystem work this doc assumes.
- **Cost**: Local Ollama-via-Copilot agents are free (0x). Copilot's own hosted agents
  used to be free at 0x tier too, but that tier was removed from the plan — those now
  carry a cost.

**Default for Steps 1–2 is local Qwen via the Copilot custom agent config** — free and
has the access these steps need. Cloud/paid Copilot agents are a fallback when local
capacity is unavailable, not the default, because they cost. Pure web-chat agents are
never used for Steps 1–2 regardless of cost, because they simply cannot reach the files.
Web-chat agents are used for Step 4 (design pass) and for cross-agent spot-checking on
what the local agent already found.

---

## Why This Exists

The backlog contains task files from the Feb–March 2026 stabilization sprint and other
earlier phases. Some of these are done in substance but were never closed. Others are
still-valid design ideas that never got finished. Treating these two cases the same way
wastes either archive effort or implementation effort. This workflow's whole job is to
tell them apart correctly, before anything gets rewritten or refiled.

**The design intent in a stale task file is not automatically stale.** Even a task with an
outdated approach, wrong phase label, or dead file references may contain economic,
mechanical, or architectural thinking that's still correct. Audit the task, not just the
prose — a task can be `INCOMPLETE` (real work) while also needing a Gemini design pass to
bring its concept in line with the current codebase before it's implementation-ready.

---

## The Four Classifications

| Classification | Meaning | Outcome |
|---|---|---|
| `STALE_SOLVED` | Work already done (incidentally or by another task), file never closed | Move to `tasks/deprecated/`. No new task file needed unless minor housekeeping remains. |
| `STALE_OBSOLETE` | Original problem no longer exists, or the project pivoted away from this approach | Move to `tasks/deprecated/` with a note on the pivot. New task file only if the pivot itself created new work. |
| `INCOMPLETE` | Design intent still valid, needs refinement to match current codebase | Refine and refile to the correct phase folder. May need a Gemini design pass first if scope or approach needs updating. May split into multiple task files. |
| `BLOCKED` | Can't proceed until another task/decision completes | Note the blocker explicitly; leave in `tasks/review/` or route to backlog with a documented dependency, not to an active phase folder. |

**A `STALE_SOLVED` or `STALE_OBSOLETE` call is the highest-stakes classification here** —
getting it wrong means real, still-needed work gets archived as done. This classification
requires the same evidence standard as everything else in this project: a specific
file/line/commit where the equivalent functionality now lives, not "this appears to be
handled already."

---

## Workflow Steps

### Step 1 — Overlap & Related-Artifact Scan (Local Agent Only — do this before the Fast Resolution Check)

**Requires filesystem access — same local agent as the rest of Steps 1–3.**

Stale tasks in this backlog have shown two related but distinct overlap problems:

1. **Duplicate/competing task files** — the same underlying work described in more than
   one task file, sometimes with different priority, phase, or scope, and no
   cross-reference between them. (Real example: `TASK_GUARDRAILS_SPLIT.md` and
   `TASK_WEDNESDAY_SURGICAL_AUDIT.md` both independently describing a split of the same
   `docs/GUARDRAILS.md` file, created the same day, never linked to each other.)
2. **Orphaned supporting artifacts** — design docs, scripts, or asset specs elsewhere in
   the repo that relate to this task but aren't referenced by it. (Real example: a
   settlement-view audit only discovered `PHASE_3_SPRITE_SHEET_PROMPT.md`,
   `CHROMAKEY_PHASE3_README.md`, and `scripts/chromakey_spritesheet.py` incidentally,
   during codebase research — none were linked from the original task file.)

**Before auditing the task itself**, search for both:
- Other task files (in `review/`, `backlog/`, `active/`) referencing the same source
  file(s), subsystem, or close keyword matches — check filenames and a quick read of intent
- Related non-task files elsewhere in the repo (planning docs, scripts, specs) that
  mention the same subject but aren't cross-linked from the task being audited

If overlap is found:
- **Duplicate task files** → do not audit in isolation. Note the relationship in the
  summary, and either audit both together as one pass or explicitly flag which one is
  authoritative and why, before drafting any refined task file.
- **Orphaned supporting artifacts** → note them in the summary as reusable/relevant
  context, even if they don't change the classification. These often represent real,
  already-done groundwork (e.g. a sprite-generation script) that shouldn't be redone.

**GUARDRAILS.md specifically has a recurring multiple-copies-multiple-locations problem**
(distinct from the two-task-file case above) — there is a `docs/GUARDRAILS.md`
(project-level) and an `agent-tasks/rules/GUARDRAILS.md` (generic agent execution rules,
referenced throughout this workflow and `README.md`). This ambiguity has already produced
at least two independent, uncoordinated task files trying to fix it.

**Resolved (human-confirmed, do not re-litigate this during audit)**:
- `agent-tasks/rules/GUARDRAILS.md` is the authoritative generic file — applies across
  all projects (Galaxy Game, Samvera, WVU Libraries). This one is correct as-is.
- `docs/GUARDRAILS.md` is Galaxy Game–specific but currently mixes several things that
  don't belong together and need to be sorted into four destinations, not two:

  1. **Truly generic agent rules that already exist in `agent-tasks/rules/GUARDRAILS.md`**
     → these are duplicates. Discard from `docs/GUARDRAILS.md`, don't re-merge.
  2. **Generic-sounding agent rules that are NOT yet in `agent-tasks/rules/GUARDRAILS.md`**
     → these are real gaps in the generic file. Diff carefully against what's already
     there (don't assume something's missing without checking), then merge the genuinely
     new content into `agent-tasks/rules/GUARDRAILS.md`.
  3. **Genuinely Galaxy Game–specific process/agent rules** (not generic, not game
     design) → move to a Galaxy Game–specific project rules file — do not name it
     "GUARDRAILS," to avoid recreating this exact confusion.
  4. **Game design / system intent content** → architecture/gameplay/systems/flavor
     docs, per the routing both `TASK_GUARDRAILS_SPLIT.md` and
     `TASK_WEDNESDAY_SURGICAL_AUDIT.md` already proposed.

  Category 2 is the one requiring the most judgment — it's a real audit task (read each
  section, decide generic vs. project-specific, check for duplication against the
  existing generic file), not a mechanical split. Don't let it get flattened into
  category 3 just because it currently lives in the project-level file.

### Step 2 — Fast Resolution Check (Local Agent Only)

**Requires filesystem access — run this on a local Ollama agent, not a web agent.**

Before investing research or design effort, check the cheap thing first:
- Search `status.md` for any mention of this task's subject matter
- Grep the codebase for equivalent functionality under a different name
- Check recent commit history for anything that plausibly closes this task's intent

If this turns up clear evidence the task is done → classify `STALE_SOLVED`, cite the
evidence, stop here. Skip straight to filing (Step 6). Don't spend a design pass or
implementation-grade research on something already finished.

If nothing turns up → proceed to Step 3.

### Step 3 — Audit Against Current State (Local Agent Only)

**Also requires filesystem/codebase access — same local Ollama agent as Steps 1–2.**

Answer, with evidence for each:
1. Has this work already been done? (should already be answered by Step 2, confirm)
2. Is the original approach still valid, or has the project pivoted since?
3. Are there new blocking dependencies that didn't exist when the task was written?
4. Is the scope still accurate as written, or does it need to be split or merged with
   related work?

Classify using the table above.

**If the audit surfaces additional work worth doing that isn't part of the original
task's scope** — a reusable script found during research, a consolidation need (like the
GUARDRAILS duplication above), or a follow-up gap — don't fold it into this task's
refined file. Note it separately in the summary as a **recommended new task**, with
enough detail that a future STRATEGIST session can decide whether to create it. This
keeps each refined task's scope honest instead of quietly growing beyond what it was
audited for.

### Step 4 — Design Pass (Web Agent — You Are the Bridge)

If the design intent is still good but needs to be reconciled with the current
codebase — updated file paths, changed dependencies, refined scope, or split into
multiple tasks — hand this to a web agent (Gemini, Claude, Perplexity) for a design pass.
ChatGPT is a **last resort** for this step — not excluded, but reach for Gemini/Claude/
Perplexity first; bring in ChatGPT when you want another angle or the others haven't
landed on a good approach. Also note ChatGPT's free tier is generally reserved for image
generation work, so treat this as an occasional pull, not a rotation default.

**Web agents cannot reach the summaries folder themselves.** You copy/upload the local
agent's summary file content into the web agent session manually. The web agent works
only from what you give it — it does no independent codebase or filesystem checking.
This is a hard constraint, not a workflow choice: web agents simply have no access to
the machine where the files live. Gemini may produce one refined task file, or multiple
if the original scope has grown too broad for one task.

This step is skippable if the STRATEGIST's Step 3 audit already produced a
clean, current, single-scope refinement — don't route it to Gemini just because the
workflow has a slot for it.

### Step 5 — Draft or Refine the Task File(s)

Using `TASK_TEMPLATE.md`, produce task file(s) that:
- Preserve the original design intent that's still correct
- Remove stale assumptions (old file paths, deprecated models, outdated approach)
- Have accurate prerequisites and success criteria for the current codebase
- Reference the original stale task for traceability

### Step 6 — File It

- `STALE_SOLVED` / `STALE_OBSOLETE` → move original to `tasks/deprecated/` once the
  refined/replacement work (if any) is created and approved
- `INCOMPLETE` (refined) → once approved:
  - If ready for immediate executor pickup → `tasks/active/`
  - If queued for a later phase, not yet actionable → correct `tasks/backlog/phase{N}+/`
  - Move the original stale file to `tasks/deprecated/` once its replacement is approved
- `BLOCKED` → leave in `tasks/review/` or `tasks/backlog/` with the blocker documented
- Superseded intermediate drafts (e.g. prior audit attempts, refactored-task-files
  working drafts) → same treatment as the original: `tasks/deprecated/` once the final
  consolidated version is approved
- Working-memory handoff files (`_working-temp/`, superseded summary files) → delete,
  not archive — their findings should already be captured in the final task file

Follow the standard commit process from `README.md` (stage, post diff, wait for
approval — no autonomous commits).

If Step 3 flagged any **recommended new tasks**, list them in the filing summary too —
these are candidates for a future STRATEGIST session to formally create, not something
this audit creates itself.

---

## Evidence & Output — Save to Summaries, Not Chat

Do not paste research findings, synthesis reports, or command output into chat as the
primary record. Save them as a file to:

```
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
```

This lets the same file be handed directly to any other agent (Gemini, Claude, Qwen)
without retyping or lossy copy-paste, and keeps chat sessions shorter.

**The summaries folder is working memory, not permanent record.** Once a task is filed
(Step 6) or the audit concludes, the summary file that supported it has done its job.
It can be deleted — the durable outputs are the task file and `status.md`, not the
summary. If a future agent needs current codebase state again, that's a fresh Qwen
research pass, not an old summary.

**Every summary must include real evidence**, matching the standard already enforced
elsewhere in this project: the actual command run and the actual output returned — not
a description of what the output probably showed. This applies most strictly to
`STALE_SOLVED`/`STALE_OBSOLETE` calls, since those are the ones that archive work.

---

## Do NOT Do

- Run code, tests, or terminal commands beyond read-only research
- Make git commits without explicit human approval (per `GUARDRAILS.md` Rule 26)
- Modify codebase files
- Implement anything — this is audit and task-drafting only
- Classify `STALE_SOLVED`/`STALE_OBSOLETE` without a specific file/line/commit citation
- Treat the summaries folder as permanent — clear it once findings are absorbed into a
  task file or `status.md`

---

## Reviewer Gate

Once task file(s) are drafted, they go through the standard `README.md` review path —
Tier 1 (you) always, Tier 2 (Claude web, Gemini, or Perplexity, by fit not cost) as
needed for complex or ambiguous routing calls. There is no separate "Approval Phase"
role for this workflow; use the existing REVIEWER role and tier system.
