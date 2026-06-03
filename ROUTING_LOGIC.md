# Routing Logic — Quick Reference
**Last Updated**: 2026-06-03

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.[cite: 3]
> This file is a quick-reference summary only.[cite: 3]

---

## ⚠️ EXPERIMENTAL TOOLS (Not in Active Routing)

- **Continue (VS Code Extension)**: Text-only terminal execution (no `run_terminal`), but CAN read files including git history.
  - DeepSeek-v2: Honest refusal ("unable to execute").
  - Codestral: Can access and read local files (including .git/ structures). Does NOT fabricate — output verified accurate. However, still cannot execute terminal commands.
  - **Limited use case**: Architecture review, file analysis, git history reading. **NOT recommended for active workflow** since Qwen3.5 in Copilot does everything Continue does PLUS terminal execution.

---

## The One Rule

**Local workspace agents handle file reads, active terminal triage, planning, and targeted edits.**[cite: 3]
**Cloud agents handle mechanical execution, high-speed 0x tasks, or deep complex reviews.**[cite: 3]
**Cloud agents never receive the full codebase — snippets only.**[cite: 3]

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session. The same model can fill different roles in different sessions.[cite: 3]

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Qwen3.5-9B (Copilot) | Triage, plan, delegate, update task files — never execute code in this role[cite: 3] |
| **EXECUTOR** | GPT-5 mini / Raptor mini (Cloud), Qwen3.5-9B / 27B (Local Live Work) | Implement assigned tasks only — never self-assign[cite: 3] |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only[cite: 3] |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access[cite: 3] |
| **RESEARCHER** | Perplexity | External documentation lookup[cite: 3] |
| **DOCUMENTATION AGENT** | Gemini (Web - Context/Intent) / Qwen3.5-27B (Local - Tech Structure) | Parse repositories, maintain markdown guides, and sync code realities with gameplay design rules[cite: 3] |

---

## ⚖️ Cross-Auditing & Verification Protocol (Anti-Hallucination Gate)

To prevent structural drift and trope hallucinations, no agent output may be finalized without a cross-audit from a separate model family.[cite: 3]

1. **The Code-to-Doc Audit**: When a local agent (e.g., Qwen) generates architectural maps or changes code, a context agent (e.g., Gemini) must audit the output against the master design intent to ensure it has not slipped into generic 4X/turn-based tropes.[cite: 3]
2. **The Doc-to-Code Audit**: When a design rule is updated, the local execution agent must immediately run targeted terminal checks (e.g., running specific RSpecs with `unset DATABASE_URL`) to verify that the codebase physically reflects the documentation.[cite: 3]
3. **The Contradiction Ban**: Agents are explicitly ordered to cross-reference their active output against `docs/new_agent/agent_guides/galaxy_game.md`. If a generated code pattern or task file assumes mechanics (like turn-clocks) that contradict the guide, the execution must be halted and flagged for the human.[cite: 3]

---

## 📋 Task Assignment & Operational Handoff Rules (STRATEGIST Mandate)

When the STRATEGIST (Qwen3.5-9B) is updating task files, allocating work, or closing out a session, it must strictly adhere to these operational steps to prevent state drift:[cite: 3]

### 1. The State-Tracking Triad
Every active development cycle requires updating three core files simultaneously to maintain project telemetry:[cite: 3]
- **Task Files**: Must be built using `TASK_TEMPLATE.md` exactly, clearing out all old template suffixes. Every assignment must clearly define the scope, target specific RSpec files, and state explicit **Escalation Criteria** (e.g., *"If local tests fail due to unexpected environmental dependency collisions, halt execution and report back to the human—do not rewrite core network infrastructure"*).[cite: 3]
- **`STATUS.md` Updates**: The STRATEGIST must update the global tracking log whenever a task shifts from active to completed or when a block is cleared by a verified RSpec pass.[cite: 3]
- **`DECISIONS.md` Logging**: Any architectural pivot, database model adjustment, or game balancing deviation from the master `galaxy_game.md` context must be logged as a permanent entry in `DECISIONS.md` before work is handed off.[cite: 3]

### 2. Executor Handoff Mechanics
When assigning a blueprint to an EXECUTOR model (whether cloud-based or local live work), the STRATEGIST must append a **Simple Handoff Template** to the active task file. This template must explicitly map:[cite: 3]
1. **Priority Stacking**: Tasks must be ordered sequentially (Task 1, Task 2). Executors are strictly banned from working out-of-order.[cite: 3]
2. **Context Injection**: Explicit instructions on which domain guides (e.g., `@galaxy_game.md`) the executor must pull into their session before writing code.[cite: 3]
3. **The Sandbox Constraint**: A reminder that Git is unavailable in the container and raw JSON changes are prohibited.[cite: 3]

### 3. Session End Handoffs
At the conclusion of an exploration or development session, the active strategist agent must synthesize a concise **Session Handoff Document** summarizing what was validated, what paths were checked via terminal, and what state the active task folder is being left in for the next active launch.[cite: 3]

---

## AI Stack at a Glance

