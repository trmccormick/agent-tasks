# Task File as Execution Contract — Workflow Proposal

**Created**: 2026-09-17  
**Author**: Planning Agent (Qwen)  
**Review Target**: Haiku agent, separate session  
**Status**: PROPOSAL — not adopted until reviewed and approved  

---

## Purpose

Establish a minimal, backward-compatible standard across all managed projects in the `agent-tasks` repository:

> **The selected task file, including its embedded handoff statement, is the authoritative execution contract for an implementation agent. Dispatch prompts must be minimal pointers to that file, plus only explicit current-session modifiers.**

This eliminates redundant dispatch context, prevents silent override of task-file instructions, and makes the task file the single source of truth.

---

## 1. Current-State Map

### Canonical Governance/Template Paths

| Document | Path | Purpose |
|---|---|---|
| TASK_TEMPLATE.md | `/agent-tasks/TASK_TEMPLATE.md` | Reusable task template with YAML frontmatter, readiness checklist, Agent Dispatch Interface |
| SIMPLE_HANDOFF_TEMPLATE.md | `/agent-tasks/SIMPLE_HANDOFF_TEMPLATE.md` | Minimal handoff for straightforward assignments |
| HANDOFF_TEMPLATE.md (advanced) | `/agent-tasks/archive/HANDOFF_TEMPLATE.md` | Complex multi-step coordination handoffs |
| GUARDRAILS.md | `/agent-tasks/rules/GUARDRAILS.md` | Operational guardrails (Docker, RSpec, database, paths, etc.) |
| README.md | `/agent-tasks/README.md` | Repository overview, file organization, symlink conventions |
| PLANNING_AGENT_SESSION_START.md | `/agent-tasks/PLANNING_AGENT_SESSION_START.md` | Planning agent session protocol |
| REVIEW_AGENT_GUIDE.md | `/agent-tasks/REVIEW_AGENT_GUIDE.md` | Review agent workflow |

### Project-Specific Variants

Each managed project has:
- `projects/[PROJECT]/README.md` — domain context guide (auto-generated from project knowledge)
- `projects/[PROJECT]/status.md` — living status document (in the galaxyGame repo via symlink)
- `projects/[PROJECT]/tasks/` — task files organized by lifecycle stage (backlog/current, active, completed/YYYY/MM)
- `projects/[PROJECT]/summaries/` — synthesis reports and session data
- `projects/[PROJECT]/NEEDS_REVIEW.md` — escalation entries needing second opinion

**Managed projects** (12 total): galaxy_game, samvera_hyku, samvera_hyrax, wvulibraries_acda_portal, wvulibraries_authentication, wvulibraries_databases, wvulibraries_knapsack, wvulibraries_library_directory, agent-tasks, ccdt, eve_dashboard

### Representative Task File Patterns

#### Pattern A: Well-Formed (Good)
**Example**: `2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md`
- YAML frontmatter with status, priority, type, system_domain, mvp_alignment
- Task Readiness Checklist (all boxes checked when ready)
- Complete Agent Dispatch Interface with exact file paths, Step 0 instructions, lifecycle guidance
- Implementation Steps with concrete file:line targets
- Acceptance Criteria that are measurable
- Stop Conditions explicitly listed
- Handoff Summary at end

#### Pattern B: Incomplete (Needs Work)
**Example**: `2026-08-30-MEDIUM-FEATURE-OPERATIONAL-DATA-MODE-MODIFIERS-SCHEMA.md`
- Has YAML frontmatter and Agent Dispatch Interface
- BUT has unchecked readiness checklist items ("All file paths verified to exist — NOT DONE")
- Has `[FILL IN]` placeholders in Agent Dispatch Interface (project/SUBFOLDER path segments)
- Would fail dispatch-readiness check

#### Pattern C: Duplicative Handoff Context
**Example**: Various handoff files in `projects/galaxy_game/handoffs/` and `projects/galaxy_game/templates/`
- Multiple template variants exist (TEMPLATE_QWEN_HANDOFF, TEMPLATE_GEMINI_SYNTHESIS_HANDOFF, etc.)
- Some handoff files repeat implementation steps that should live only in task files
- The SIMPLE_HANDOFF_TEMPLATE explicitly warns against adding extra context beyond the task file

#### Pattern D: Historical Tasks Without Templates
**Example**: Pre-template task files in completed/2026-06/ and earlier
- Missing YAML frontmatter entirely
- No Agent Dispatch Interface section
- No readiness checklist
- Cannot be evaluated for dispatch-readiness against current standards

### Key Finding: The Template Already Exists

The TASK_TEMPLATE.md already mandates an "Agent Dispatch Interface" section immediately after YAML frontmatter. It states:

> "The 'Agent Dispatch Interface' section (immediately after YAML) is NOT optional scaffolding. It is the CONTRACT between human and agent."

And the SIMPLE_HANDOFF_TEMPLATE explicitly instructs:

