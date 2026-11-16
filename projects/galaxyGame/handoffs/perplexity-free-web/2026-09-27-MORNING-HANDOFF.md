Session Handoff — Agent-Tasks Governance and Model Planning
Date: 2026-09-27
Repository: agent-tasks
Branch: main
Working tree at last verification: Clean
Push status: Local commits only; no push was performed during this work

Current Git state
Latest confirmed commit:

text
d0fc03b (HEAD -> main) chore: mark superseded workflow audit task
Earlier related commit:

text
ed5ac79 docs: make model routing eligibility-first
Run at the start of the next session:

bash
git status --short
git log -5 --oneline
Expected: clean working tree, with d0fc03b as the latest local commit unless later work has occurred.

Completed work
1. Routing documentation updated and committed
Commit:

text
ed5ac79 docs: make model routing eligibility-first
Files changed:

text
ROUTING_LOGIC.md
rules/AGENT_ROUTING.md
Result:

Added advisory-status language: routing records preferences/observations but does not override guardrails, selected task files, or explicit human instruction.

Removed automatic named-provider escalation to Claude Haiku 4.5 from local failure paths.

Replaced the old “Always Try Local First” Qwen-specific default with “Eligibility Review First.”

Added guidance on high-multiplier Copilot use and the evidence limits of free web-model output.

Added a bounded new/changed-model evaluation rule.

Kept model availability and performance preferences advisory rather than automatic routing mandates.

Important current model-position language:

Qwen 3.6: established local baseline.

Qwen 3.8: available for bounded comparative evaluation; do not call it nonfunctional, categorically worse, or an automatic replacement.

Qwen 3.5: superseded and not in active use now that Qwen 3.6 is available.

Qwen 2.5: superseded; had carefully tested tool/terminal limitations.

2. Live inventories captured
These were real, host-local ollama list / ollama ps snapshots collected on 2026-09-24. They are dated evidence only—not proof of current state.

M4 Mac
At capture time, qwen3.6:35b was loaded with 262,144-token context.

Installed inventory:

text
qwen3.8:latest
gemma4:latest
qwen3.6-35b-copilot-m4:latest
qwen3.6-27b-copilot-m4:latest
qwen3.6:35b
qwen3.6:27b
qwen3.5:9b
qwen3.5:27b
deepseek-coder-v2:16b
codestral:latest
qwen2.5-coder:14b
nomic-embed-text:latest
qwen2.5-coder:7b
Ryzen 7 Windows
At capture time, qwen3.6-35b-copilot-ryzen:latest was loaded with 262,144-token context.

Installed inventory:

text
gemma4:26b
gemma4:12b
qwen3.8-copilot-ryzen:latest
qwen3.8:latest
qwen3.6-35b-copilot-ryzen:latest
qwen3.6-27b-copilot-ryzen:latest
qwen3.6:35b
qwen3.6:27b
deepseek-r1:8b
deepseek-r1:14b
llama3.1:8b
nomic-embed-text:latest
Key resolved inventory point:

llama3.1:8b was actually present on Ryzen.

qwen3:8b did not appear in the Ryzen live inventory and should not be assumed present.

Host roles:

Intel MacBook / LIB-DCL-TRACYMK: orchestration/development surface; no local Ollama models.

M4: model-serving host.

Ryzen: model-serving host.

Never attribute M4 model output to the Intel MacBook or infer models cross-host.

3. Model-inventory planning task created locally
Created task file:

text
projects/agent-tasks/tasks/backlog/2026-09-25-MEDIUM-DOCUMENTATION-LIVE-MODEL-INVENTORY-EVALUATION-PLANNING.md
Purpose:

Produces a planning-only, reviewable inventory classification and bounded evaluation proposal.

Does not authorize model execution, benchmark/inference runs, model pulls/removal, host configuration changes, remote access, routing edits, or Git writes.

Requires explicit Tracy approval and relevant host availability before dispatch.

Preserves historical references to Qwen 2.5 and Qwen 3.5.

Requires any model comparison to be later authorized, host-side availability reconfirmed, bounded, and evidence-based.

Note: Earlier reporting said this task directory was ignored; later operations showed project task files can appear in Git diffs/staging. Treat actual local Git state as authoritative next session—do not assume ignore behavior without checking.

4. Deferred M4 cleanup identified
When the M4 is idle—not during active work—consider removing only:

text
qwen2.5-coder:14b
qwen2.5-coder:7b
Rationale:

Carefully tested; known tool/terminal execution limitations.

Superseded by current Qwen 3.6 workflow.

Approximate listed footprint: 9.0 GB + 4.7 GB = ~13.7 GB, though actual reclaimed storage may differ.

This is deferred maintenance, not an active task and not part of the model-inventory planning task.

Do not remove while M4 has ongoing work or a relevant loaded session. When the M4 is truly idle, use a short separate maintenance flow:

