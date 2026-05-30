# Routing Logic — Quick Reference
**Last Updated**: 2026-05-29

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## The One Rule

**Local models handle file reads and targeted edits.**
**Cloud agents handle reasoning, review, and planning.**
**Cloud agents never receive the full codebase — snippets only.**

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session. The same model can fill different roles in different sessions.

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Haiku (Copilot) | Triage, plan, delegate, update task files — never execute |
| **EXECUTOR** | GPT-4.1, Continue local agents | Implement assigned tasks only — never self-assign |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access |
| **RESEARCHER** | Perplexity | External documentation lookup |

---

## AI Stack at a Glance

| Agent | Cost | Role | Local Files | When to Use |
|---|---|---|---|---|
| Claude (web) | Free tier | REVIEWER | ❌ | Architecture decisions, complex task creation, spot reviews |
| Haiku (Copilot) | 0.33x | STRATEGIST | ✅ | Session planning, triage, task file management, delegation |
| GPT-4.1 (Copilot) | 0x* | EXECUTOR | ✅ | Implementation tasks, spec fixes, migrations |
| Gemini (web) | Free | DOMAIN EXPERT | ❌ | Geological rules, game balance, scientific questions |
| Perplexity (web) | Free | RESEARCHER | ❌ | Samvera/Hyku docs, external research |
| Codestral (M4) | Free/local | EXECUTOR/ARCHITECT | ✅ via Continue | Architecture, complex multi-file reasoning |
| Qwen3.5-27B (M4) | Free/local | EXECUTOR/AUDITOR | ✅ via Continue | Backlog audit, heavy structural reasoning, second opinion |
| Qwen3.5-9B (Windows/M4) | Free/local | EXECUTOR | ✅ via Continue | Primary coding workhorse, implementation, general tasks |
| Qwen3-30B (Windows) | Free/local | EXECUTOR | ✅ via Continue | Legacy high-complexity tasks |
| Qwen2.5-14B (M4) | Free/local | EXECUTOR | ✅ via Continue | Implementation, code refinement |
| Qwen2.5-3B (Windows) | Free/local | EXECUTOR | ✅ via Continue | Small targeted edits, JSON, docs |
| Llama 3.1 8B (Windows) | Free/local | EXECUTOR | ✅ via Continue | General tasks, lightweight chat, fallback |

> *GPT-4.1 moves to token-based billing June 1st 2026. Routing will be updated after transition impact is assessed. Do not route Galaxy Game tasks to Copilot for work/Samvera projects.

---

## Quick Routing Table

| Task | Agent | Role |
|---|---|---|
| Session triage / priority stack | Haiku (Copilot) | STRATEGIST |
| Task file creation / backlog audit | Haiku (Copilot) | STRATEGIST |
| Architecture decision / design review | Claude (web) | REVIEWER |
| Geological/game balance question | Gemini (web) | DOMAIN EXPERT |
| Research Samvera/Hyku community | Perplexity | RESEARCHER |
| Architecture decision / multi-file design | Codestral (M4) | EXECUTOR/ARCHITECT |
| Audit logic before implementing | Codestral (M4) | EXECUTOR/ARCHITECT |
| Fix a single spec — cause is known | Qwen3.5-9B or GPT-4.1 | EXECUTOR |
| Fix a single spec — cause unknown | Codestral audit → Qwen3.5-9B | EXECUTOR |
| Multi-file refactor | Codestral synthesis → Qwen3.5-9B | EXECUTOR |
| Backlog file audit / task cleanup | Qwen3.5-27B (M4) | EXECUTOR/AUDITOR |
| Search the codebase | Qwen3.5-9B + Nomic Embed | EXECUTOR |
| Edit a JSON / small config file | Qwen2.5-3B | EXECUTOR |
| Write docs after a change | Qwen2.5-3B or Qwen3.5-9B | EXECUTOR |
| Complex logic / second opinion | Qwen3.5-27B or DeepSeek 16B (M4) | EXECUTOR |
| Work/Samvera tasks | Copilot (token budget aware) | EXECUTOR |

---

## Hard Rules

- M4 must stay caffeinated (`caffeinate` / `pmset`) for stable Ollama connection
- All codebase-wide scans go to local models — never send full codebase to cloud
- Cloud agents receive only specific file snippets needed for the task
- One RSpec runner at a time — never run parallel spec execution
- Commits always from Intel Mac (host/orchestration node)
- `unset DATABASE_URL` before every RSpec run inside Docker — never omit
- Synthesis report before any code change — Executors must stop and wait for approval
- Do not route Galaxy Game tasks to Copilot (token budget) — use GPT-4.1 Copilot agent instead

---

## June 2026 Transition Note

GitHub Copilot moves to token-based billing effective June 1st 2026. Impact on routing:
- GPT-4.1 (Copilot agent) — was 0x free, becomes token-based
- Routing table will be updated after transition impact is assessed
- Until updated: treat GPT-4.1 as conservation-worthy, prefer local agents for implementation
- Local Qwen3.5 series is the primary fallback for implementation work
