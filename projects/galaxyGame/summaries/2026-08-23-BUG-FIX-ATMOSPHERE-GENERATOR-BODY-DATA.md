# Synthesis Report: AtmosphereGeneratorService @body_data nil/wrong

**Date:** 2026-08-23
**Task:** MEDIUM-BUG-FIX-ATMOSPHERE-GENERATOR-BODY-DATA-NIL
**Status:** Investigation Phase

## Summary of Findings

### Purpose
Investigate and fix `AtmosphereGeneratorService#generate_composition_for_body` failure when `@body_data` is nil or wrong outside the normal full-system generation flow.

### Key Areas to Investigate
1. **AtmosphereGeneratorService** — examine `generate_composition_for_body` method, how `@body_data` is set, and what guards exist
2. **SystemBuilderService** — understand the standard invocation order that sets `@body_data` correctly
3. **procedural_generator_magnetosphere_spec.rb** — find the workaround that was applied to avoid triggering this code path
4. **Other callers** — check if any live code paths call `generate_composition_for_body` without proper setup

### Scope Boundaries
- **In Scope**: Investigation + fix for @body_data nil/wrong issue in AtmosphereGeneratorService
- **Out of Scope**: Any unrelated refactoring, new features, or changes to other services

### Dependencies
- `app/services/star_sim/atmosphere_generator_service.rb` — primary target
- `spec/services/star_sim/procedural_generator_magnetosphere_spec.rb` — where bug was discovered
- `app/services/star_sim/system_builder_service.rb` — standard invocation flow
- Related task: 2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md

### Risks & Considerations
- **Shared-code rule**: If AtmosphereGeneratorService is called from many places, need Synthesis Report escalation before commit
- **Test-only vs live bug**: Must confirm whether this manifests in normal game flow or only in tests
- **Defensive guard vs caller fix**: Fix approach depends on root cause — service should probably have a guard regardless

### Next Steps
1. Read AtmosphereGeneratorService source code
2. Check callers of generate_composition_for_body across the codebase
3. Reproduce the failure by running relevant specs
4. Determine root cause (test setup vs live caller ordering)
5. Apply fix at correct layer
6. Run full AtmosphereGeneratorService spec suite to confirm no regressions
