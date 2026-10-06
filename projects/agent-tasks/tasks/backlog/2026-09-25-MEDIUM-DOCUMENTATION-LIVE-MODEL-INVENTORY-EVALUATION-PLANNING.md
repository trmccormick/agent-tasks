---
status: backlog
priority: MEDIUM
type: documentation
system_domain: AGENT_WORKFLOW
mvp_alignment: CORE_OPERATIONS
local_worker_safe: true
cross_project_impact: true
---

> **Dispatch gate:** This backlog task is planning/documentation only. Do not dispatch it until Tracy explicitly approves it and the relevant host is available for the limited, read-only inventory verification the task requires. It does not authorize model execution, model downloads or removal, host configuration changes, routing-policy edits, or Git writes.

# TASK: Live Model Inventory Capture, Classification, and Evaluation Planning

**Status**: Backlog (draft for Tracy review; no agent dispatch yet)  
**Priority**: MEDIUM  
**Type**: Documentation / Planning  
**Repository**: agent-tasks (universal governance, not project-specific)  
**Created**: 2026-09-25

⚠️ **CROSS-PROJECT IMPACT**: This task produces inventory records and evaluation proposals that affect routing guidance across all projects tracked in agent-tasks. No model state changes are authorized by this task.

---

## Objective

Prepare a reviewable preview that proposes how the captured M4 and Ryzen Ollama inventories (collected 2026-09-24) should be recorded and evaluated:

1. A dated, evidence-labeled local-model inventory record.
2. A clear distinction between dated host-specific inventory evidence, documented current baseline, historical/superseded references, candidates requiring bounded evaluation, and deferred cleanup candidates.
3. A proposed bounded evaluation queue for newly available or changed models.
4. A separate deferred-maintenance note for the M4 Qwen 2.5 cleanup.

This task is **planning/documentation only**. It must not run model tests, modify live model state, delete models, change routing policy, or change model configurations.

---

## Authority Order

When conflicts arise, authority follows this order:

1. `rules/GUARDRAILS.md` (active guardrails)
2. This selected task file (when active)
3. Explicit human instruction from Tracy

Routing guidance in `ROUTING_LOGIC.md`, `rules/AGENT_ROUTING.md`, and `DECISIONS.md` is advisory unless explicitly updated by human decision.

---

## Host Roles and Inventory Scope

### Intel MacBook (orchestration/development surface)

- Primary Galaxy Game development surface.
- VS Code + GitHub Copilot.
- **No local Ollama models** — accesses M4 and Ryzen 7 remotely via Ollama when needed.
- The captured M4 output must NOT be attributed to this host.

### M4 Mac (model serving host)

- Serves Ollama models for local inference.
- Inventory captured on 2026-09-24 is a **host-specific historical snapshot** — it does not establish current loaded state or future availability.

### Ryzen 7 Windows (model serving host)

- Serves Ollama models for local inference.
- Inventory captured on 2026-09-24 is a **host-specific historical snapshot** — it does not establish current loaded state or future availability.

### Critical Host-Specific Rule

**The M4 and Ryzen inventories are host-specific.** Never infer that an M4 model exists on Ryzen or vice versa. A future evaluator must reconfirm host identity and live availability directly on the relevant host before proposing or executing any evaluation.

---

## Evidence — Captured Inventories (2026-09-24)

### M4 Mac — captured inventory snapshot (2026-09-24)

```text
qwen3.8:latest                   17 GB
gemma4:latest                    9.6 GB
qwen3.6-35b-copilot-m4:latest    23 GB
qwen3.6-27b-copilot-m4:latest    17 GB
qwen3.6:35b                      23 GB
qwen3.6:27b                      17 GB
qwen3.5:9b                       6.6 GB
qwen3.5:27b                      17 GB
deepseek-coder-v2:16b            8.9 GB
codestral:latest                 12 GB
qwen2.5-coder:14b                9.0 GB
nomic-embed-text:latest          274 MB
qwen2.5-coder:7b                 4.7 GB
```

At inventory time, `qwen3.6:35b` was loaded with a 262144-token context allocation. This is historical snapshot evidence only — it does not establish current loaded state or future availability.

### Ryzen 7 Windows — captured inventory snapshot (2026-09-24)

