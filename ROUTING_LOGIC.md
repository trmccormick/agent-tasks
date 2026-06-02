# Routing Logic — Quick Reference
**Last Updated**: 2026-06-02

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## The One Rule

**Local workspace agents handle file reads, active terminal triage, planning, and targeted edits.**
**Cloud agents handle mechanical execution, high-speed 0x tasks, or deep complex reviews.**
**Cloud agents never receive the full codebase — snippets only.**

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session. The same model can fill different roles in different sessions.

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Qwen3.5-9B (Copilot) | Triage, plan, delegate, update task files — never execute code in this role |
| **EXECUTOR** | GPT-5 mini / Raptor mini (Cloud), Qwen3.5-9B / 27B (Local Live Work) | Implement assigned tasks only — never self-assign |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access |
| **RESEARCHER** | Perplexity | External documentation lookup |

---

## AI Stack at a Glance

| Agent | Cost | When to Use | Local File/Terminal Access |
|---|---|---|---|
| **Qwen3.5-9B (Copilot)** | Free/local | **PRIMARY Local Strategist / Planner**, failure triage, terminal context gathering, task detailing | ✅ Yes (Native `run_in_terminal`) |
| **Qwen3.5-27B (Copilot)** | Free/local | High-complexity local coding, deep logic validation, terminal verification runner | ✅ Yes (Native `run_in_terminal`) |
| Codestral (Continue) | Free/local | Complex offline reasoning, multi-file structural planning (No terminal execution) | ❌ Read Only (No terminal tool) |
| Qwen2.5-3B (Windows) | Free/local | Small targeted text edits, JSON configurations, quick doc updates | ❌ Read Only (No terminal tool) |
| **GPT-5-mini (IDE Cloud)** | 0x Tier | **Implementation Worker** for well-specified mechanical code & migrations | ✅ Yes (IDE Workspace Only) |
| **Raptor mini (Preview)** | 0x Tier | **Alternative Implementation Worker** for mechanical code, quick logic blocks, and specs | ✅ Yes (IDE Workspace Only) |
| Claude (web) | Free tier | High-level conceptual reviews or planning fallback only | ❌ No |
| Gemini (web) | Free | Macro session planning, priority stacking, and deep domain game-balance advice | ❌ No |
| Perplexity (web) | Free | Research, workspace task deployment validation | ❌ No |

---

## Quick Routing Table

| Task | Agent | Role Context |
|---|---|---|
| **Triage overnight failures / build task files** | Qwen3.5-9B (Local Copilot) | **STRATEGIST** |
| **Verify workspace paths (Ledger check)** | Qwen3.5-9B (Local Copilot) | **STRATEGIST** |
| **Execute live specs via terminal to check errors** | Qwen3.5-9B / 27B (Local Copilot) | **STRATEGIST / EXECUTOR** |
| Macro session planning / priority stacking | Gemini (web) or Qwen3.5-9B | **STRATEGIST** |
| Validate task deployment clarity | Perplexity (web) | **RESEARCHER** |
| Architecture decision / multi-file design | Codestral (Continue) or Qwen3.5-27B | **REVIEWER / EXECUTOR** |
| **Mechanical code implementation (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)** |
| **Database migration scaffolding (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)** |
| Fix a single spec — cause is known | Qwen3.5-9B | **EXECUTOR (Local)** |
| Fix a single spec — cause unknown | Qwen3.5-27B (Terminal verification) | **EXECUTOR (Local)** |
| Multi-file refactor (Patterns specified) | GPT-5-mini (Cloud) or Raptor mini (Preview) | **EXECUTOR (Cloud)** |
| Edit a JSON / small config file | Qwen2.5-3B | **EXECUTOR (Local)** |
| Write docs / update task tracking | Qwen2.5-3B or Qwen3.5-9B | **EXECUTOR (Local)** |

---

## Hard Rules

- **No Continue Sidebar for Automation**: Continue models (like Codestral) lack terminal tools. All active terminal automation must be executed via GitHub Copilot custom agent commands.
- **The Verified Execution Rule**: Local models utilizing the `run_in_terminal` tool (Qwen3.5 series) MUST use it to verify files and run live Docker specs instead of fabricating or assuming test results.
- **Role Isolation**: The **STRATEGIST** generates and coordinates the plan. The **EXECUTOR** acts on existing task blueprints and never self-assigns tasks.
- **Scrub Your Templates**: Ensure all generated task files strip out old `_2` template suffixes and use `TASK_TEMPLATE.md` exactly.
- All codebase-wide scans go to local models — never send the full codebase to cloud agents.
- One RSpec runner at a time — never run parallel spec execution inside Docker.
- Commits are strictly performed by the human from the host node (Intel Mac).