**⚠️ TESTED FINDINGS (2026-06-03):**
- **Copilot + Qwen3.5 (both 9b & 27b)**: ✅ Tools work correctly
- **Copilot + Qwen2.5 (14b & 7b)**: ❌ Tool format incompatibility (outputs JSON, Copilot doesn't execute)
- **Continue**: ❌ All tools disabled by design (text-only mode)
- **DeepSeek-v2:16b & Codestral**: Not selectable in Copilot (discovered but hidden from dropdown)

| Agent | Cost | When to Use | Local File/Terminal Access |
|---|---|---|---|
| **Qwen3.5-9B (Copilot)** | Free/local | **Fast Local Strategist / Planner**, quick triage, terminal context gathering, task detailing | ✅ Yes (Tested & confirmed working)[cite: 3] |
| **Qwen3.5-27B (Copilot)** | Free/local | **Heavy Local Executor**, deep logic validation, terminal verification runner, complex multi-file reasoning | ✅ Yes (Tested & confirmed working)[cite: 3] |
| ~~Codestral (Continue)~~ | Free/local | ~~Complex offline reasoning~~ **NOT SELECTABLE IN COPILOT** | ❌ Text-only (Continue disabled tools)[cite: 3] |
| ~~Qwen2.5 variants (Copilot)~~ | Free/local | ~~Code implementation~~ **DO NOT USE — TOOL FORMAT BROKEN** | ❌ Tool calls not executed (format mismatch)[cite: 3] |
| **GPT-5-mini (IDE Cloud)** | 0x Tier | **Implementation Worker** for well-specified mechanical code & migrations | ✅ Yes (IDE Workspace Only)[cite: 3] |
| **Raptor mini (Preview)** | 0x Tier | **Alternative Implementation Worker** for mechanical code, quick logic blocks, and specs | ✅ Yes (IDE Workspace Only)[cite: 3] |
| Claude (web) | Free tier | High-level conceptual reviews or planning fallback only | ❌ No[cite: 3] |
| Gemini (web) | Free | Macro session planning, priority stacking, and deep domain game-balance advice | ❌ No[cite: 3] |
| Perplexity (web) | Free | Research, workspace task deployment validation | ❌ No[cite: 3] |

---

## Quick Routing Table

| Task | Agent | Role Context | Notes |
|---|---|---|---|
| **Triage overnight failures / build task files** | Qwen3.5-9B (Copilot) | **STRATEGIST**[cite: 3] | Fast, proven tool support |
| **Verify workspace paths (Ledger check)** | Qwen3.5-9B (Copilot) | **STRATEGIST**[cite: 3] | Terminal-verified only |
| **Execute live specs via terminal to check errors** | Qwen3.5-27B (Copilot) | **EXECUTOR**[cite: 3] | ⚠️ ONLY Qwen3.5 works; Qwen2.5 format broken |
| Macro session planning / priority stacking | Gemini (web) or Qwen3.5-9B | **STRATEGIST**[cite: 3] | Avoid Qwen2.5 for planning |
| Validate task deployment clarity | Perplexity (web) | **RESEARCHER**[cite: 3] |  |
| Architecture decision / multi-file design | Qwen3.5-27B (Copilot) | **REVIEWER / EXECUTOR**[cite: 3] | Primary local option |
| **Mechanical code implementation (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)**[cite: 3] |  |
| **Database migration scaffolding (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)**[cite: 3] |  |
| Fix a single spec — cause is known | Qwen3.5-9B | **EXECUTOR (Local)**[cite: 3] | Verified working |
| Fix a single spec — cause unknown | Qwen3.5-27B (Terminal verification) | **EXECUTOR (Local)**[cite: 3] | Deep reasoning + terminal tools |
| Multi-file refactor (Patterns specified) | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)**[cite: 3] |  |
| Write docs / update task tracking | Qwen3.5-9B (Copilot) | **EXECUTOR (Local)**[cite: 3] | Use Qwen3.5, not Qwen2.5 |

---

## Hard Rules

- **⚠️ Qwen2.5 Tool Incompatibility (Copilot)**: Qwen2.5 models (7b, 14b, 32b) output tool-calls in a format that Copilot doesn't recognize/execute. They are NOT suitable for terminal automation in Copilot. Do NOT use in active routing.[cite: 3]
- **Qwen3.5 Only for Copilot Terminal Tools**: Both Qwen3.5-9B and Qwen3.5-27B support terminal execution in Copilot. These are the ONLY local Copilot agents confirmed working with `run_in_terminal` tool.[cite: 3]
- **Continue is Experimental/Deprecated**: Continue disables terminal execution (`run_terminal` tool) for all models but allows file reading. Cannot execute, so no advantage over Copilot + Qwen3.5 which does both reading AND execution. Keep for architecture analysis only, but do NOT use for active task routing since Qwen3.5 covers all use cases.[cite: 3]
- **The Verified Execution Rule**: Local models utilizing the `run_in_terminal` tool (Qwen3.5 series in Copilot) MUST use it to verify files and run live tests instead of fabricating or assuming test results.[cite: 3]
- **Role Isolation**: The **STRATEGIST** generates and coordinates the plan. The **EXECUTOR** acts on existing task blueprints and never self-assigns tasks.[cite: 3]
- **Scrub Your Templates**: Ensure all generated task files strip out old `_2` template suffixes and use `TASK_TEMPLATE.md` exactly.[cite: 3]
- All codebase-wide scans go to local models — never send the full codebase to cloud agents.[cite: 3]
- One RSpec runner at a time — never run parallel spec execution inside Docker.[cite: 3]
- **Supervised Host-Only Version Control**: Because Git is not installed or accessible inside the Docker container environment, all version control operations must be executed directly on the host node (Intel Mac). Agents may stage and commit application code, specs, and markdown documentation under direct human supervision on the host, but they are strictly prohibited from attempting Git commands inside Docker.
- **Data Commit Restriction**: Staging or committing raw JSON data files is strictly banned at this time; version control tracking is reserved exclusively for source code, tests, and documentation.