```text
gemma4:26b                        18 GB
gemma4:12b                        7.6 GB
qwen3.8-copilot-ryzen:latest      17 GB
qwen3.8:latest                    17 GB
qwen3.6-35b-copilot-ryzen:latest  23 GB
qwen3.6-27b-copilot-ryzen:latest  17 GB
qwen3.6:35b                       23 GB
qwen3.6:27b                       17 GB
deepseek-r1:8b                    5.2 GB
deepseek-r1:14b                   9.0 GB
llama3.1:8b                        4.9 GB
nomic-embed-text:latest            274 MB
```

At inventory time, `qwen3.6-35b-copilot-ryzen:latest` was loaded with a 262144-token context allocation. This is historical snapshot evidence only.

---

## Evidence-Labelling Rules

Every classification in the output must follow these rules:

| Evidence type | What it proves | What it does NOT prove |
|---|---|---|
| **Dated live snapshot** (e.g., `ollama list` from 2026-09-24) | Models installed on that host at that specific time | Current loaded state, future availability, task fitness, or cross-host presence |
| **Documented reference** (repository docs, routing tables) | Historical or current documented guidance | That a model is currently installed, usable, or appropriate for any specific task |
| **Observed task evidence** (completed handoffs, signed task files) | A model was successfully used for a specific task at a specific time | General suitability, ongoing availability, or fitness for other task types |
| **Unknown** | — | Any classification without direct evidence from one of the three categories above |

Additional rules:

1. **A live inventory snapshot proves only models listed on that host at that specific time.** It does not prove current loaded state, future availability, or task fitness.
2. **A documented reference does not prove a model is currently installed or usable.** Repository documentation may lag behind live state changes.
3. **A provider/version claim does not establish task fitness.** Availability ≠ suitability for any specific task type.
4. **A single successful evaluation does not establish general preference.** Bounded testing must be deliberate and repeatable before broader consideration.
5. **Historical references must be preserved.** Do not rewrite completed handoffs, decision history, or archived logs to remove older model references. They serve as historical record.

---

## Current Established Baseline (Known Facts)

| Model family | Operational status | Notes |
|---|---|---|
| **Qwen 3.6** (`qwen3.6:27b`, `qwen3.6:35b` and Copilot-tagged variants) | **Current established local baseline** | Deployed on both hosts 2026-06-23; validated as drop-in replacements for Qwen 3.5 with improved reasoning |
| **Qwen 3.8** (`qwen3.8:latest` and Copilot-tagged variants) | **Available for bounded evaluation only** | Not automatically preferred; not rejected; must be deliberately evaluated on bounded, low-risk work before any routing consideration |
| **Qwen 3.5** (`qwen3.5:9b`, `qwen3.5:27b`) | **Superseded and not in active use** | ROUTING_LOGIC.md retired Qwen 3.5 as primary executor (2026-06-23); historical references must remain intact |
| **Qwen 2.5** (`qwen2.5-coder:14b`, `qwen2.5-coder:7b`) | **Superseded with known tool issues** | Terminal execution blocked by design (documented in `docs/QWEN2.5_TOOL_BLOCKER.md`); M4 cleanup is deferred maintenance, outside this task's scope |
| **Other families** (Gemma, DeepSeek, Codestral, Nomic, Copilot-tagged) | **May be evaluated deliberately** | Availability alone is not a routing decision; each requires bounded evaluation before any role assignment |

---

## Scope

### In scope:
- Converting captured inventories into a dated, evidence-labeled inventory record
- Assigning each model exactly one primary disposition and one or more applicable evidence classes, using the definitions in the Required Output — Inventory Record section
- Proposing a bounded evaluation queue (≤3 candidate comparisons)
- Producing a reviewable preview/report before any routing, decision, or policy document is edited
- Preserving historical references in completed handoffs and decision history

### Out of scope:
- Running model tests, inference runs, or benchmarking
- Modifying live Ollama model state (pull, remove, create, copy)
- Changing routing policy, agent configuration, or shell profiles
- Connecting to M4 or Ryzen hosts remotely
- Editing `ROUTING_LOGIC.md`, `rules/AGENT_ROUTING.md`, `DECISIONS.md`, or any model configuration
- Creating more than one task file
- Rewriting historical documentation to remove Qwen 2.5 or Qwen 3.5 references

