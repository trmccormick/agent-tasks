# Handoff — catalog-image containment draft review
Date: 2026-10-18

## Roles and authority
- ChatGPT is the lead asset/UI reviewer and has final say on reconciling these tasks.
- Claude, Qwen, and Perplexity may provide supporting review.
- This is draft review only. No implementation, dispatch, task moves, commits,
  or pushes are authorized unless the human explicitly approves them.
- Review tasks one at a time. Keep them in backlog/drafts with
  status: backlog, resume: false, readiness checkboxes unchecked.

## Work in scope
Three catalog-image containment child-task drafts:
A. `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-A-CORE-REALPATH.md`
B. `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-B-SYMLINK-CASES.md`
C. `2026-10-17-HIGH-BUG-FIX-CATALOG-IMAGE-CONTAINMENT-C-HTTP-VERIFICATION.md`

The original parent task and `TASK_TEMPLATE.md` have not been supplied in this
review thread. Do not claim their wording or conventions have been verified.
Qwen reportedly made minor updates; review current versions when supplied.

## Review status
- Perplexity reviewed the pasted drafts A, B, and C.
- ChatGPT reviewed A and returned a reconciliation of Perplexity’s A findings.
- ChatGPT was unavailable for roughly two hours as of 2026-10-17 afternoon.
  The user paused, stating ChatGPT is the main asset/UI agent and has final say.
- No edits have been made based on these reviews.

## Task A — findings to resume from
ChatGPT agreed that the draft clearly states the core policy: resolve root and
candidate, enforce a separator-aware strict-descendant boundary, require a
regular file, preserve 404 behavior, and isolate tests. This is not evidence
that an implementation is secure.

Three genuine internal workflow conflicts were identified:
1. Dispatch Interface says save synthesis in summaries and never paste in chat;
   synthesis section says to post it in chat.
2. Synthesis says before any commands, while Step 0 requires lifecycle commands.
3. Dispatch commands use paths rooted at `projects/...`, while the find command
   uses `agent-tasks/projects/...`; intended working directory is unverified.

ChatGPT suggested replacing the synthesis instructions and making `find`
relative to the clone root, but said the parent/template must be checked first.
Important subsequent correction: its proposed phrase “before any further
repository inspection” may block required reading of task/guidance before
synthesis. Reconcile one sequence that permits Step 0, required reading,
saved synthesis, required approval, then implementation. Also resolve the
“POST THIS TO CHAT” line; do not apply overlapping replacements independently.

Optional, non-substantive wording: “section below” → “section above” and
“checklist below” → “readiness checklist above.” Unchecked readiness boxes are
normal for a draft, not a defect.

ChatGPT requested `TASK_TEMPLATE.md` and the parent
`2026-10-16-HIGH-BUG-FIX-CATALOG-IMAGE-PATH-CONTAINMENT.md` for final
reconciliation, but neither was supplied here.

## Task B — Perplexity’s provisional review
B depends on A and covers: in-root symlink to outside file, dangling symlink,
and valid file when configured catalog root is itself a symlink. It correctly
says not to reimplement A; if A’s behavior is unclear, stop. Changes are
conditional on a gap; tests-only completion is sensible if A already handles
the cases. Optional clarification: state explicitly that controller changes
are only if necessary, so acceptance criteria do not imply both files must
change. Keep B draft-only pending ChatGPT review.

## Task C — Perplexity’s provisional review
C is report-only and calls for actual HTTP evidence: encoded traversal behavior,
authentication observation, one valid-image request, and one bypass request.
No code/config/auth changes.
Concrete wording issue: if base URL cannot be established, report that
limitation and leave criteria unmet; don’t require reporting an unknown “actual”
URL. C is marked blocked by A. Earlier suggestion to run C first conflicts with
that dependency; do not alter order without human approval. An HTTP response
alone may not prove encoded input reached the controller unchanged; distinguish
observation from inference.

## Next steps
1. Ask the user for ChatGPT’s latest information/prompt and current Task A/B/C
   text as needed, one file at a time.
2. ChatGPT leads the review and decides which findings warrant changes.
3. If parent/template comparison is needed, request those documents separately.
4. Only after final review and explicit human approval should anyone edit tasks,
   move the parent, complete checklists, or consider dispatch.