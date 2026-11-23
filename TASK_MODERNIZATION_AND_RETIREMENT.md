# Legacy Task Modernization, Decomposition, and Retirement

**Applies to:** All projects using this agent-task workspace  
**Owner:** Human + planning/strategist role  
**Purpose:** Preserve valuable intent while replacing stale, oversized, or poorly specified tasks with a current, reviewable plan.

## Governing principle

A legacy task is evidence of an intended outcome, not a specification that must be preserved. Do not merely polish its existing structure or carry its old scope forward. Recover the core intent, reassess it against current project priorities, authoritative documentation, and verified implementation, then create the smallest useful plan that can be executed reliably.

The desired shape is **one coherent goal, delivered through appropriately sized tasks**. A task may remain single when it is already clear, bounded, and independently verifiable. Decompose when the work combines materially different discoveries, architectural decisions, implementation areas, or verification burdens.

## Modernization workflow

1. **Triage the original.** Read the task, its history and relevant handoffs/reviews. Determine whether its goal is still wanted and relevant. Check current project status, active work, authoritative wiki/design docs, and source code where repository access is available.
2. **Recover and validate intent.** State the enduring goal separately from obsolete assumptions, implementation proposals, file paths, and outdated sequencing. Record conflicts and unresolved questions; do not silently reconcile them.
3. **Update authoritative documentation first.** Where the intended design is still valid but undocumented or stale, update the canonical wiki/docs. Keep detailed design and architectural rationale there; task files should link to it rather than duplicate it.
4. **Choose the right planning route.** Use a capable web-based model for external research, broad synthesis, requirements clarification, option analysis, and architecture/design planning when asking Qwen to discover or resolve these issues would be inefficient or ambiguous. Use repository-aware local research to verify actual paths, interfaces, conventions, and implementation constraints. A web model without repository access must not be treated as source-code verification.
5. **Decompose around deliverables and dependencies.** Create multiple tasks when that makes execution clearer. Each task should have one primary outcome, a bounded scope, known inputs/interfaces where possible, ordered steps, explicit exclusions, measurable acceptance criteria, targeted verification, and a handoff to dependent work. Keep a parent/goal reference or common goal identifier so the tasks remain one coordinated effort. Do not split mechanically or create artificial task overhead.
6. **Create replacement tasks in `tasks/drafts/`.** Every replacement starts with `status: draft` and follows the current task template. Link the related wiki pages and the original task. A draft must not be moved directly to backlog or dispatched.
7. **Supersede the original.** Move the old task to `tasks/superseded/` (preserving its filename/history where practical) and update its status/metadata to `superseded`. Add a concise supersession note: why it was retired, what core intent was retained, links to all successor drafts, and any intent explicitly dropped or deferred. Do not mark it completed; superseded is a distinct lifecycle state.
8. **Review replacements normally.** Submit the new drafts through the standard independent review process. The human considers the review disposition and explicitly approves any move from drafts to backlog. Superseding the old task does not approve its replacements.
9. **Update tracking.** Update the project's status/index and relevant handoff or modernization summary so the relationship between the original goal, superseded task, successor drafts, wiki updates, and review state is discoverable.

## Task sizing and agent fit

### Qwen/local implementation work

Qwen performs best when the task is concrete and bounded. Give it a clear plan rather than asking it to solve an open-ended architecture problem while implementing. Prefer small sequential tasks with explicit prerequisites and handoffs. Identify relevant source paths and interfaces when verified; specify what to inspect when they are not yet known. Avoid combining broad research, design decisions, implementation, documentation, and end-to-end validation in one Qwen assignment.

Do not assume every subtask should be assigned to Qwen. If a task still requires substantial independent research, product/design judgment, or architectural synthesis, route that work to an appropriate web-based model or domain expert first, then have repository-aware work validate the plan before implementation.

### Web-based model work

Use web-based models where their strengths are material: research, comparison, design synthesis, requirement clarification, decomposition, risk analysis, and preparing an implementation plan. Supply verified repository findings and canonical wiki references when available. Clearly label any source-code assumptions they cannot independently verify.

### Review and approval

Reviewers assess the draft task(s) for intent preservation, scope, clarity, dependencies, architecture fit, agent fit, and verifiability. A GO on one draft does not approve sibling drafts. Human approval remains required for lifecycle movement and dispatch under the normal guardrails.

## Superseded task retention and cleanup

The `superseded/` directory is a transition and audit record, not a permanent archive of every historical task.

- Keep a superseded task while it is needed to explain successor work, preserve a material decision/rationale, support an active review, or resolve an outstanding dependency.
- At each scheduled backlog/maintenance review, identify superseded tasks whose successor tasks have been reviewed and whose rationale is already preserved in canonical documentation or a concise modernization record.
- Once no active workflow or historical need depends on the old task, remove it from the working task repository. Preserve only the useful information: core intent, reason for supersession, successor links, and decisions that are not already documented elsewhere.
- Do not retain duplicate technical specifications merely for completeness. Canonical wiki pages and concise decision/history records are the long-term sources; obsolete task bodies are disposable.
- Do not delete a task that is still referenced by an active task, unresolved review, open dependency, or project decision without first repairing those references.
- Cleanup is a deliberate maintenance action by the human/planning owner, not an executor's incidental cleanup. Record what was removed in the relevant maintenance summary or status history.

No fixed age threshold is imposed here. Age alone is not sufficient: use relevance and dependency status. The human may establish project-specific retention windows or cleanup cadence as the backlog grows.

## Required links and traceability

A modernization record should make it possible to answer:

- What was the original goal?
- What was retained, changed, deferred, or dropped?
- Which wiki pages now hold the authoritative design?
- Which draft tasks replace the original, and in what dependency order?
- What review/approval state does each replacement have?
- When can the superseded task be safely removed?

## Stop conditions

Stop and ask for direction rather than creating replacement tasks when the original goal is no longer wanted, conflicts with a locked decision, depends on unresolved product direction, duplicates active work, or cannot be separated from a larger decision that has not been made. Record the disposition as obsolete, deferred, or awaiting decision rather than manufacturing work to preserve the old task.
