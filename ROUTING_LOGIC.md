# Routing Logic — Quick Reference
**Last Updated**: 2026-06-02

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## The One Rule

**Local workspace agents handle file reads, triage, planning, and targeted edits.**
**Cloud agents handle mechanical execution, high-speed 0x tasks, or deep complex reviews.**
**Cloud agents never receive the full codebase — snippets only.**

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session. The same model can fill different roles in different sessions.

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Haiku (Copilot) | Triage, plan, delegate, update task files — never execute |
| **EXECUTOR** | Continue agents (primary), GPT-5 mini/Raptor mini (fallback) | Implement assigned tasks only — never self-assign |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access |
| **RESEARCHER** | Perplexity | External documentation lookup |

---

## AI Stack at a Glance

| Agent | Cost | When to Use |
|---|---|---|
| **Qwen3.5-9B (Copilot Endpoint)** | Free/local | **PRIMARY Local Planner**, failure triage, task detailing, general coding workhorse[cite: 3, 6] |
| Codestral (M4) | Free/local | Architecture, complex multi-file logic/synthesis[cite: 3, 6] |
| Qwen3.5-27B (Windows) | Free/local | High-complexity coding, validation, second opinions[cite: 3, 6] |
| Qwen2.5-3B (Windows) | Free/local | Small targeted edits, JSON, quick doc changes[cite: 3, 6] |
| **GPT-5-mini (IDE Cloud)** | 0x Tier | **Implementation Worker** for well-specified mechanical code & migrations[cite: 2, 6] |
| **Raptor mini (Preview) (IDE Cloud)** | 0x Tier | **Alternative Implementation Worker** for mechanical code, quick logic blocks, and specs |
| Claude (web) | Free tier | High-level conceptual reviews or planning fallback only[cite: 3, 6] |
| Gemini (web) | Free | Macro session planning and initial priority stacking[cite: 3, 6] |
| Perplexity (web) | Free | Research, workspace task deployment validation[cite: 3, 6] |

> **Copilot / Ollama Note**: Local models are now accessed directly as custom Copilot endpoints[cite: 6]. 
> Do not route Galaxy Game tasks to Cloud Copilot premium models; target local Qwen3.5 models or your designated 0x cloud workers (GPT-5-mini / Raptor mini)[cite: 2, 3, 6].

---

## Quick Routing Table

| Task | Agent |
|---|---|
| **Triage overnight failures / build task files** | Qwen3.5-9B (Local Copilot)[cite: 3, 6] |
| **Verify workspace paths (Ledger check)** | Qwen3.5-9B (Local Copilot)[cite: 5, 6] |
| Macro session planning / priority stacking | Gemini (web)[cite: 3, 6] |
| Validate task deployment clarity | Perplexity (web)[cite: 6] |
| Architecture decision / multi-file design | Codestral (M4)[cite: 3, 6] |
| **Mechanical code implementation (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview)[cite: 2, 6] |
| **Database migration scaffolding (0x)** | GPT-5-mini (Cloud) or Raptor mini (Preview)[cite: 2, 6] |
| Fix a single spec — cause is known | Qwen3.5-9B[cite: 3, 6] |
| Fix a single spec — cause unknown | Codestral ➔ Qwen3.5-9B[cite: 3] |
| Multi-file refactor (Patterns specified) | GPT-5-mini (Cloud) or Raptor mini (Preview)[cite: 2, 6] |
| Multi-file refactor (Complex synthesis) | Codestral synthesis ➔ Qwen3.5-9B[cite: 3] |
| Edit a JSON / small config file | Qwen2.5-3B[cite: 3, 6] |
| Write docs / update task tracking | Qwen2.5-3B or Qwen3.5-9B[cite: 3, 6] |
| Complex logic / second opinion | Qwen3.5-27B[cite: 3, 6] |

---

## Hard Rules

- **No Continue Sidebar**: All local models must be invoked directly inside the editor via GitHub Copilot custom agent commands[cite: 6].
- **The Fabrication Rule**: Local models can read active workspace files via Copilot context, but they cannot execute shell, git, or Docker commands[cite: 6]. Never let them guess test results or schemas[cite: 6].
- **Scrub Your Templates**: Ensure all generated task files strip out old `_2` template suffixes and use `TASK_TEMPLATE.md` exactly[cite: 4, 5].
- All codebase-wide scans go to local models — never send the full codebase to cloud agents[cite: 3, 6].
- One RSpec runner at a time — never run parallel spec execution inside Docker[cite: 3, 6].
- Commits are strictly performed by the human from the host node (Intel Mac)[cite: 3, 6].
