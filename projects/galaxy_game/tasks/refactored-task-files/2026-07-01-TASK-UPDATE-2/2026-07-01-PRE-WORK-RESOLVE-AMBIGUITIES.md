---
name: "Pre-Work: Resolve Path Ambiguities & Drift Issues"
priority: HIGH
phase: 5
type: infrastructure
status: backlog
created: 2026-07-01
---

# PRE-WORK TASK: Resolve Critical Ambiguities Before Executor

**Purpose**: Address 4 critical decision points that block clean executor handoff  
**Blocker**: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` needs these decisions  

---

## Issues to Resolve

### 1. em_power_shield_tiers.md Target Path Ambiguity

**Problem**: Unified task references two possible target locations:
- `docs/systems/em_power_shield_tiers.md`
- `docs/architecture/stations/em_power_shield_tiers.md`
- Possibly: `docs/architecture/structures/em_power_shield_tiers.md`

**Decision Needed**: Which is the correct target?

**Context**:
- EM Power Shield is a station defense technology (suggests `stations/`)
- Alternatively, if it's a system architecture component (suggests `systems/` or `architecture/systems/`)
- `docs/architecture/structures/` exists but is mostly empty (only README.md)

**Recommendation from Qwen**: Clarify this in unified task before executor starts

**Action**:
1. Review GUARDRAILS.md section where this is mentioned (EM Power Transition & Shield Tech, ~35 lines)
2. Determine correct canonical location
3. Update unified task acceptance criteria with explicit file path

---

### 2. agent-tasks/rules/GUARDRAILS.md Drift (255 lines)

**Problem**: Version mismatch discovered by Qwen:
- Project root: `docs/GUARDRAILS.md` = 680 lines
- Sync target: `agent-tasks/rules/GUARDRAILS.md` = 425 lines
- **Difference**: 255 lines (37% divergence)

**Decision Needed**: Which version is authoritative? How do we reconcile?

**Options**:
- **Option A**: Update `agent-tasks/rules/GUARDRAILS.md` to match root version before migration starts
- **Option B**: Migrate at root level first, then resync agent-tasks copy afterward
- **Option C**: Both repos have diverged; audit both before any executor work

**Recommendation**: Option A or C — Need to know which is canonical before executor trims the 680-line file

**Action**:
1. Compare both files line-by-line (check git history to understand when they diverged)
2. Decide: Is agent-tasks copy a subset (intentional) or a stale copy?
3. Update unified task with sync strategy

---

### 3. Content Conflict Prevention (economy/ & ai_manager/ directories)

**Problem**: Existing docs already exist in target directories:
- `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` — may already contain economic constants
- `docs/architecture/ai_manager/` — has 28 existing docs; migrating sections 1-7 (~90 lines) could overwrite

**Decision Needed**: How to merge without duplication?

**Options**:
- **Option A**: Conduct overlap audit before executor (check what's already there)
- **Option B**: Executor does merge manually (risky, requires deep knowledge)
- **Option C**: Create new consolidated docs (e.g., `economic_guardrails.md` instead of merging into FISCAL_POLICY_AND_FEES.md)

**Recommendation**: Conduct overlap audit before executor starts

**Action**:
1. Read `docs/architecture/economy/FISCAL_POLICY_AND_FEES.md` to see if it already contains SCC/Broker/Sales Tax
2. List all 28 AI manager docs to understand what sections already exist
3. Flag any content that will conflict with migrated sections
4. Create merge strategy in unified task

---

### 4. Market vs. Build Logic Archival

**Problem**: Core game logic is embedded in code, not docs:
- Location: `galaxy_game/app/models/structures/converted_base.rb` line 20
- Comment: `# SOURCING LOGIC (The "Market vs. Build" Core)`
- When GUARDRAILS.md is trimmed, this game-design rationale needs preservation

**Decision Needed**: Where should this design rationale live?

**Options**:
- **Option A**: Document it in `docs/architecture/economy/` (e.g., new file `market_vs_build_logic.md`)
- **Option B**: Add it to `docs/architecture/structures/em_power_shield_tiers.md` or related structure doc
- **Option C**: Create `docs/architecture/gameplay/sourcing_logic.md`

**Recommendation**: Add to economy docs (it's fundamentally about resource acquisition strategy)

**Action**:
1. Extract the "Market vs. Build" rationale from GUARDRAILS.md
2. Create proper documentation in target architecture folder
3. Link from trimmed GUARDRAILS.md (cross-reference only)

---

## Success Criteria

All 4 questions answered and documented:
- [ ] **em_power_shield_tiers.md**: Target path decided and unified task updated
- [ ] **agent-tasks GUARDRAILS.md**: Drift strategy decided (reconcile or resync)
- [ ] **Content conflicts**: Overlap audit completed; merge strategy documented
- [ ] **Market vs. Build logic**: New home decided; migration plan created

---

## Owner

**Claude or Strategist** — These are strategic decisions, not code changes. Needs human judgment to decide.

---

## Related Tasks

- Blocker: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` (awaiting decisions)
- Validation: `2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md` (identified all 4 issues)
- Pre-work: `2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md` (also needed before executor)
