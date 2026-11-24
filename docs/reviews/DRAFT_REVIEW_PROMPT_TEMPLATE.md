# Draft Review Prompt Template

**Applies To**: Any human or planning agent coordinating an independent draft-task review
**Purpose**: One standard prompt for every reviewer. Access instructions are selected by the actual session capabilities, not by model/provider name.
**Authority**: Procedure and report format live in the root `REVIEW_AGENT_GUIDE.md` § "Reviewing Draft Tasks for Scope Approval". Access-profile selection and optional ZIP packaging are explained in `REVIEWER_ACCESS_GUIDE.md` in this folder. This file supplies the dispatch prompt; it does not replace either guide.

---

## Rules (apply to every agent)

1. **One draft task per review.** Two drafts means two reviews, run in dependency order.
2. **Same three-file evidence set for every reviewer**, read from GitHub when the session has repository access or attached/pasted when it does not:
   1. the draft task
   2. `TASK_TEMPLATE.md`
   3. `REVIEW_AGENT_GUIDE.md`

   All three must come from the same commit. Repository-capable reviewers can read them at the GitHub paths; do not upload files they can already access. Do not add the general review workflow or report template to a draft-review package by default; they are not part of the required three-file draft-review evidence set.
3. **The prompt is pasted into chat, never uploaded.** It does not count toward the three-file limit.
4. **Approval target is `backlog/`**, not `active/`. Moving to `active/` happens at dispatch, Step 0 of the Agent Dispatch Interface.
5. **Reviewers are read-only**, whatever their session permits.
6. **Pick the ACCESS PROFILE by what the session can do**, not by model name. If the agent turns out to have less access than expected, it says so and uses Block B.

## Access profiles (confirm per session; never infer from model name)

| Profile | Actual session capability | Instructions |
|---|---|---|
| Repository-read | Can open specified GitHub paths and inspect named branch/commit | Block A |
| Repository-read/write | Can read and write GitHub; review remains strictly read-only | Block A + write lock |
| Supplied-package-only | Cannot access repository in this session | Block B |
| Local workspace | Can inspect a local checkout | Adapt Block A; record exact checkout, branch, commit, and working-tree state |

These are access profiles, not model categories. Ollama models, web agents, and GitHub Copilot models may fit different profiles in different sessions. Copilot offers multiple models; premium availability is controlled by Tracy. Never assume a particular model is available.

Tier A and B use the same package. The only difference is how much the reviewer may claim to have verified.

---

## Independence and evidence requirements

- The task creator instance is excluded from the review roster. It may revise, but may not review or count toward coverage.
- Use separate agent instances for independent reviews; prefer distinct models where practical, without requiring a fixed lineup.
- Supply GitHub repository paths, branch names, and commit hashes for repository-capable reviewers. Uploads are unnecessary when they can read those files directly.
- For local VS Code/Copilot or Ollama sessions, state whether the review uses a local checkout or GitHub. Record exact checkout/branch/commit and whether uncommitted changes are visible.
- Copilot model availability, including premium access, is decided by Tracy; do not assume or auto-select premium models.

## Pre-send checklist (human or planning agent)

- [ ] Exactly one draft task is under review.
- [ ] The draft task, `TASK_TEMPLATE.md`, and `REVIEW_AGENT_GUIDE.md` are available from the same `[PACKAGE_COMMIT]` (directly in GitHub/local checkout or in the supplied package).
- [ ] Every `[PLACEHOLDER]` below is filled. Check: `grep -nE '\[[A-Z_]+\]' <prompt file>` returns nothing.
- [ ] Exactly one access profile kept, matching the actual session.
- [ ] Established decisions are separated from open questions.

---

## Optional supplied ZIP package

Use a ZIP only when the reviewer cannot access the repository in the actual session or when a single packaged upload is materially easier. ZIP is a transport option, not a separate review process. It must contain the same three required artifacts from one agent-tasks commit, plus a short manifest identifying the task, source repository, commit SHA, included files, and any optional supporting files with their own source/revision. Add supporting context only when it materially affects the assigned review; do not routinely include the full wiki, repository, or large code samples. The prompt is still pasted into chat and the reviewer uses Block B. A ZIP does not grant repository access or permit claims of live verification.

## THE PROMPT (copy from here)

