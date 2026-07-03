# Qwen Validation — Guardrails Migration Path

**Date**: 2026-07-01  
**Validator**: Qwen (Local Validation Agent)  
**Status**: VALIDATED — Ready for Executor with Pre-Work

---

## 1. Mapping Verification Result

Most target directories already exist and are populated with content, which is a strong positive signal. However, two gaps were found: `docs/systems/` does not exist at all, and `docs/architecture/terrain/` is missing. The `docs/architecture/` tree is well-established with 25+ subdirectories including `stations/`, `economy/`, `logistics/`, `ai_manager/`, `simulation/`, and `structures/`. The `docs/mission_profiles/` directory exists with 3 files. **The mapping is mostly ready but requires two new directory creations before executor can proceed.**

---

## 2. Prerequisites Checklist

### Directories that EXIST (ready to receive content):
- ✅ `docs/architecture/` — 25+ subdirectories present
- ✅ `docs/architecture/stations/` — 10 station docs already here
- ✅ `docs/architecture/economy/` — 13 economic docs (FISCAL_POLICY_AND_FEES.md, MARKET_OPERATIONS.md, etc.)
- ✅ `docs/architecture/logistics/` — logistics architecture docs present
- ✅ `docs/architecture/ai_manager/` — 28 AI manager docs (includes AI_MANAGER_ECONOMIC_LOGIC_UPDATE.md)
- ✅ `docs/architecture/simulation/` — 14 simulation system docs
- ✅ `docs/architecture/structures/` — exists but only contains README.md (empty slot for em_power_shield_tiers.md)
- ✅ `docs/mission_profiles/` — 3 profile files present

### Directories that DO NOT EXIST (need creation):
- ❌ `docs/systems/` — does not exist; needed for `em_power_shield_tiers.md` per unified task
- ❌ `docs/architecture/terrain/` — does not exist; needed for terrain generation content (section 7.5, ~120 lines)

### Files that need creation:
- `docs/systems/em_power_shield_tiers.md` (or `docs/architecture/stations/em_power_shield_tiers.md` — needs clarification from unified task)
- Any new economy/logistics files identified in the audit mapping

---

## 3. Pre-Work Tasks

1. **Create `docs/systems/` directory** — mkdir -p for the systems folder
2. **Create `docs/architecture/terrain/` directory** — mkdir -p for terrain content extraction
3. **Clarify target path for `em_power_shield_tiers.md`** — unified task references both `docs/architecture/stations/` and `docs/systems/`; need to confirm which is correct (structures/ might also be appropriate given it's about shield tech)
4. **Verify no conflicts in existing files** — audit mapping should check that target files don't already exist with conflicting content (e.g., `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` may already contain economic constants that would conflict with migrated content)

---

## 4. Risk Mitigation Summary

### Drift Risk: HIGH — CONFIRMED
- `docs/GUARDRAILS.md`: **680 lines** (project root)
- `agent-tasks/rules/GUARDRAILS.md`: **425 lines** (sync target)
- **Difference: 255 lines of drift** — the agent-tasks copy is significantly stale. This is not a minor desync; it's roughly 37% of the content that has diverged. The executor MUST sync this file as part of the migration, not just "verify" it.

### Economic Constants: SAFE
- All three constants (SCC Surcharge 0.5%, Broker Fee 0.3%, Sales Tax 3.37%) are present and aligned in `docs/GLOSSARY_SYSTEM_MECHANICS.md` at line 43.
- The audit confirms GUARDRAILS.md values match GLOSSARY values — no numeric drift detected.
- **Recommendation**: Add cross-reference link in trimmed GUARDRAILS.md pointing to GLOSSARY_SYSTEM_MECHANICS.md as canonical source.

### Market vs. Build Logic: LOCATED — Needs Careful Handling
- Primary location: `galaxy_game/app/models/structures/converted_base.rb` line 20 (`# SOURCING LOGIC (The "Market vs. Build" Core)`)
- Secondary references: deprecated task files in `docs/new_agent/projects/galaxy_game/tasks/deprecated/`
- **Risk**: This logic is embedded in a model file, not in docs. The migration must ensure the game-design rationale for this logic is preserved in the architecture docs even though the code itself lives in the app layer.

### Integration Risk: MEDIUM
- `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` likely already contains economic constants — migrating GUARDRAILS section 8 (Economic System Guardrails, ~130 lines) into this directory could create duplicates or conflicts.
- `docs/architecture/ai_manager/` has 28 existing docs — migrating AI Manager guardrails (sections 1-7, ~90 lines) needs careful merge strategy to avoid overwriting existing canonical definitions.

---

## 5. Blockers Identified

1. **Missing directories** (`docs/systems/`, `docs/architecture/terrain/`) — must be created before executor can write target files
2. **Path ambiguity for em_power_shield_tiers.md** — unified task references both `docs/architecture/stations/` and `docs/systems/`; need clarification
3. **agent-tasks/rules/GUARDRAILS.md is 255 lines out of sync** — this is a blocker because the executor needs to know which version is authoritative before making changes
4. **Potential content conflicts in economy/ and ai_manager/ directories** — existing files may already contain overlapping content that needs merge strategy

---

## 6. Recommended Action

**NOT ready for immediate executor handoff.** Pre-work must happen first:

### Phase 0 — Pre-Work (Strategist/Planner):
1. Create `docs/systems/` and `docs/architecture/terrain/` directories
2. Clarify target path for `em_power_shield_tiers.md` (stations vs systems vs structures)
3. Audit existing content in `docs/architecture/economy/` and `docs/architecture/ai_manager/` for overlap with GUARDRAILS sections being migrated
4. Determine if `agent-tasks/rules/GUARDRAILS.md` should be updated to match the 680-line root version, or if the root version is the one that needs trimming first

### Phase 1 — Executor (after pre-work):
1. Extract and migrate sections per audit mapping
2. Trim GUARDRAILS.md to ~150 lines of agent protocol only
3. Add migration index at top of trimmed file
4. Sync `agent-tasks/rules/GUARDRAILS.md` with trimmed version
5. Validate line counts and cross-references

---

## Appendix: Full docs/architecture/ Subdirectory Listing

```
adrs/          ai_manager/      asteroid_conversion_logic.md  biology/
concerns/      core/            economy/                      glossary/
intent/        isru/            logic/                        logistics/
lookup/        manufacturing/   operations/                   overview.md
patterns/      planning/        services/                     settlement/
simulation/    starsim/         stations/                     structures/
systems/ (NOTE: this is architecture/systems/, NOT docs/systems/)  terrasim/
units/         wormhole/
```

**Note**: `docs/architecture/systems/` EXISTS but `docs/systems/` (root-level) does NOT. The unified task references `docs/systems/` — confirm which was intended.
