# TASK: Purge Deprecated Manufacturing Services — Dead Code Removal

**Task ID:** `2026-08-08-LOW-REFACTOR-PURGE-DEPRECATED-MANUFACTURING-SERVICES`
**Date Created:** 2026-08-08
**Priority:** LOW
**Type:** refactor
**System Domain:** MANUFACTURING
**MVP Alignment:** AI_MANAGER_LUNA_SETTLEMENT
**Local Worker Safe:** true

---

## Context

Three Manufacturing services were confirmed dead (zero live callers in app/lib) during the 2026-08-07 verification session. Two are safe to purge; one requires a hold condition pending review of `deployment_refinement.md`.

### Confirmed Dead Code (Safe to Remove)

| Service | File Path | Live Callers | Status |
|---------|-----------|-------------|--------|
| `Manufacturing::ConstructionManager` | `galaxy_game/app/services/manufacturing/construction/construction_manager.rb` | **None** | ✅ Safe to delete |
| `Manufacturing::ProductionService` | `galaxy_game/app/services/manufacturing/production_service.rb` | **None** | ✅ Safe to delete |

### Hold Condition (Do NOT Remove Yet)

| Service | File Path | Live Callers | Status |
|---------|-----------|-------------|--------|
| `Manufacturing::AssemblyService` | `galaxy_game/app/services/manufacturing/assembly_service.rb` | **None** | ⚠️ HOLD — see below |

**Hold reason:** `deployment_refinement.md` references `Manufacturing::AssemblyService` 3 times as a planned integration target:
- Line 20: *"Replace Outdated References: Instead of `UnitModuleAssemblyService`, integrate with `Manufacturing::AssemblyService`."*
- Line 31: *"Medium Priority: Create deployment service that integrates with `Manufacturing::AssemblyService` job completion."*
- Line 45: *"Review current `Manufacturing::AssemblyService` for deployment hooks."*

**Action required before touching AssemblyService:** Read `deployment_refinement.md` and determine whether the integration plan is still current or superseded by later design decisions. If superseded, note it in the commit message and proceed with removal. If current, leave the file intact and report why.

---

## Execution Steps

### Step 1 — Verify Zero Callers (Pre-flight Check)

Run these inside Docker to confirm zero production callers:

```bash
# Inside web container
docker exec web bash -c 'grep -rn "ConstructionManager" /home/galaxy_game/app/ /home/galaxy_game/lib/ | grep -v "class ConstructionManager"'
docker exec web bash -c 'grep -rn "ProductionService" /home/galaxy_game/app/ /home/galaxy_game/lib/ | grep -v "class ProductionService"'
```

Both should return **no results** (excluding the class definition lines themselves). If any production caller is found, STOP and report it. Do not proceed with removal.

### Step 2 — Delete Dead Code Files

Delete these 4 files (service + spec for each):

```bash
# Inside web container
docker exec web bash -c 'rm /home/galaxy_game/app/services/manufacturing/construction/construction_manager.rb'
docker exec web bash -c 'rm /home/galaxy_game/spec/services/manufacturing/construction/construction_manager_spec.rb'
docker exec web bash -c 'rm /home/galaxy_game/app/services/manufacturing/production_service.rb'
docker exec web bash -c 'rm /home/galaxy_game/spec/services/manufacturing/production_service_spec.rb'
```

### Step 3 — Verify No Broken Imports

Run the manufacturing spec suite to confirm no broken imports:

```bash
cd /Users/tam0013/Documents/git/galaxyGame && docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/manufacturing/ --format progress' 2>&1 | tail -5
```

Report the result. If any failures, STOP and report them — do not attempt to fix.

### Step 4 — Review AssemblyService Hold Condition

Read `docs/developer/deployment_refinement.md` (on host):

```bash
grep -n "AssemblyService" /Users/tam0013/Documents/git/galaxyGame/docs/developer/deployment_refinement.md
```

Report the 3 references found. Determine:
- Is the integration plan still current? → Leave `Manufacturing::AssemblyService` intact, report why in your notes.
- Is it superseded? → Delete `assembly_service.rb` + its spec file too, note the supersession in commit message.

### Step 5 — Commit on Host (Not Inside Docker)

```bash
cd /Users/tam0013/Documents/git/galaxyGame && git add -A && git commit -m "refactor: purge dead Manufacturing services (zero live callers confirmed)

- Remove Manufacturing::ConstructionManager + spec (dead code)
- Remove Manufacturing::ProductionService + spec (dead code)
- AssemblyService: [kept/deleted — reason]"
```

---

## Stop Conditions

- **STOP** if any production caller is found for ConstructionManager or ProductionService — report the caller and stop.
- **STOP** if specs fail after deletion — report failures and stop. Do not attempt to fix.
- **DO NOT** modify any other files beyond the 4 dead-code files (plus AssemblyService only if hold condition is resolved).

---

## Output Format

Return a structured report:

```
Dead Code Purge Report
======================

Pre-flight verification:
  ConstructionManager callers: [none / list of callers]
  ProductionService callers: [none / list of callers]

Files deleted:
  - [file path]
  - [file path]
  - [file path]
  - [file path]

Manufacturing spec suite result: [X examples, Y failures]

AssemblyService hold condition:
  deployment_refinement.md references: [count]
  Integration plan status: [current / superseded — reason]
  Action taken: [kept / deleted]

Commit hash: [hash]
```

---

## References

- Verification session: 2026-08-08 (grep confirmed zero live callers)
- `deployment_refinement.md`: `/Users/tam0013/Documents/git/galaxyGame/docs/developer/deployment_refinement.md`
- `Manufacturing::ConstructionManager`: `galaxy_game/app/services/manufacturing/construction/construction_manager.rb`
- `Manufacturing::ProductionService`: `galaxy_game/app/services/manufacturing/production_service.rb`
- `Manufacturing::AssemblyService`: `galaxy_game/app/services/manufacturing/assembly_service.rb` (HOLD)

---

## Minimal Handoff Block

```
You are the Implementation Agent.

Project: galaxy_game
Task: Purge dead Manufacturing services (zero live callers confirmed)
Scope: Delete 4 files (2 services + specs), verify no broken imports, review AssemblyService hold condition
Do NOT fix any failures — report them and stop.
Do NOT modify any files beyond the dead-code targets.

Return structured report per task specification above.
```