```text
You are an INDEPENDENT REVIEW agent. Confirm you are a different agent instance from the task creator; the creator is not eligible to review or count toward review coverage. Task-specification review only. Do not execute the task, edit or create any file, commit, push, dispatch work, or write a replacement task. Return the review as text in this chat.

ASSIGNMENT
- Draft under review: [TASK_FILE] (path in repo: [TASK_PATH])
- Agent-tasks repository: trmccormick/agent-tasks
- Project: [PROJECT]; main application repo and branch: [APP_REPO_AND_BRANCH]
- Package commit (all three attached files come from it): [PACKAGE_COMMIT]
- Decision requested: is this draft ready for Tracy's consideration for promotion from drafts/ to backlog/?

PACKAGE (available directly at the named repository paths/commit, in a local checkout, or attached as files/a ZIP; do not review from memory or from an unverified link)
1. [TASK_FILE]
2. TASK_TEMPLATE.md
3. REVIEW_AGENT_GUIDE.md
If any of the three is missing, ask for it and stop. Follow REVIEW_AGENT_GUIDE.md section "Reviewing Draft Tasks for Scope Approval" exactly: Phase A (template conformance), Phase B (content), and the required ten-section report format. Disposition is GO, REVISE or STOP only. State the package commit in the report.

5. Template compliance (mandatory): Read TASK_TEMPLATE.md and compare every required section against the draft. Verify: all YAML fields (resume, system_domain, mvp_alignment, local_worker_safe), DRAFT STATUS NOTE, DRAFT REVIEW CHECKLIST, full Agent Dispatch Interface block (not abbreviated), Task Readiness Checklist. Flag any missing sections as REVISE requirements. Do not GO if the template is non-compliant.
6. Review package (mandatory): The task file, TASK_TEMPLATE.md and REVIEW_AGENT_GUIDE.md must be available from the named repository paths/commit, the identified local checkout, or the supplied package. All three must come from the same agent-tasks commit. If any is unavailable, ask for it.

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

ACCESS PROFILE
[PASTE THE PROFILE THAT MATCHES THIS SESSION'S ACTUAL ACCESS. Do not select by model name.]

EVIDENCE RULES
Label every claim: Direct verification (you checked it this session), Supplied evidence (given to you), or Dated claim (from status.md or a handoff, with its date). Never describe supplied evidence as independently verified. Do not guess paths, method names or line numbers; list anything you could not check under Evidence Boundary.

RETURN
The complete report in this chat, in the REVIEW_AGENT_GUIDE.md format. Do not say you saved a file.
```

---

## ACCESS BLOCKS (paste exactly one into the prompt)

### Block A — repository-read session

```text
ACCESS: You may read GitHub repository trmccormick/agent-tasks (read-only) at the specified paths and package commit.
- Cross-check, at commit [PACKAGE_COMMIT] or later (say which): the project README.md, SESSION_GUIDANCE.md, status.md, NEEDS_REVIEW.md, active/backlog tasks for overlap, and paths the draft references.
- If any of this cannot be opened, say so under Evidence Boundary. Do not guess its contents.
- If the repo's current file differs from the attached package file, report the difference. Review the attached copy.
- Information that exists only on the project machine (command output, uncommitted files, container state) must be requested from the human as raw output. Do not guess it.
```

### Block A + write lock — repository-read/write session

Use Block A, then append:

```text
WRITE LOCK: Your session may allow writing to the repository. You are not authorized to. Do not create, edit, commit, push, open pull requests, or move any file, including the draft under review and any review artifact. Returning the report as chat text is the only permitted output. If you believe a change is needed, state it as a recommended correction in the report.
```

### Block B — supplied-package-only session

```text
ACCESS: You have no repository access in this session. Your evidence is limited to the three required review artifacts, this prompt, and any optional supporting files explicitly listed in the supplied package manifest.
- Describe conclusions as "review of supplied evidence." Do not claim to have checked file paths, status.md, other tasks, or the application repo.
- Mark every path, method name and cross-task dependency you cannot confirm as [FILL IN: needs repo check].
- If a finding depends on repo state you cannot see, list it under Evidence Boundary and under Human decisions, with the exact file or command a repo-capable session should check.
- Do not browse or search the web in place of the repo. Web results are not a substitute for the package commit.
```

---

## Re-review variant (after REVISE)

Per the guide, the planning agent fixes every listed item in place in `drafts/`, and the reviewer re-checks only the listed items. Use the same package method (refresh the revised task, same template and guide; update the manifest/commit if using a ZIP) and replace ASSIGNMENT and the Phase instructions with:

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
