# Draft Review Prompt Template

**Applies To**: Any human or planning agent sending a draft task to a web review agent
**Purpose**: One standard prompt for every reviewer. Only the ACCESS BLOCK changes by agent capability.
**Authority**: Procedure and report format live in `REVIEW_AGENT_GUIDE.md` § "Reviewing Draft Tasks for Scope Approval". This file does not repeat them. It only makes sure the prompt sent is correct.

---

## Rules (apply to every agent)

1. **One draft task per review.** Two drafts means two reviews, run in dependency order.
2. **Same three-file package for every agent**, attached or pasted:
   1. the draft task
   2. `TASK_TEMPLATE.md`
   3. `REVIEW_AGENT_GUIDE.md`

   All three must come from the same commit. Do not upload `REVIEW_AGENT_WORKFLOW.md` or `REVIEW_REPORT_TEMPLATE.md`; those are for implementation-completion reviews, not draft reviews.
3. **The prompt is pasted into chat, never uploaded.** It does not count toward the three-file limit.
4. **Approval target is `backlog/`**, not `active/`. Moving to `active/` happens at dispatch, Step 0 of the Agent Dispatch Interface.
5. **Reviewers are read-only**, whatever their session permits.
6. **Pick the ACCESS BLOCK by what the session can do**, not by model name. If the agent turns out to have less access than expected, it says so and uses Block B.

## Access tiers (confirm per session; current as of 2026-11-17)

| Tier | Session capability | Agents | Block |
|---|---|---|---|
| A1 | Can read the public repo (GitHub, raw URLs or a clone) | Claude, Grok | A |
| A2 | Can read AND write the repo | ChatGPT | A + write lock |
| B | Uploads and pasted text only, no repo access | Perplexity, Gemini | B |

Tier A and B use the same package. The only difference is how much the reviewer may claim to have verified.

---

## Pre-send checklist (human or planning agent)

- [ ] Exactly one draft task is attached.
- [ ] `TASK_TEMPLATE.md` and `REVIEW_AGENT_GUIDE.md` attached, all three from commit `[PACKAGE_COMMIT]`.
- [ ] Every `[PLACEHOLDER]` below is filled. Check: `grep -nE '\[[A-Z_]+\]' <prompt file>` returns nothing.
- [ ] Exactly one ACCESS BLOCK kept (A, A+write lock, or B).
- [ ] Established decisions are separated from open questions.

---

## THE PROMPT (copy from here)

```text
You are a REVIEW agent. Task-specification review only. Do not execute the task, edit or create any file, commit, push, dispatch work, or write a replacement task. Return the review as text in this chat.

ASSIGNMENT
- Draft under review: [TASK_FILE] (path in repo: [TASK_PATH])
- Agent-tasks repository: trmccormick/agent-tasks
- Project: [PROJECT]; main application repo and branch: [APP_REPO_AND_BRANCH]
- Package commit (all three attached files come from it): [PACKAGE_COMMIT]
- Decision requested: is this draft ready for human approval to move from drafts/ to backlog/?

PACKAGE (attached or pasted below; do not review from memory or from a link)
1. [TASK_FILE]
2. TASK_TEMPLATE.md
3. REVIEW_AGENT_GUIDE.md
If any of the three is missing, ask for it and stop. Follow REVIEW_AGENT_GUIDE.md section "Reviewing Draft Tasks for Scope Approval" exactly: Phase A (template conformance), Phase B (content), and the required ten-section report format. Disposition is GO, REVISE or STOP only. State the package commit in the report.

5. Template compliance (mandatory): Read TASK_TEMPLATE.md and compare every required section against the draft. Verify: all YAML fields (resume, system_domain, mvp_alignment, local_worker_safe), DRAFT STATUS NOTE, DRAFT REVIEW CHECKLIST, full Agent Dispatch Interface block (not abbreviated), Task Readiness Checklist. Flag any missing sections as REVISE requirements. Do not GO if the template is non-compliant.
6. Review package (mandatory): The task file, TASK_TEMPLATE.md and REVIEW_AGENT_GUIDE.md are attached or pasted, with the commit they came from. If any is missing, ask for it.

CONTEXT
- Objective: [OBJECTIVE: one or two sentences, what the task does and why it exists]
- Established decisions (preserve these; do not reopen without new evidence):
  [ESTABLISHED_DECISIONS]
- Open questions (not decided; the reviewer may raise them as human decisions):
  [OPEN_QUESTIONS or "none"]
- Supplied evidence (logs, diffs, checksums; treat as supplied, not verified):
  [SUPPLIED_EVIDENCE or "none"]
- Special constraints (scope limits, data-preservation rules, environment cautions):
  [CONSTRAINTS]

ACCESS BLOCK
[PASTE ONE BLOCK: A, A + write lock, or B, from below]

EVIDENCE RULES
Label every claim: Direct verification (you checked it this session), Supplied evidence (given to you), or Dated claim (from status.md or a handoff, with its date). Never describe supplied evidence as independently verified. Do not guess paths, method names or line numbers; list anything you could not check under Evidence Boundary.

RETURN
The complete report in this chat, in the REVIEW_AGENT_GUIDE.md format. Do not say you saved a file.
```

