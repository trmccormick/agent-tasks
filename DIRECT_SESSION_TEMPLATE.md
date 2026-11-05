# Direct Session Template
**Purpose:** Standard bootstrap and working agreement for focused, iterative work sent directly to a local coding agent. This is separate from the formal task workflow in `TASK_TEMPLATE.md`.

Use this template when the human is collaborating directly with the coding agent and reviewing progress as it happens. Do not use it to bypass formal task requirements for larger, autonomous, cross-cutting, or otherwise task-sized work.

---

## Dispatch prompt

`<agent-tasks>` and `<project-repo>` are placeholders. Resolve them using the local workspace and the project's own guidance; never copy another machine's absolute paths.

```markdown
# [Project] — [One clear objective]

## 1. Session startup

If this is a **fresh session**, read the following in the exact order specified by the project's session guidance:

1. `<agent-tasks>/rules/GUARDRAILS.md`
2. `<agent-tasks>/projects/<project>/README.md`
3. `<agent-tasks>/projects/<project>/SESSION_GUIDANCE.md`

Then read the most recent relevant handoff for this project and agent. If the human names a specific handoff, use that one. Follow any additional or different startup sequence explicitly established by the project.

If this is a **continuation of the existing session**, STEP 0 is already complete. Do not repeat startup reading. Continue from the current approved objective and the work already reviewed.

Follow repository, branch, environment, and tool instructions from the project guidance.

## 2. Objective

[State one specific outcome. Keep the session focused on this objective.]

## 3. Working boundaries

- Inspect the current repository state and relevant implementation before changing files.
- Preserve existing work. Do not reset, discard, overwrite, or assume uncommitted changes are disposable.
- Make targeted changes; do not broaden scope or redesign unrelated areas.
- Ask the human before making a material design or architectural choice that is not already established.
- Work iteratively and pause for human review at the agreed checkpoint before moving to a distinct objective.
- Follow all applicable guardrails, including any required synthesis/review gate. This direct-session format does not waive them.

## 4. Review and verification

[State what the human should be shown and the specific checks needed. Use screenshots/live-page review when the work is visual.]

Report what changed, what was actually tested, exact results, and anything not verified. Do not claim a command, test, browser check, or commit occurred unless it did.

## 5. Scope and closeout

Do not create a formal task file, separate report, summary, or handoff for this direct session unless the human asks or the work genuinely grows beyond direct collaboration. If it grows, stop and recommend moving to the formal task workflow.

Do not commit or push without explicit human approval. Follow project-specific closeout instructions when requested.
```

---

## How to choose the workflow

| Situation | Use |
|---|---|
| Small, focused, iterative work with the human reviewing progress | **Direct session** — this template |
| Larger, independently executable work with defined scope and acceptance criteria | **Formal task** — `TASK_TEMPLATE.md`, review and lifecycle rules apply |
| Dispatching an already approved task file to an executor | `SIMPLE_HANDOFF_TEMPLATE.md` or the applicable handoff template |

## Rules for agents preparing a direct-session prompt

1. Start from this template; do not invent a new bootstrap format for each project.
2. Use project-specific guidance for the actual startup reading order and handoff location. Do not assume every project has identical files or paths.
3. Include only the current objective and the constraints needed to perform it safely. Do not paste the entire project guide into the prompt.
4. Preserve the fresh-session versus continuation distinction. A continuation must not repeat STEP 0 unless context was lost or the human requests it.
5. Keep direct work conversational and incremental. Do not convert every small adjustment into a formal task or generate extra documentation by default.
6. The human controls scope changes, review checkpoints, commits, and pushes.
