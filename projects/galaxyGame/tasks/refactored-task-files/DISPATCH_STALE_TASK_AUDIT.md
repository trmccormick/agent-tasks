# Dispatch Template — Stale Task Audit

**Status**: Revision 3 — supersedes `DISPATCH_STALE_TASK_AUDIT_TEMPLATE.md` and
`DISPATCH_EXAMPLE_PLANNING_AGENT.md`

**This is a two-part dispatch, not one.** Steps 1–2 (fast check + audit) require
filesystem access and must run on a **local Ollama agent** — Qwen or whatever model is
currently hosted locally (this replaced Copilot 0x-tier agents, it's not Qwen-specific).
Step 4 (design pass), if needed, goes to a **web agent** (Gemini, Claude, Perplexity) —
but web agents can't reach the filesystem, so you manually bridge by pasting/uploading
the local agent's saved summary into the web session.

---

## Part 1 — Dispatch to LOCAL Agent (Qwen / Ollama) — Fast Check + Audit

```
You are **STRATEGIST** for this session (per README.md role definitions).

Project: galaxy_game

Workflow: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/STALE_TASK_AUDIT_WORKFLOW.md
Read this in full before starting — it has the classification rules, the fast-resolution
check, and the evidence standard.

Also read:
1. /Users/tam0013/Documents/git/agent-tasks/README.md (STRATEGIST role section)
2. /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3. /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/TASK_UPDATE_GUIDE.md

STALE TASK TO AUDIT:
[paste the stale task file content here]

YOUR JOB TODAY (this is filesystem-access work — that's why it's you, not a web agent):
1. Run the Overlap & Related-Artifact Scan first (Step 1 of the workflow doc) — check
   for other task files describing the same work, and for orphaned supporting docs/
   scripts elsewhere in the repo that relate to this task but aren't linked from it.
   Note anything found, even if it doesn't change the classification.
2. Run the Fast Resolution Check (Step 2) — grep the codebase, check status.md, check
   commit history. If this task is already done, say so with evidence and stop there.
3. If not resolved, audit it fully (Step 3) and classify: STALE_SOLVED | STALE_OBSOLETE |
   INCOMPLETE | BLOCKED. If the audit turns up worthwhile work outside this task's scope
   (a reusable script, a consolidation need, a gap), note it separately as a
   **recommended new task** — don't fold it into this one.
4. If INCOMPLETE and the design needs updating for current codebase state, flag that a
   design pass with a web agent is needed — don't draft a task file with a scope you're
   not confident in. The human will bridge your findings to the web agent manually.
5. If the audit is clean and no design pass is needed, draft/refine the task file(s)
   yourself per Step 5, using TASK_TEMPLATE.md.

OUTPUT:
- Save your findings as a file — not chat paste — to:
  /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename: [YYYY-MM-DD]-[TOPIC]-AUDIT.md
  Include: the classification, the evidence supporting it (real commands + real output,
  not description), any overlap/related-artifact findings from Step 1, any recommended
  new tasks discovered during the audit, and your recommended next step
  (file it now / needs design pass).
- If drafting task file(s) directly, save those separately using TASK_TEMPLATE.md
  structure — these are the durable output, not the summary.

DO NOT:
- Implement anything
- Run tests or Docker commands beyond read-only research
- Make git commits without explicit approval
- Classify STALE_SOLVED/STALE_OBSOLETE without citing a specific file/line/commit

START:
1. Confirm you've read the workflow doc and understand the overlap scan and fast-
   resolution check come before the full audit
2. Tell me you're ready, then I'll confirm and you begin the audit
```

---

## Part 2 — Dispatch to WEB Agent (Gemini / Claude / Perplexity, ChatGPT as last resort) — Design Pass

**Only needed if Part 1 flagged INCOMPLETE + design pass required.** Manually copy or
upload the local agent's summary file (`summaries/[date]-[topic]-AUDIT.md`) into the web
agent's session — it has no way to reach that file itself.

```
You are doing a design/refinement pass on a stale task for galaxy_game.

A local agent already audited this task against the current codebase — findings below.
Your job is NOT to re-audit the codebase (you can't access it) — take the local agent's
findings as given, and work out the design/scope refinement needed.

[paste the full contents of the summary file here]

YOUR JOB:
1. Work out how to reconcile the original design intent (preserve what's still valid)
   with the current-state findings above
2. Decide if this is one refined task or should split into multiple
3. Draft the refined task file(s) using TASK_TEMPLATE.md structure

OUTPUT: Post the refined task file(s) in chat — the human will save these to disk and
route them through review before filing.
```

---

## After Both Parts Report Back

1. Open the summary file in `summaries/` — check the evidence is real (pasted
   command output), not asserted
2. If `STALE_SOLVED`/`STALE_OBSOLETE` — spot check the cited file/line/commit yourself
   before archiving; this is the highest-risk call in the workflow
3. If a design pass ran — save the web agent's refined task file(s) to disk yourself
   (or have the local agent save them, if you paste the content back to it)
4. Once task file(s) exist and you're satisfied — route through the normal REVIEWER
   tier system from `README.md` before filing to the phase folder
5. Once filed, the summary file in `summaries/` can be deleted — its job is done
6. If the summary flagged any **recommended new tasks** (reusable scripts found, a
   consolidation need, etc.) — these aren't created automatically. Decide separately
   whether to formally task them, using this same dispatch process if warranted.

---

## When to Use This

✅ Task is in `tasks/review/` and needs audit before it can move anywhere
✅ You want a STRATEGIST-role agent (any model) to triage before deciding next steps
✅ You're working through the backlog in batches

❌ Task is already in `tasks/active/` — use the standard EXECUTOR dispatch instead
❌ You already know the answer and just need a task file written — skip straight to
Step 5 of the workflow doc