---

## ACCESS BLOCKS (paste exactly one into the prompt)

### Block A — repo-read agents (Claude, Grok)

```text
ACCESS: You may read the public repo trmccormick/agent-tasks (read-only).
- Cross-check, at commit [PACKAGE_COMMIT] or later (say which): the project README.md, SESSION_GUIDANCE.md, status.md, NEEDS_REVIEW.md, active/backlog tasks for overlap, and paths the draft references.
- If any of this cannot be opened, say so under Evidence Boundary. Do not guess its contents.
- If the repo's current file differs from the attached package file, report the difference. Review the attached copy.
- Information that exists only on the project machine (command output, uncommitted files, container state) must be requested from the human as raw output. Do not guess it.
```

### Block A + write lock — repo-read/write agents (ChatGPT)

Use Block A, then append:

```text
WRITE LOCK: Your session may allow writing to the repository. You are not authorized to. Do not create, edit, commit, push, open pull requests, or move any file, including the draft under review and any review artifact. Returning the report as chat text is the only permitted output. If you believe a change is needed, state it as a recommended correction in the report.
```

### Block B — upload-only agents (Perplexity, Gemini)

```text
ACCESS: You have no repository access in this session. Your evidence is limited to the three attached files and this prompt.
- Describe conclusions as "review of supplied evidence." Do not claim to have checked file paths, status.md, other tasks, or the application repo.
- Mark every path, method name and cross-task dependency you cannot confirm as [FILL IN: needs repo check].
- If a finding depends on repo state you cannot see, list it under Evidence Boundary and under Human decisions, with the exact file or command a repo-capable session should check.
- Do not browse or search the web in place of the repo. Web results are not a substitute for the package commit.
```

---

## Re-review variant (after REVISE)

Per the guide, the planning agent fixes every listed item in place in `drafts/`, and the reviewer re-checks only the listed items. Use the same package (re-attach the revised task, same template and guide) and replace ASSIGNMENT and the Phase instructions with:

```text
RE-REVIEW: Round [1 or 2]. Previous disposition: REVISE on [DATE], package commit [OLD_COMMIT]. Revised draft is from [NEW_COMMIT].
Re-check ONLY these listed items: [PASTE THE MANDATORY CORRECTIONS LIST]
For each: FIXED / NOT FIXED / NEW PROBLEM, with quoted evidence. Do not reopen accepted items or raise new optional improvements. Disposition: GO if all fixed, else REVISE for the remaining items. A second failed re-check means STOP: Tracy decides.
```

---

## After the review

- The reviewer returns the report in chat. The human or planning agent saves it to `projects/<project>/summaries/YYYY-MM-DD-REVIEW-<topic>.md`.
- Reviewer GO is not approval. Only Tracy moves a task `drafts/` → `backlog/`.
- Never use `git add .` or `git add -A` when committing the saved report.
