---
task_id: 2026-08-20-VERIFICATION-BLUEPRINT-AND-ARCHITECTURE-LOCKDOWN
type: VERIFICATION
status: backlog
priority: high
created: 2026-08-20
session_generated_by: Claude
---

# Verification: Blueprint Loading + Architecture Lockdown

**Purpose:** Close the loop on three design sessions' worth of changes. Verify JSON blueprints load correctly, PHASE_STRUCTURE.md current state is stable, and material dependencies resolve end-to-end.

**Why this matters:** Session produced 10+ new/edited JSON files + PHASE_STRUCTURE.md updates + graphene_composite dependency chain. Each shipped before verification. This is a "trust but verify" cleanup, not a new blocker.

---

## Verification 1: PHASE_STRUCTURE.md Current State (Plain Read)

**Instruction:** Read the current file and verify the Foundation phase mk2 unlock is documented correctly.

**File:** `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md`

**Verification checklist:**

- [ ] **Foundation Phases (1-4) section** explicitly states mk2 cooling technology unlocked
  - Should mention: "mk2 technology becomes available during Phases 1-4"
  - Should explain: Early HLT harvesters equipped with mk2 before Phase 0 launch
  - Should NOT say: "mk2 available Phase 11" or "mk2 research Phase 9-10"

- [ ] **Phase 0 section** mentions HLT harvesters launch with mk2 cooling equipped
  - Should mention: "Venus (Month 5) and Titan (Month 18) harvesters equipped with mk2 storage"
  - Should explain: mk2 cooling (0.15% daily) prevents boil-off losses during 8-10 month transit

- [ ] **Phase 10 (Venus operations)** clarifies early harvester returns with mk2-cooled cargo
  - Should distinguish: Mobile HLT harvesters (Phase 0, return Phase 10) vs. stationary skimmers (Phase 11+)
  - Should mention: Early harvester returns have mk2 cooling already built-in; no boil-off code needed yet

- [ ] **Phase 11+ (Cycler/Skimmer activation)** mentions stationary skimmers (different craft type)
  - Should distinguish: Stationary skimmers are permanently deployed (not mobile like HLT)
  - Should mention: Boil-off enforcement activates on cycler return transits

**Success criteria:** 
- No contradictions between Foundation phase mk2 unlock and Phase 0 HLT launch
- Clear distinction between mobile harvester lifecycle and stationary skimmer infrastructure
- Boil-off enforcement timing is clear (Phase 10 = early harvesters already cooled, Phase 11+ = cycler transits enforced)

**If issues found:** Document specific lines/sections that are incorrect or contradictory. Do not edit; report findings.

---

## Verification 2: JSON Blueprint Load Testing

**Instruction:** Confirm 10+ modified/new blueprint JSON files actually parse and load through the blueprint loader without errors.

**Files to test:**
```
/data/json-data/blueprints/units/storage/
  - multi_purpose_cryogenic_storage_tank_mk2_bp.json
  - multi_purpose_cryogenic_storage_tank_mk3_bp.json
  - methane_storage_tank_mk2_bp.json
  - methane_storage_tank_mk3_bp.json
  - lox_storage_tank_mk2_bp.json
  - lox_storage_tank_mk3_bp.json

/data/json-data/blueprints/materials/
  - graphene_composite_bp.json

/data/json-data/blueprints/crafts/space/spacecraft/
  - heavy_lift_transport_mk2_bp.json
  - heavy_lift_transport_mk3_bp.json
  - (also verify HLT mk1 still loads)

/data/json-data/operational_data/crafts/space/spacecraft/
  - heavy_lift_transport_harvester_venus_data.json
  - heavy_lift_transport_harvester_titan_data.json
```

**Test procedure:**

1. **Identify the blueprint loader service**
   - Search for: `BlueprintLoaderService`, `BlueprintValidator`, or similar
   - Check: How does the system load JSON blueprints into the database?

2. **Write a test that loads each file**
   ```ruby
   # Pseudocode structure
   blueprint_files = [
     "/data/json-data/blueprints/units/storage/multi_purpose_cryogenic_storage_tank_mk2_bp.json",
     # ... etc
   ]
   
   blueprint_files.each do |file_path|
     json_data = JSON.parse(File.read(file_path))
     result = BlueprintLoaderService.load(json_data)
     puts "#{file_path}: #{result.success? ? 'PASS' : 'FAIL'}"
     puts "  Error: #{result.error}" if result.error.present?
   end
   ```

