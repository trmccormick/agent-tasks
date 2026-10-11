# Small edits to apply (agent-tasks repo)

## A. TASK_TEMPLATE.md
1. In the YAML block (line ~63, after `status: backlog`) add one line:
   `resume: false`
2. Before `## Completion Report` (line ~461) insert:

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

3. In the dispatch prompt, after the STEP 0 move instructions (line ~101), add:

   If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step",
   and set `resume: false` when you move the file to `active/`. Do not restart the task.

   And replace any pointer to a closeout file with: "Closeout: follow SESSION_CLOSEOUT.md only."

## B. PLANNING_AGENT_SESSION_START.md
- Line ~73: change "using `SESSION_CLOSEOUT.md` (read its section 0 first)" to
  "using `PLANNING_AGENT_CLOSEOUT.md`".
- Line ~79: change "as `SESSION_CLOSEOUT.md` section 1c describes" to "as `PLANNING_AGENT_CLOSEOUT.md` section 5 describes".
- Line ~81 (maintenance paragraph): add "See `PLANNING_AGENT_CLOSEOUT.md`."
- Line ~97: keep "Follow `SESSION_CLOSEOUT.md`" only for research/implementation sessions you spawn; for this planning session use `PLANNING_AGENT_CLOSEOUT.md`.

## C. README.md
Add to the file index, next to SESSION_CLOSEOUT.md:
`PLANNING_AGENT_CLOSEOUT.md` — planning session closeout and workspace maintenance.
Describe `SESSION_CLOSEOUT.md` as "research and implementation sessions (including stop-early)".

## D. GUARDRAILS.md (optional, only if a rule cites section 1c, 6 or 7 of SESSION_CLOSEOUT.md)
Run: `grep -n "SESSION_CLOSEOUT" rules/GUARDRAILS.md` and repoint any "section 1c / 6 / 7" reference to PLANNING_AGENT_CLOSEOUT.md.

## E. status.md (agent-tasks and each project)
Optional, planning agent adds later: an `## Open Sessions` table (columns in PLANNING_AGENT_CLOSEOUT.md section 5).