> "Do NOT add extra context... Context belongs in task file, not handoff"
> "Do NOT add implementation hints... Implementation details belong in task file synthesis template"

**The standard already exists in templates. What's missing is an explicit governance rule that makes the task file authoritative and dispatch prompts minimal.**

---

## 2. Gap Analysis

### What Already Exists (Matches Proposed Standard)

| Proposed Rule | Current State |
|---|---|
| Task file as execution contract | TASK_TEMPLATE.md mandates Agent Dispatch Interface as "the CONTRACT between human and agent" |
| Minimal dispatch prompts | SIMPLE_HANDOFF_TEMPLATE explicitly forbids adding extra context beyond task file |
| Stop conditions required | TASK_TEMPLATE includes stop conditions in readiness checklist |
| Pre-flight verification | GUARDRAILS Rule 10 (host vs container paths), Rule 3a (pre-execution check) |
| Portfolio modifiers concept | SIMPLE_HANDOFF_TEMPLATE anti-patterns section implicitly supports this by forbidding extra context |

### Gaps (Systemic vs Project-Specific)

#### Systemic Gaps (Affect All Projects)

1. **No explicit governance rule stating the task file is authoritative** — The template says the Dispatch Interface is "the CONTRACT" but there's no standing rule that dispatch prompts MUST NOT duplicate, override, or supplement task-file instructions beyond a minimal pointer + portfolio modifier.

2. **No standardized minimal dispatch-prompt template** — The SIMPLE_HANDOFF_TEMPLATE exists but is not formally designated as THE dispatch standard. Multiple handoff templates exist (simple, advanced, project-specific variants), creating ambiguity about which to use.

3. **Historical tasks are not evaluated for compliance** — Tasks created before the current template have no Agent Dispatch Interface, no readiness checklist, and cannot be dispatched under the proposed standard without retroactive updates.

4. **No explicit preflight verification rule** — GUARDRAILS has path-convention rules but no rule requiring agents to verify task-file claims (file existence, status match, prerequisite truth) before acting.

5. **No formal exception mechanism** — When extra dispatch context IS needed (complex architecture, cross-project coordination), there's no documented process for justifying and recording the exception.

#### Project-Specific Gaps

- **galaxy_game**: Has the most template variants (7+ project-specific templates in `projects/galaxy_game/templates/`). Some duplicate functionality from canonical templates.
- **wvulibraries_***: Less task file history; fewer compliance issues but also less evidence of template adoption.
- **samvera_hyku/hyrax**: Moderate task file history; some pre-template tasks exist.

### Risks of Adopting the Rule Unchanged

1. **Historical task breakage** — Tasks without Agent Dispatch Interface sections cannot be dispatched under this standard. Must have a migration policy.
2. **Template proliferation confusion** — Multiple handoff templates already exist. Formalizing one as THE standard requires retiring or deprecating others.
3. **Over-constraint on complex tasks** — Some multi-step coordination tasks genuinely need extra context beyond the task file. The rule must allow documented exceptions.

---

## 3. Minimal Proposed Change Set

### A. Governance Rule Addition to GUARDRAILS.md

Add a new section (Rule 20) to `/agent-tasks/rules/GUARDRAILS.md`:

```markdown
### Rule 20 — Task File as Authoritative Execution Contract

**The selected task file, including its embedded Agent Dispatch Interface, is the authoritative execution contract for an implementation agent.**

#### What this means:

1. **Dispatch prompts must be minimal.** A dispatch prompt contains ONLY:
   - Exact task-file path
   - Instruction to read the task file and its embedded handoff in full
   - Statement that the task file is authoritative
   - One optional, concise portfolio modifier (see below)
   - Stop/report instruction for ambiguity, scope conflict, repository-state drift, active-work conflict, or unapproved decision

2. **Dispatch prompts must NOT:**
   - Duplicate implementation steps, validation instructions, or lifecycle instructions from the task file
   - Silently override task-file instructions
   - Add architectural context that belongs in the task file's Prerequisites section
   - Add implementation hints that belong in the task file's Implementation Steps

3. **Portfolio modifiers are exceptions.** A modifier may state ONLY current coordination facts not reasonably stored in a static task file:
   - Another agent or test/container environment is active
   - A related architecture stream is human-gated
   - A workstream is paused/deferred
   - A specific integration conflict must be avoided
   - A current review gate is pending

4. **Preflight verification.** Before acting, the assigned agent verifies:
   - Task location and YAML/status match the assignment
   - Relevant referenced files and paths exist
   - No active task/agent conflicts with the planned work
   - Current repository state does not already satisfy or invalidate the task
   - Any stated prerequisite is still true

5. **Exception process.** If extra dispatch context is needed beyond a portfolio modifier:
   - Document WHY in the dispatch prompt (one sentence)
   - Reference the specific gap in the task file that necessitates it
   - The exception is session-scoped; do not bake it into future task files or templates

#### Historical tasks:

Tasks created before this rule does not apply to them. They may be dispatched as-is. When a historical task is selected for new work, update it to template compliance during the move-to-active step (not before).
```

