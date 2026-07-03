# TASK: Surgical Guardrails Audit & Migration (Unified V2)
**Date**: 2026-07-01
**Status**: Ready for Executor/Qwen Parallel Review

## 1. Reconciliation Summary
The stale task provides a high-level goal, while the recent audit provides the granular "surgical" map and economic validation required to safely execute the split[cite: 5]. This unified task supersedes the stale task by correcting outdated directory targets, addressing fragmented constants, and introducing a formal migration index to maintain document integrity during the refactor[cite: 5].

## 2. Refined Acceptance Criteria
- [ ] **Modularity**: Domain-specific logic moved from `docs/GUARDRAILS.md` to `docs/architecture/` and `docs/mission_profiles/` according to the section-to-folder mapping[cite: 5].
- [ ] **Economic Integrity**: All constants validated against `GLOSSARY_SYSTEM_MECHANICS.md` as the sole canonical source of truth[cite: 5].
- [ ] **Operational Core**: `docs/GUARDRAILS.md` trimmed to ~150 lines, retaining only agent-operational rules (Docker/Process/Database)[cite: 5].
- [ ] **New Documentation**: Creation of `docs/architecture/stations/em_power_shield_tiers.md` and others as identified in the audit mapping[cite: 5].
- [ ] **Index Consistency**: Migration index and cross-references established at the top of the trimmed `GUARDRAILS.md` to prevent broken links[cite: 5].
- [ ] **Data Preservation**: Historical precedents, bug fixes, and incident notes moved to target architecture docs; nothing deleted without an archival destination[cite: 5].

## 3. Refined Execution Plan
1. **Audit & Preparation**: Cross-reference all percentages against `GLOSSARY_SYSTEM_MECHANICS.md`; create all new target files in `docs/architecture/` and `docs/mission_profiles/`.
2. **Extraction**: Migrate mapped sections into the new files; merge content without overwriting existing canonical definitions.
3. **Trim & Index**: Remove extracted sections from `docs/GUARDRAILS.md`; prepend the migration index for future agent navigation.
4. **Validation & Sync**: Perform checksum/line count validation; sync operational rules with `agent-tasks/rules/GUARDRAILS.md`[cite: 5].

## 4. Issues Identified
- **Fragmented Truth**: Economic constants (SCC, Broker, Sales Tax) are currently defined in three locations; audit confirms `GLOSSARY_SYSTEM_MECHANICS.md` as the required canonical source[cite: 5].
- **Formatting Debt**: Duplicate section numbering (13 appears twice) and a missing Section 4 create navigation friction[cite: 5].
- **Stale Context**: Includes time-bound implementation notes (e.g., "Sabatier Bug Fix [2026-01-15]") that belong in commit history or permanent docs, not rules[cite: 5].
- **Mixed Concerns**: High-level game design, architecture, and low-level agent operational rules are currently conflated in one file[cite: 5].

## 5. Risk Flags
- **Drift Risk**: Risk of agent-tasks/rules/GUARDRAILS.md becoming desynchronized from the project root[cite: 5].
- **Dependency**: Architecture docs must be verified for accuracy before deleting source content from the monolithic GUARDRAILS.md[cite: 5].
- **Integration**: The "Market vs. Build" logic and other saved game-dev notes must be verified against the new `docs/architecture/economy/` structure to ensure no loss of game-dev logic.

## 6. Recommended Priority: HIGH
**Justification**: The fragmentation of economic constants across three files is a high-risk factor for simulation errors; cleaning the agent protocols is also critical for long-term maintainability[cite: 5].

## 7. Next Step Recommendation
This is ready for parallel review with the Qwen agent to verify the automated path/mapping, followed by direct handoff to the Executor[cite: 1].
