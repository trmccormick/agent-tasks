# Dispatch — GUARDRAILS Consolidation (Ryzen Qwen)

**This is NOT a fresh audit.** Substantial research already exists from 2026-07-01.
Your job is to consolidate it, resolve the one remaining open item, and produce a
single unified, executable task file — not to re-investigate settled questions.

---

## Copy-Paste This

```
You are **STRATEGIST** for this session (per README.md role definitions).

Project: galaxy_game

⚠️ TWO SEPARATE REPOS — do not confuse them:
- Agent workflow/task files live in: /Users/tam0013/Documents/git/agent-tasks/
  (this is the real path; galaxyGame's docs/new_agent/ is a symlink pointing here —
  use the real agent-tasks/ path directly, do not use the symlink path)
- Game code + game documentation (including GUARDRAILS.md) live in a SEPARATE repo:
  /Users/tam0013/Documents/git/galaxyGame/ — docs/ is directly under this repo, not a
  symlink, not reachable through agent-tasks/

CONTEXT: GUARDRAILS.md consolidation has already had significant research done
2026-07-01. Do NOT re-audit from scratch. Your job is to consolidate existing findings,
resolve one remaining open item, and produce ONE unified task file ready for executor
handoff.

READ FIRST (prior research — treat as settled unless you find contradicting evidence):
1. [paste contents of 2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md]
2. [paste contents of GUARDRAILS_REF_PACKAGE.md]
3. [paste contents of TASK_REF_GUARDRAILS_MIGRATION_V2.md]
4. [paste contents of 2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md]
5. [paste contents of 2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md]

EXACT PATHS TO USE (do not guess, do not vary these):
- Source file being audited: /Users/tam0013/Documents/git/galaxyGame/docs/GUARDRAILS.md
- Generic agent rules (authoritative, do not edit): /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
- Task template: /Users/tam0013/Documents/git/agent-tasks/TASK_TEMPLATE.md
- Phase structure reference: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md
- Summaries output folder: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
- Task files folder (backlog root): /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/
- Prior GUARDRAILS task files to check/supersede — confirm their exact current location
  with `find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/ -iname "*guardrails*"`
  before assuming any path for them; do not guess their folder.
- Game architecture doc targets (for the four-way sort destinations):
  /Users/tam0013/Documents/git/galaxyGame/docs/architecture/
  /Users/tam0013/Documents/git/galaxyGame/docs/mission_profiles/
  /Users/tam0013/Documents/git/galaxyGame/docs/gameplay/
  /Users/tam0013/Documents/git/galaxyGame/docs/flavor/
  /Users/tam0013/Documents/git/galaxyGame/docs/systems/ (may not exist yet — verify)

ALREADY RESOLVED (human-confirmed, do not re-litigate):
- Canonical file: /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md is the
  authoritative GENERIC agent rules file, applies across all projects. Correct as-is,
  do not trim/edit it directly.
- /Users/tam0013/Documents/git/galaxyGame/docs/GUARDRAILS.md (Galaxy Game–specific, 680
  lines) sorts into FOUR destinations:
  1. Generic rules already covered in agent-tasks/rules/GUARDRAILS.md → duplicates,
     discard from docs/GUARDRAILS.md
  2. Generic rules genuinely MISSING from agent-tasks/rules/GUARDRAILS.md → real gaps,
     merge into agent-tasks/rules/GUARDRAILS.md (diff carefully, don't assume — check
     the file's actual current content before merging anything in; prior research cited
     425 lines but that may have drifted since 2026-07-01, re-verify)
  3. Galaxy Game–specific process/agent rules (not generic, not game design) → new
     project-specific rules file under /Users/tam0013/Documents/git/galaxyGame/docs/,
     NOT named "GUARDRAILS"
  4. Game design / system intent content → the galaxyGame docs/ subdirectories listed
     above, per the section-to-folder table already mapped in GUARDRAILS_REF_PACKAGE.md

STILL OPEN (your job to resolve today):
- em_power_shield_tiers.md target path: is it
  /Users/tam0013/Documents/git/galaxyGame/docs/systems/em_power_shield_tiers.md,
  /Users/tam0013/Documents/git/galaxyGame/docs/architecture/stations/em_power_shield_tiers.md,
  or /Users/tam0013/Documents/git/galaxyGame/docs/architecture/structures/em_power_shield_tiers.md?
  Read the actual GUARDRAILS.md section on EM Power Transition & Shield Tech (~35 lines)
  and determine which target fits based on content, not just naming convention. Verify
  which of these three directories currently exist with `ls` before deciding — do not
  assume any of them exist. State your reasoning with real command output.

YOUR TASK TODAY:
1. Read the prior research files above. Confirm you understand the four-way sort and
   the one open item.
2. Resolve the em_power_shield_tiers.md path question with real evidence (read the
   actual section, check what's already in each candidate directory using the exact
   paths above).
3. Check /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md's actual current
   line count and content (do not assume the 425-line figure from prior research is
   still accurate — re-verify with a real command) to confirm what's a genuine gap
   (category 2) vs. what's already covered (category 1).
4. Produce ONE consolidated task file — supersedes ALL prior GUARDRAILS task files
   (locate their exact paths via the `find` command above; do not guess) — using the
   TASK_TEMPLATE.md structure at the exact path above, with the resolved
   em_power_shield path baked in and the four-way sort as explicit acceptance criteria.
5. Note in the task file which prior files it supersedes, with their exact paths, so
   those can be archived correctly.

OUTPUT:
- Save consolidated task file to the correct phase folder under
  /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ — confirm
  the exact phase subfolder against PHASE_STRUCTURE.md before saving; do not guess phase5.
- Save your reasoning/evidence for the em_power_shield path decision and the
  agent-tasks/rules/GUARDRAILS.md re-verification to:
  /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/2026-07-02-guardrails-consolidation-AUDIT.md

DO NOT:
- Trim or edit agent-tasks/rules/GUARDRAILS.md or galaxyGame/docs/GUARDRAILS.md yet —
  this task produces the plan, not the execution
- Re-investigate the canonical-file question (already resolved above)
- Create new task files beyond the one consolidated file — old files get superseded,
  not duplicated further
- Guess any path not explicitly given above — if a path isn't listed and you're unsure,
  verify it with `find`/`ls` first, or stop and ask

START:
1. Confirm you've read the prior research and understand what's settled vs. open
2. Tell me you're ready, then begin
```

---

## After Ryzen Reports Back

1. Open the summary file — check the em_power_shield path reasoning is real (cites the
   actual GUARDRAILS.md section content, not just a guess)
2. Confirm the re-verification of agent-tasks/rules/GUARDRAILS.md's current line
   count/content — prior research's "425 lines" figure is from yesterday and may have
   drifted further
3. Review the single consolidated task file — this is what actually gets executed next,
   so it's worth a careful read before approving
4. Once approved, archive the superseded files (`TASK_GUARDRAILS_SPLIT.md`,
   `TASK_WEDNESDAY_SURGICAL_AUDIT.md`, the five 2026-07-01 files) to `tasks/reference/`
   with a note pointing to the new consolidated file
5. Consolidated task then goes to executor (Qwen, same or different session) for actual
   implementation — separate dispatch, not this one