---

## Stop Conditions

**STOP and do not proceed if any of the following occur:**

1. Any step requires running `ollama rm`, `ollama pull`, `ollama run`, `ollama create`, or `ollama cp`.
2. Any step requires connecting to M4 (10.6.186.161) or Ryzen (10.6.186.50) remotely.
3. Any step requires modifying routing policy, agent configuration, shell profiles, or service definitions.
4. The output would declare any model "better," "worse," installed now, or ready for broader routing without new direct evidence.
5. The evaluation queue exceeds three candidate comparisons.

---

## Required Output — Inventory Record

Produce a classification table with these columns:

| Model | Host | Listed size | Primary disposition | Evidence classes | Evidence basis | Known dependency / use | Removal status |
|---|---|---|---|---|---|---|---|

Assign exactly one **primary disposition** to each row:

- `Established baseline`
- `Candidate for bounded evaluation`
- `Historical/superseded`
- `Deferred cleanup candidate`
- `Infrastructure dependency`
- `Unknown — needs review`

Also list one or more **evidence classes** for each row as applicable:

- `Dated live snapshot`
- `Documented reference`
- `Observed task evidence`
- `Unknown`

---

## Required Output — Evaluation Queue

Propose no more than three bounded comparisons. Each must include:

1. **Baseline model** — the current established reference
2. **Challenger model** — the model to compare against
3. **Host** — which host the comparison would run on
4. **Evaluation rationale** — why this comparison is warranted (based on captured inventory evidence, not provider claims)
5. **Scope limitation** — what the comparison must NOT do

### Required comparisons from captured inventory:

1. `qwen3.8:latest` vs `qwen3.6:27b` on M4 — identical, source-bounded read-only analysis
2. `deepseek-coder-v2:16b` vs `qwen3.6:27b` on M4 — after host availability is reconfirmed
3. One additional candidate only if supported by the actual captured inventory and a clear evaluation rationale

**All comparisons require later human approval and direct host-side availability confirmation before any testing.**

---

## Required Output — Deferred Maintenance Note

Create a clearly separated deferred-maintenance note for the M4 Qwen 2.5 cleanup:

```markdown
### Deferred Maintenance — M4 Qwen 2.5 Cleanup

**Models**: `qwen2.5-coder:14b`, `qwen2.5-coder:7b`  
**Host**: M4 Mac (verified M4 hostname)  
**Reason for deferral**: These models were carefully tested and had tool/terminal execution issues (documented in `docs/QWEN2.5_TOOL_BLOCKER.md`). They are superseded by the current Qwen 3.6 workflow. Removal is a future, separate action when M4 is idle.

**Estimated reclaimable**: ~13.7 GB (approximate sum of listed sizes — not guaranteed disk savings)

**Prerequisites for future cleanup**:
- M4 host must be free and idle
- `ollama ps` confirms neither model is loaded
- `ollama list` confirms both tags are installed
- No active Copilot agent config references either tag
- No active task file requires either tag
- Separate explicit human approval required before any removal

**Action boundary**: Do not include or run removal commands in this task. Any future cleanup must use a separately approved maintenance task that identifies the exact model tags and performs a fresh on-host preflight.
```

---

## Required Output — Reviewable Preview

Return a single reviewable report in the task response containing:

1. A proposed dated, evidence-labeled inventory record.
2. A proposed bounded evaluation queue.
3. A proposed deferred-maintenance note for M4 Qwen 2.5 cleanup.
4. A recommendation for one canonical future documentation destination, with reasons.

Do not create, edit, move, or delete any inventory record, evaluation log, cleanup note, routing document, decision record, configuration file, or task file. Tracy must explicitly approve both the preview and any future destination before a separate documentation-write task is created or dispatched.

---

## Handoff Summary

This task produces a planning-only inventory record and evaluation proposal from captured 2026-09-24 live snapshots. It classifies all M4 and Ryzen models into established categories, proposes ≤3 bounded comparisons for newly available models, and records the deferred Qwen 2.5 cleanup as a separate maintenance item. No model state changes, routing modifications, or remote host connections are authorized. The output is a reviewable preview requiring human approval before any documentation edits.
