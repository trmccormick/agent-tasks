---
name: "Surgical Guardrails Audit & Migration"
priority: HIGH
phase: 5
type: architecture/documentation
status: backlog
---

# TASK: Surgical Guardrails Audit & Migration

## Objective
Refactor the monolithic `docs/GUARDRAILS.md` (681 lines) into a modular, domain-specific documentation suite.

## Constraints & Requirements
1.  **Safety First**: The operational `GUARDRAILS.md` in the root (Agent Role Boundaries, Git, Docker, Database rules) MUST NOT be modified.
2.  **Canonical Truth**: All economic values (Taxes/Fees) must be verified against `GLOSSARY_SYSTEM_MECHANICS.md`.
3.  **No Data Loss**: Preserve all incident precedents, bug fix notes, and historical context; move them to the new architectural documents rather than deleting them.
4.  **Verification**: 
    * Target `docs/GUARDRAILS.md` for refactoring.
    * Target `docs/architecture/` and `docs/mission_profiles/` for new storage.

## Execution Workflow (Planner-to-Executor Handoff)
1.  **Audit Phase (Planner)**: Create a migration index (a mapping of where each section in the 681-line file will land).
2.  **Migration Phase (Executor)**:
    * Create `docs/architecture/` and `docs/mission_profiles/` directories.
    * Copy sections into the new files.
    * Create cross-reference links in the root `GUARDRAILS.md` pointing to the new locations.
3.  **Review Phase**: Stop and present the new structure to the user.
4.  **Cleanup Phase**: Trim the original 681-line file to <150 lines (Agent Protocol only) once verified.

## Validation Checklist
- [ ] New architecture documents created?
- [ ] New mission profile documents created?
- [ ] Economic variables confirmed against `GLOSSARY_SYSTEM_MECHANICS.md`?
- [ ] Cross-references updated?
- [ ] Operational Guardrails (root) maintained <150 lines?

## Commit Instructions
```bash
git add docs/architecture/ docs/mission_profiles/ docs/GUARDRAILS.md
git commit -m "docs: refactor guardrails for domain-specific organization"