### B. Minimal Reusable Dispatch-Prompt Template

Designate the existing SIMPLE_HANDOFF_TEMPLATE as THE standard dispatch prompt. Update its header:

```markdown
# Simple Handoff Template — STANDARD DISPATCH PROMPT
**Use this for ALL straightforward task assignments.**
**This is the canonical dispatch format. Do not create alternative formats.**
```

### C. Minimal Task-File Embedded-Handoff Template

The existing Agent Dispatch Interface in TASK_TEMPLATE.md already serves this purpose. No changes needed beyond ensuring all future task files use it.

### D. Migration Policy for Historical Tasks

**Principle**: Do not mass-edit historical tasks. Update only when:
1. A historical task is selected for new work (update during move-to-active)
2. A historical task is found to be unsafe or misleading (update proactively)
3. A historical task is being reviewed as part of a backlog sweep

### E. Required Tests/Checks

No automated template validation exists in the repository. Proposed lightweight checks:
- **Manual**: Task Readiness Checklist in TASK_TEMPLATE.md serves as the gate
- **Future**: Consider a simple script that validates YAML frontmatter presence and Agent Dispatch Interface section existence across task files

---

## 4. Rollout Plan

### Phase 1: Repository-Level Templates/Governance (Immediate)
- Add Rule 20 to GUARDRAILS.md
- Update SIMPLE_HANDOFF_TEMPLATE header to designate it as THE standard
- Document exception process in the rule itself
- **No task files modified**

### Phase 2: Apply Prospectively to New Tasks (Ongoing)
- All new task files created after Phase 1 automatically comply
- Planning agents verify compliance during task creation
- No retroactive changes to existing tasks

### Phase 3: Opportunistic Updates to Selected/Reopened Tasks (As Needed)
- When a historical task is selected for new work, update it to template compliance during the move-to-active step
- During backlog sweeps, flag non-compliant tasks for optional update
- No deadline or forced migration

---

## 5. Decision Requests for Tracy

### Decision 1: Adopt Rule 20 as Standing Governance?
**Alternatives**:
- A) Adopt as written (minimal rule, explicit exception process, no mass retroactive updates)
- B) Adopt with modifications (Tracy proposes changes)
- C) Do not adopt; rely on existing template guidance only

**Recommendation**: A — the rule already exists implicitly in templates; formalizing it adds clarity without constraint.

### Decision 2: Designate SIMPLE_HANDOFF_TEMPLATE as THE Standard Dispatch Format?
**Alternatives**:
- A) Yes — retire HANDOFF_TEMPLATE.md (advanced) and all project-specific handoff templates; use SIMPLE for everything, fall back to full task file paste only for genuinely complex cases
- B) Keep both simple and advanced templates; clarify when each is appropriate
- C) Do not designate a standard; agents choose based on task complexity

**Recommendation**: A — template proliferation creates confusion. The SIMPLE format's anti-patterns section already documents what belongs in the task file vs. the handoff. Complex tasks should use the full task file as context, not a separate handoff template.

### Decision 3: Add Automated Template Validation?
**Alternatives**:
- A) Add a lightweight validation script (check YAML frontmatter + Agent Dispatch Interface presence)
- B) Rely on manual Task Readiness Checklist only
- C) No validation; trust agent compliance

**Recommendation**: B for now — the Task Readiness Checklist is sufficient. Automated validation can be added later if compliance becomes an issue.

---

## Appendix: Evidence from Current Repository State

### Files Supporting This Proposal

1. **TASK_TEMPLATE.md** already mandates Agent Dispatch Interface as "the CONTRACT between human and agent" and includes readiness checklist, stop conditions, and implementation step structure.
2. **SIMPLE_HANDOFF_TEMPLATE.md** explicitly forbids adding extra context beyond the task file, with concrete anti-pattern examples.
3. **GUARDRAILS.md** already has path-convention rules (Rule 10), pre-execution checks (Rule 3a), and lifecycle guidance — but no explicit rule making the task file authoritative over dispatch prompts.
4. **README.md** describes the task lifecycle (backlog → active → completed) and symlink conventions but does not address the relationship between task files and dispatch prompts.

### Tasks Demonstrating Good Compliance

- `2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` — complete Agent Dispatch Interface, all readiness boxes checked, concrete file:line targets, measurable acceptance criteria
- `2026-09-17-HIGH-ARCHITECTURE-GCC-ISSUANCE-RECIPIENT-AUTHORIZATION-IMPLEMENTATION-CONTRACT.md` — newly created, fully compliant with current template

### Tasks Demonstrating Gaps

- `2026-08-30-MEDIUM-FEATURE-OPERATIONAL-DATA-MODE-MODIFIERS-SCHEMA.md` — has Agent Dispatch Interface but unchecked readiness items and [FILL IN] placeholders
- Pre-template tasks in completed/2026-06/ — no YAML frontmatter, no Agent Dispatch Interface