bash
ollama ps
ollama rm qwen2.5-coder:14b
ollama rm qwen2.5-coder:7b
ollama list
ollama ps
Do not bundle Qwen 3.5 removal into this cleanup. Qwen 3.5 is superseded/not used, but its final removal should be a separate, explicit maintenance decision.

5. MAG governance set reconciled
Read-only reconciliation established that all six MAG drafts are already implemented as live rules in rules/GUARDRAILS.md:

MAG draft	Live location
MAG-1 — Task File as Execution Contract	GUARDRAILS.md line ~648
MAG-2 — Human-Controlled Dispatch Authority	line ~668
MAG-3 — Capability/Availability-Based Routing	line ~680
MAG-4 — Dependency/Parallelization	line ~694
MAG-5 — Preferences as Guidance, Not Rules	line ~704
MAG-6 — Per-Project Session Guidance	line ~716
Conclusions:

MAG-1 through MAG-6 are historical draft artifacts, not pending governance work.

Do not dispatch MAG-1 or revive the MAG sequence.

Do not add retrospective bookkeeping to GUARDRAILS.md; it is operational policy, not a task-history ledger.

The six MAG draft files should remain unchanged until a project-local task lifecycle/archive convention is deliberately designed.

The July guardrails/workflow tasks were also reviewed:

2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md: already marked completed.

2026-07-03-MEDIUM-AUDIT-STANDARDIZE-AGENT-TASK-WORKFLOW.md: was incorrectly marked active even though effectively superseded.

2026-07-04-HIGH-DOCUMENTATION-MERGE-GUARDRAILS-GAPS.md: likely superseded, but do not change it yet.

6. July 3 task status corrected and committed
Commit:

text
d0fc03b chore: mark superseded workflow audit task
Changed exactly one file:

text
projects/agent-tasks/tasks/backlog/2026-07-03-MEDIUM-AUDIT-STANDARDIZE-AGENT-TASK-WORKFLOW.md
Change:

text
status: active
became:

text
status: superseded
Added immediately after frontmatter:

text
> **Superseded status:** This audit's governance-gap objective was subsequently addressed by the live MAG-1 through MAG-6 rules in `rules/GUARDRAILS.md`. This task is retained in place as historical workflow context; it is not an active execution contract.
No other task artifacts, directories, templates, routing documentation, or guardrails were changed as part of that commit.

Deferred decisions
A. Task lifecycle convention
Do not perform bulk cleanup of the MAG or July artifacts yet.

A later planning-only task should determine whether projects/agent-tasks/tasks/ needs its own lifecycle conventions, such as:

completed/

superseded/

archive/

possibly drafts/ or review/

Questions to resolve first:

Is superseded an approved frontmatter status in the task template?

Should a standard superseded_by field exist?

Should historical artifacts stay in backlog/ with accurate status, or be moved only after a project-local archive convention exists?

How should completed, superseded, historical-draft, and archived-hand-off artifacts differ?

Do not move task files into Galaxy Game’s archive. Keep project scopes separate.

B. Remaining historical artifacts
Leave unchanged for now:

Six MAG task drafts.

July 2 completed consolidation task.

July 4 guardrails-gaps task.

Do not “repair” the MAG-3 missing priority field merely because it is a historical draft defect.

C. Model evaluation
Potential future evaluation order, after host availability is reconfirmed and a specific test is approved:

qwen3.8:latest versus qwen3.6:27b on M4 using identical source-bounded read-only analysis.

deepseek-coder-v2:16b versus qwen3.6:27b on M4.

A third candidate only if a clear gap/rationale exists.

Do not promote any model after a single task. Measure scope accuracy, evidence discipline, unsupported claims, human correction burden, response time, and operational impact.

Recommended next session start
Verify repository state:

bash
cd /path/to/agent-tasks
git status --short
git log -5 --oneline
Confirm no new user priority supersedes this handoff.

Choose one of these workstreams:

Resume the actual project/task portfolio and identify the next real project task to dispatch.

Create a planning-only task for an agent-tasks lifecycle convention.

Dispatch the existing live-model inventory planning task later when the M4 is suitable.

Perform the deferred M4 Qwen 2.5 cleanup only when the M4 is idle and only as a distinct maintenance action.

Do not resume governance cleanup automatically. The operational governance work is already live.

Minimal handoff summary
text
HANDOFF SUMMARY: Routing policy committed (`ed5ac79`); July workflow audit correctly superseded and committed (`d0fc03b`); MAG-1–MAG-6 confirmed already live in GUARDRAILS and remain historical drafts; M4/Ryzen inventories captured; model-evaluation planning task created locally; Qwen 2.5 M4 cleanup deferred until idle; next decision is real project work versus a separate lifecycle-convention planning task.
