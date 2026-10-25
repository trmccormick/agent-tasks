# Review: prompt-construction incident (ChatGPT, 2026-10-24)

**Reviewer**: Claude (free web), independent review role  **Project**: agent-tasks  **Type**: REVIEW
**Repo state read**: `trmccormick/agent-tasks` at `e001c93` (fetched this session; re-check if anyone pushed since)
**Labels**: VERIFIED (I read it in the repo at that commit), REPORTED (stated in the incident report or by Tracy), INFERRED (my conclusion)
**Scope**: read-only. No workflow file, Task A, Task C or `status.md` was edited. Every recommendation below is a proposal for Tracy, not an approved change.

## 1. Incident understanding

ChatGPT built a long multi-section Qwen prompt for a small Task C documentation correction. It left out the basic header, added reading files and lifecycle decisions, and mixed edit, review, commit and push. REPORTED: the incident report, from the conversation. I have not seen the prompt itself, and nothing shows Qwen received it (the report says so too). So F1-F6 rest on the report's description.

## 2. Findings that change the picture

1. **There is no single "basic header" (VERIFIED).** The repo has four versions of the handoff an agent receives, and they disagree:
   - `TASK_TEMPLATE.md` L125-162: 29-line dispatch block, "MANDATORY and NON-NEGOTIABLE … copy EXACTLY" (L127). Tracy's 2026-10-22 decision (REPORTED, `NEEDS_REVIEW.md` draft-gate entry, Status line (c)) makes this the minimal handoff.
   - `rules/GUARDRAILS.md` Rule 13a, L293-347: "9 lines maximum" (L300), reads README, GUARDRAILS and the task file.
   - `README.md` L591-603: a short "Proven Working Format" ending "Implement [TASK_DESCRIPTION]".
   - `SIMPLE_HANDOFF_TEMPLATE.md`: retirement decided 2026-10-22; the file is still present (`NEEDS_REVIEW.md` L112-116 records the conflict).
   An assistant looking for "the header" finds a 9-line rule, a 29-line mandatory block and a short example. That is a documentation contradiction, not only an assistant slip.
2. **Every defined header presumes a saved task (VERIFIED).** The dispatch block's Step 0 is `git mv` from `backlog/` to `active/`; Rule 13a and the README format also name a task file path. For a single-objective prompt with no saved task (this incident's case), nothing is defined. Tracy said simple edits should not become saved tasks (REPORTED), so this is the real gap.
3. **Row 3 of the delivery table is not matched to that case (VERIFIED).** `WEB_AGENT_SESSION_START.md` L93-95: row 2 (small edit to one file) gives exact text to the human; row 3 (repo read, tests, project-machine commands) says "a prompt for an implementation agent" with no header and no size limit. Work that needs a repo check but is one bounded edit sits between the rows.
4. **TASK_TEMPLATE pushes toward volume (VERIFIED).** L46: "Rule: when in doubt, add more detail. An over-specified task wastes nothing." It is written for saved tasks, but nothing says so, which fits hypothesis 2 (overgeneralizing from the template) (INFERRED).

## 3. Evidence table

| # | Observed failure | Existing rule (VERIFIED) | Verdict |
|---|---|---|---|
| F1 | Delivery method not classified | `WEB_AGENT_SESSION_START.md` L89 ("say in one line which you chose"), table L93-95 | Compliance failure, with the row-3 gap in finding 3 |
| F2 | Basic header omitted | None canonical (finding 1) | **Workflow gap** |
| F3 | Extra files, long prompt | Rule 13a L298-302 ("No extra context"); L56 one objective | Compliance failure, made likelier by the 9-line vs 29-line contradiction |
| F4 | Simple work treated as a long task | Nothing says when `TASK_TEMPLATE.md` depth applies (finding 4) | **Partly a gap** |
| F5 | Scope and authority broadened | `WEB_AGENT_SESSION_START.md` L53-56; Rule 26 (human commits and pushes, cited at L108) | Compliance failure |
| F6 | Acknowledged, did not act | Nothing a document can fix | Assistant reliability; I agree with the report that a new document will not solve it |

INFERRED and not checkable: whether ChatGPT was ever given `WEB_AGENT_SESSION_START.md` in that session. If it was not, the rules never reached it, and F1/F3/F5 are retrieval failures, not compliance failures.

## 4. Recommendations R1-R4

- **R1 (delivery classification rule): reject as a new rule.** The table and L89 already classify. Fold the missing case into R2's one edit.
- **R2 (canonical header): accept, modified.** Do not add a document. Make `TASK_TEMPLATE.md`'s dispatch block the one header for saved tasks, as already decided. For a no-task prompt, add a short form inside `WEB_AGENT_SESSION_START.md` row 3. Then replace the two competing versions (Rule 13a, README L591-603) with a pointer. The retirement of `SIMPLE_HANDOFF_TEMPLATE.md` is already on the task list.
- **R3 (template depth is conditional): reject for now.** Leave `TASK_TEMPLATE.md` unchanged. It is already queued for 1c and task 2, and a third edit to the same file risks weakening requirements for real saved tasks. Revisit after those two land.
- **R4 (six-item preflight): modify to one sentence.** A six-item checklist is the ceremony Tracy said she does not want, and one incident does not justify it. Extend L89 ("say in one line which you chose") to also say which header and which files the agent must read.

## 5. Minimal changes if Tracy approves (one concern, one file each)

1. `WEB_AGENT_SESSION_START.md`, after the table (around L96): one sentence naming the header for a no-task prompt and the maximum reading list, plus the L89 extension above. **Blocked on Tracy's answer to question 1 below**, because I cannot invent the header.
2. `rules/GUARDRAILS.md` Rule 13a L300 and `README.md` L591-603: replace with a pointer to the chosen header. Fits the queued "retire `SIMPLE_HANDOFF_TEMPLATE.md`" task.
3. **Separate defect, found while reading:** `WEB_AGENT_SESSION_START.md` L54 ("Leave new tasks in `backlog/` at `status: backlog`") and L60 ("saved back to the same `backlog/` location") contradict the draft gate (Rule 12: new tasks start in `drafts/`). This is the file a review agent reads first, so it can steer a draft into `backlog/`. Small task, same file as change 1, so run it first.

## 6. Leave unchanged

The task lifecycle and Rule 12; the review gate and the human authority boundaries (Rule 26); `SESSION_CLOSEOUT.md`; `REVIEW_AGENT_GUIDE.md`; the delivery table itself (the three rows are sound); `TASK_TEMPLATE.md` apart from 1c and task 2. No new document.

## 7. Validation plan (next three prompts built for a single-objective repo edit)

Pass if each one: states which table row it used; opens with the chosen header; lists only the task or target file plus the files the header requires; has one objective; contains at most one commit or push instruction, and only if Tracy asked. Two clean passes in a row, keep the change. A failure goes into the dated rows in `MODEL_SELECTION_GUIDE.md` (agent, date, what happened, label).

## 8. Uncertainties for Tracy

1. What was "the basic header discussed earlier"? It is not in the repo (REPORTED only). Was it the dispatch block, Rule 13a, or something else?
2. Is "Qwen does a single bounded edit with no saved task" an approved path? If yes, change 1 defines its header; if no, it should go to the human as exact text (row 2).
3. Was `WEB_AGENT_SESSION_START.md` loaded into that ChatGPT session?
4. The report's evidence files are not on `main` (VERIFIED by `git ls-files`: `grok-chatgpt-review-request.md` and Grok's Task C review summary return no matches). I could not check the report's statements about Grok's review. Commit them if they should stay as evidence. The Task C draft itself is in `projects/galaxy_game/tasks/drafts/`.
