# Prompts — Agent Dispatch & Review Templates

**Purpose**: Standard, reusable prompt templates for consistent agent communication.

Agents and planning coordinators fill in `[BRACKETS]` while preserving structure and style. This keeps prompts consistent, reduces reinvention, and helps agents understand expectations quickly.

---

## Available Templates

### Implementation & Dispatch

**`QWEN_IMPLEMENTATION_DISPATCH.md`** — For dispatching implementation or revision tasks to Qwen.

Use when:
- Qwen needs to make code changes, run tests, or revise files
- Task is well-scoped with clear mandatory and optional items
- GitHub-based work (not local paths)

Fill in:
- Repository URL and branch
- Specific task file(s) to reference
- Numbered mandatory changes (what/why/fix)
- Optional improvements
- Constraints and deliverables

### Review & Compliance

**`COPILOT_TASK_COMPLIANCE_REVIEW.md`** — For initial task compliance review (WVU/Samvera projects).

Use when:
- M365 Copilot is doing the first compliance pass on draft tasks
- Review scope is template conformance, structure, clarity
- Task files are ready in GitHub (commit reference provided)

Fill in:
- Task file(s) being reviewed
- GitHub commit hash for the review package
- Repository path
- Reviewer name and date (Copilot fills in during response)

---

## Workflow

**Standard WVU/Samvera Task Flow**:

1. **Planning agent** drafts task file(s) → commits to `tasks/drafts/`
2. **M365 Copilot** (never the drafting agent) → compliance review using `COPILOT_TASK_COMPLIANCE_REVIEW` template
3. **Qwen** (if changes needed) → revises files using `QWEN_IMPLEMENTATION_DISPATCH` template
4. **M365 Copilot** (re-review) or **ChatGPT** (coordination) → final validation if needed
5. **Human** → approves move to `tasks/active/` and dispatch

---

## Adding New Templates

When a new prompt pattern emerges:

1. Create a file: `[AGENT]_[TASK_TYPE].md`
2. Document: purpose, when to use, what to fill in, structure
3. Commit to this folder
4. Update this README with the new template entry
5. Cross-link from relevant workflow guides (e.g., `HUMAN_WORKFLOW_GUIDE.md`)

---

## Principles

- **Minimal required fields**: Only `[BRACKETS]` that change task-to-task
- **Standard sections**: Preserve order and naming across all templates
- **Evidence-based**: Include references to repo paths, commit hashes, specific sections
- **No local paths**: All paths GitHub-compatible (web agents can't use `/Users/...`)
- **Clear disposition options**: Go/Revise/Stop (or equivalent); no ambiguous middle ground

