# Reviewer Access and Package Guide

**Purpose:** Help the coordinator select review instructions based on what the reviewer session can actually access. This is an operational companion to the root `REVIEW_AGENT_GUIDE.md` and the dispatch prompt in this folder.

## Core rule

Choose the access profile from the capabilities available in the specific session—not the model, provider, subscription label, or what another session could do. If access is uncertain, test the named repository/path first and downgrade to the narrower profile if it fails.

Access to repository files and ability to execute commands are separate capabilities. A reviewer may be able to read GitHub without a terminal, or run commands in a local checkout without being able to inspect GitHub. Record both independently when relevant.

## Access profiles

| Actual session access | How the reviewer works | Evidence boundary |
|---|---|---|
| **Repository read** | Read the named repository paths at the specified branch/commit. Use live repository content directly; do not ask the coordinator to upload files already accessible. | State the exact repository and revision inspected. Do not claim access to local-only files, uncommitted changes, containers, or command output. |
| **Repository read/write** | Work exactly as repository-read, but treat the review as strictly read-only. Do not create, edit, move, commit, push, or open a PR. Use the write-lock block in the prompt. | Same as repository-read. Write permission is not review authorization. |
| **Local checkout** | Inspect the named local checkout. Record its absolute or otherwise unambiguous location, branch, commit, and working-tree state. Distinguish committed files from uncommitted changes. | Do not imply that the checkout matches origin or another machine unless checked. |
| **Supplied files/package only** | Review the files attached or pasted by the coordinator. A ZIP may be used as an optional transport container; use the supplied-package-only block. | Conclusions are a review of supplied evidence. Do not claim live path, branch, commit, dependency, or application-code verification unless the relevant evidence is actually included and identified. |

The task creator remains ineligible to review the task, regardless of access profile. Reviewers remain read-only for the review assignment.

## Required evidence for draft-task reviews

The draft-task review process retains its existing three-artifact minimum:

1. The complete draft task under review.
2. The root `TASK_TEMPLATE.md`.
3. The root `REVIEW_AGENT_GUIDE.md`.

All three must come from the same `agent-tasks` commit. Repository-capable reviewers may read them directly. Reviewers without repository access need the three files supplied. The prompt is pasted into the chat; it is not one of the three artifacts.

Do not add `REVIEW_AGENT_WORKFLOW.md`, the report template, project READMEs, status files, handoffs, or code excerpts to every package by default. Add a supporting item only when it is needed to assess a material claim or dependency and the reviewer cannot retrieve it directly. Keep each extra item relevant and identify its source and revision.

This three-file rule is specific to draft-task reviews. For implementation-completion reviews, follow the assigned implementation-review procedure and provide the evidence that procedure requires; do not assume the draft package is sufficient.

## Optional ZIP packaging

A ZIP is an optional delivery method for a reviewer that cannot read the repository in the current session, or when a single package is more practical than several individual uploads. It does not change the review process, grant repository access, or raise the evidence level.

A lean ZIP should contain:

- The three required draft-review artifacts from the same `agent-tasks` commit.
- A short `PACKAGE-README.md` or manifest recording:
  - Task name and repository path.
  - Source repository and exact commit SHA for the three required artifacts.
  - A list of every included file.
  - For each optional supporting file: its source repository/path, commit or dated revision when available, and why it is included.
  - Any known omissions or evidence limitations.
- Only the optional supporting documents or excerpts necessary for the assigned review.

Do not routinely include the full project wiki, a repository snapshot, broad handoff history, or large code samples. If a particular path, method, or code dependency must be assessed, include only the smallest relevant evidence when direct repository access is unavailable. If that evidence is not supplied, the reviewer must identify it as unverified and name the exact check needed from a repository-capable session.

The dispatch prompt is pasted separately into the reviewer chat. When using a ZIP, select the supplied-package-only access block even if the reviewer can browse the web generally. General web browsing is not a substitute for the source repository or the package commit.

## How to record the evidence boundary

Every review should make clear:

- Which access profile actually applied.
- Which repository, branch, commit, local checkout, or package manifest was inspected.
- Which claims were directly verified, supplied by the coordinator, or taken from dated status/handoff material.
- Which relevant files, paths, commands, or runtime facts could not be checked.

If the session has less access than expected, stop relying on the stronger profile and continue only within the narrower evidence boundary. Do not fill gaps by guessing.

## Related documents

- `../../REVIEW_AGENT_GUIDE.md` — governing review responsibilities, draft-task review procedure, evidence standards, and authority.
- `REVIEW_AGENT_WORKFLOW.md` — general review-session procedure.
- `DRAFT_REVIEW_PROMPT_TEMPLATE.md` — standard draft-review prompt and access blocks.
- `REVIEW_REPORT_TEMPLATE.md` — concise implementation-review report template.
