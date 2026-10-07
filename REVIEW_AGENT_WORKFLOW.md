# Review Agent Workflow

**Applies To**: Any agent performing a review session  
**Purpose**: Concise review procedure — assignment → evidence → targeted review → disposition

---

## Procedure

### 1. Confirm the assignment

Confirm what is being reviewed and the decision requested. Do not broaden scope.

### 2. Read relevant evidence

Use actual session capabilities (repository access or supplied evidence). Start with the latest relevant handoff; consult older material only where ongoing work needs context. Do not assume written status is current — verify claims against live evidence when possible.

### 3. Review for consequential issues

Check for:
- Scope adherence and architectural conflicts
- Verification adequacy (test results, manual checks, commit evidence)
- Risks that could affect downstream work

Request only specific missing evidence that could change the disposition. Do not demand universal synthesis or additional reviewers without concrete need.

### 4. Save one concise review artifact

Save to the task/project destination, or `summaries/` by default. Include:
- Assignment and evidence basis
- Findings
- Disposition: **proceed**, **proceed with a specific caution**, or **stop for a named decision**
- Any targeted correction or decision needed

For sessions without filesystem access, provide the artifact text for an authorized local session to save; do not claim it was saved.

### 5. Report briefly in chat

State the disposition and artifact location. Do not paste the full review.

---

## Do Not

- Reopen accepted decisions without material new evidence
- Automatically rewrite tasks, dispatch work, or add reviewers
- Override specifically authorized checkpoints on existing assignments
- Assume all reviewers lack filesystem access — capabilities depend on actual session access

---

## Shared Rules Reference

- **Responsibilities and evidence standards**: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
- **Closeout procedure**: `/Users/tam0013/Documents/git/agent-tasks/SESSION_CLOSEOUT.md`

Do not repeat shared rules here.