3. **Verify each file:**
   - [ ] JSON parses without syntax errors
   - [ ] Blueprint loader accepts the schema
   - [ ] No missing required fields
   - [ ] No references to non-existent materials/components (see Verification 3)
   - [ ] mk1/mk2/mk3 distinction is valid (if applicable)

**Success criteria:**
- All 11 files parse JSON without error
- All files pass blueprint loader validation
- No schema errors or missing fields
- mk2/mk3 variants are syntactically valid vs mk1

**If issues found:** Document which files fail, with specific error messages. Do not edit; report findings.

---

## Verification 3: Material Dependency Chain (Graphene Composite)

**Instruction:** Verify graphene_composite production blueprint doesn't introduce new ungrounded dependencies.

**File to check:** `/data/json-data/blueprints/materials/graphene_composite_bp.json`

**Verification checklist:**

- [ ] **Input: graphite**
  - Verify `graphite` exists as a game material in the materials list
  - Check: Is graphite extractable (mining blueprint exists)?
  - Should be: Phase 10+ availability (asteroid/crustal mining)

- [ ] **Input: epoxy_resin**
  - Verify `epoxy_resin` exists as a game material in the materials list
  - Check: Is epoxy_resin sourceable (Earth import or production blueprint)?
  - Should be: Earth-importable (synthetic chemistry)
  - **CRITICAL:** If `epoxy_resin` doesn't exist, this repeats the exact mistake just caught

- [ ] **Output: graphene_composite**
  - Check: Is graphene_composite referenced in mk2/mk3 storage blueprint files?
  - Should appear in: `multi_purpose_cryogenic_storage_tank_mk2_bp.json` + others
  - Verify: Material quantities match (does production output match consumption in tanks?)

- [ ] **Facility requirement: fabrication_plant**
  - Verify: Fabrication plant blueprint exists
  - Check: Is it constructible before Phase 11?
  - Should be: Available Phase 11 at latest (when mk2/mk3 storage needed for skimmers)

- [ ] **Cross-reference validation**
  - Run grep across all storage blueprints to confirm they reference `graphene_composite`
  - Count: How many units of graphene_composite per mk2 tank? (should be ~250 kg per storage tank)
  - Verify: Production rate (8 hours, 10 kg per cycle) matches demand during Phase 11 skimmer build-out

**Material resolution chain:**
```
graphite (extracted Phase 10+) 
  + epoxy_resin (imported from Earth) 
  → graphene_composite (fabrication_plant, Phase 11+)
  → mk2/mk3 storage tanks (multiple units)
  → stationary skimmer infrastructure (Phase 11+)
```

**Success criteria:**
- graphite exists and is extractable
- epoxy_resin exists and is Earth-importable
- fabrication_plant exists and is constructible
- All three materials reference each other consistently
- No circular dependencies
- No unmakeable components in the chain

**If issues found:** 
- If epoxy_resin doesn't exist: Stop verification, report as blocker. This repeats the exact problem just fixed.
- If other issues found: Document specific files and missing dependencies. Do not edit; report findings.

---

## Verification 4: Git Hygiene Check

**Instruction:** Confirm that blueprint JSON changes were NOT committed to git (they should remain in `/data/`, which is gitignored).

**Check:**

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git log --oneline --all -20 | grep -i "blueprint\|storage\|graphene\|mk2\|mk3" || echo "No blueprint commits found (correct)"
```

- [ ] **Expect:** No commits mentioning blueprints or JSON data files
- [ ] **If found:** Verify commits only touch `.md` documentation and PHASE_STRUCTURE.md, not `/data/` files

**Success criteria:**
- No JSON blueprint files accidentally committed to galaxyGame repo
- PHASE_STRUCTURE.md and documentation commits are OK
- `/data/json-data/` remains untracked (in .gitignore)

---

## Final Signoff

**When all verifications pass, update this task:**
- [ ] Change status to `completed`
- [ ] Add summary section: "All three design chains verified: PHASE_STRUCTURE.md current state stable, JSON blueprints load correctly, graphene_composite dependency chain resolves to existing materials."
- [ ] Commit task file with results

**If any verification fails:** Create a separate LOW-priority task for the specific issue (e.g., "Fix epoxy_resin dependency" or "Correct PHASE_STRUCTURE.md boil-off timing").

---

## Related Work (Not Blocking)

These verifications assume the following already exist (from prior sessions):
- mk1 storage blueprints and HLT mk1 operational data are correct and tested
- Material list includes standard game resources (not just storage tanks)
- Blueprint loader service exists and works

If any of these assumptions are wrong, report separately.
