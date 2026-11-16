# AI Manager Backlog

Cross-cutting AI Manager work that sits **outside** the world-settlement phase folders (phase05-luna, phase09-mars, etc.).

The settlement phases produce the real knowledge (especially the Luna loop and later Earth–Mars–Venus logistics).  
The AI Manager is intended to consume and generalize that knowledge into resource-first foothold planning.

Tasks here are lower urgency than the active Luna / logistics work and can be pulled when bandwidth allows.

## Current Tasks

| Priority | File | Intent |
|----------|------|--------|
| **HIGH** | `2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md` | Architecture + minimal interface for a planner driven by body/system resources instead of pattern names |
| **MEDIUM** | `2026-09-01-MEDIUM-REFACTOR-MISSION-PLANNER-ENTRY-POINT.md` | Evolve MissionPlannerService so it can accept (or cooperate with) resource-first input while keeping the old pattern path working |
| **MEDIUM** | `2026-09-01-MEDIUM-FEATURE-CAPTURE-LUNA-WORKED-EXAMPLE.md` | Capture the working Luna loop as structured training/reference data for the future planner |
| **MEDIUM** | `2026-09-01-MEDIUM-ARCHITECTURE-SUPER-MARS-NO-MOON-TEST-CASE.md` | Formalize the Super-Mars (no moons) scenario as a planner test case |
| **MEDIUM** | `2026-09-01-MEDIUM-DOCUMENTATION-AI-MANAGER-OUTSIDE-PHASE-STRUCTURE.md` | Explicitly document that AI Manager sits outside the phase sequence |
| **LOW** | `2026-09-01-LOW-RESEARCH-AI-MANAGER-SERVICE-INVENTORY-AND-GAPS.md` | Lightweight classification of existing AI Manager services and gaps relevant to resource-first planning |

## Suggested Order When Bandwidth Allows

1. Documentation task (quick clarity win)
2. HIGH Foothold Planner architecture
3. MissionPlanner entry-point refactor (after or in parallel with #2)
4. Super-Mars test case
5. Luna worked-example capture (after Luna loop is stable)
6. Service inventory (anytime, low urgency)
