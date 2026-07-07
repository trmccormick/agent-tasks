---
status: active
priority: HIGH
type: implementation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
date_created: 2026-07-06
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/design/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md

LIFECYCLE: backlog → active → completed (use git mv, never copy)
READ FIRST: Task file contains all prerequisites and implementation details.
```

---

# PROFILES_V2 CANONICAL IMPLEMENTATION — LUNA MIGRATION ONLY

**Status**: ACTIVE
**Priority**: HIGH
**Type**: implementation
**Created**: 2026-07-06
**Approved by**: Claude (design review complete)
**Canonical examples location**: `data/json-data/missions_v2/`

---

## Context

Claude reviewed the canonical V2 examples and approved them as a reference implementation. This task covers **only** the Luna migration — creating production files in `missions_v2/`, updating the rake with path resolution, and verifying 17 tasks still hold.

### What Exists (Canonical Examples)

| File | Path | Status |
|------|------|--------|
| Profile schema reference | `missions_v2/README.md` | ✅ Created |
| Luna profile | `missions_v2/profiles/luna_base_profile_v2.json` | ✅ Created |
| Phase 1: power_comms | `missions_v2/phases/power_comms_v2.json` | ✅ Created |
| Phase 2: isru_deployment | `missions_v2/phases/isru_deployment_v2.json` | ✅ Created |
| Phase 3: gas_processing | `missions_v2/phases/gas_processing_v2.json` | ✅ Created |
| Phase 4: robot_logistics | `missions_v2/phases/robot_logistics_v2.json` | ✅ Created |
| Mission plan (DAG) | `missions_v2/mission_plans/luna_precursor_mission_plan_v2.json` | ✅ Created |
| Task index | `missions_v2/task_index/all_tasks.json` | ✅ Created |
| 17 V2.1 tasks | `missions_v2/tasks/*_v2.json` | ✅ Copied + converted |

### Claude's Scope Constraints (MANDATORY)

**IN SCOPE for this task:**
1. Create production files in `missions_v2/` based on the approved canonical examples
2. Update the rake with `missions_v2/` path resolution and legacy fallback
3. Verify 17 tasks still hold (no regressions)

**NOT IN SCOPE — separate tasks later:**
- Engine changes to `TaskExecutionEngineV2`
- Parameter interpolation (`$variable` resolution)
- LegacyPortAdapter bridge integration
- Venus foundry migration
- DAG execution engine changes

---

## Implementation Steps

### Step 1: Verify Canonical Examples Are Complete

Confirm all files in `missions_v2/` are present and valid JSON. Check that the 17 V2.1 tasks have `"template": "task_v2.1"` and `"version": "2.1"`.

### Step 2: Update luna_mission.rake with missions_v2/ Path Resolution

Add `missions_v2/` path resolution to `galaxy_game/lib/tasks/luna_mission.rake`:

```ruby
# In the rake task, change profile path resolution:
profile_path = if File.exist?(v2_profile_path)
  v2_profile_path
elsif File.exist?(legacy_profile_path)
  legacy_profile_path
else
  nil
end

# Add missions_v2/ to phase path resolution:
phase_path = GalaxyGame::Paths::MISSIONS_PATH.join("missions_v2/phases/#{phase_id}.json").to_s
```

### Step 3: Verify No Regressions

Run the existing rake task and confirm all 17 tasks still execute correctly. No engine changes needed — this is purely a path resolution update.

---

## Stop Conditions

- If any canonical file is missing or malformed — document and stop
- If rake has breaking changes needed beyond path resolution — escalate before proceeding

---

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files created/modified**: [count]
**Output location**: `data/json-data/missions_v2/` + rake updates

### What Needs Implementation

1. **Engine integration** — TaskExecutionEngineV2 must resolve profiles_v2 paths
2. **Rake task update** — luna_mission.rake must load from profiles_v2
3. **LegacyPortAdapter bridge** — Reference in registry phases that need it
4. **DAG execution** — Phase dependency resolution in mission plans

---

## Implementation Steps

### Step 1: Engine Integration (TaskExecutionEngineV2)

Add profile_v2 path resolution to `galaxy_game/app/services/ai_manager/task_execution_engine_v2.rb`:

```ruby
# Add to initialize method — resolve profiles_v2 paths
@profile_path = case manifest_or_path
                when String
                  # Try profiles_v2 first, then legacy paths
                  v2_candidate = GalaxyGame::Paths::MISSIONS_PATH.join("profiles_v2/profiles/#{manifest_or_path}")
                  legacy_candidate = GalaxyGame::Paths::MISSIONS_PATH.join(manifest_or_path)
                  
                  if File.exist?(v2_candidate)
                    v2_candidate
                  elsif File.exist?(legacy_candidate)
                    legacy_candidate
                  else
                    nil
                  end
                when Hash
                  manifest_or_path
                else
                  {}
                end
```

### Step 2: Phase Path Resolution Update

Update `load_phase_tasks` in TaskExecutionEngineV2 to resolve phase files from profiles_v2/phases/:

```ruby
def load_phase_tasks(task_list_file)
  # Try profiles_v2 path first
  v2_path = GalaxyGame::Paths::MISSIONS_PATH.join("profiles_v2/phases/#{task_list_file}").to_s
  return JSON.parse(File.read(v2_path)) if File.exist?(v2_path)
  
  # Fallback to legacy paths
  legacy_paths = [
    GalaxyGame::Paths::MISSIONS_PATH.join(task_list_file).to_s,
    GalaxyGame::Paths::MISSIONS_PATH.join("luna_base_establishment/#{task_list_file}").to_s
  ]
  
  legacy_paths.each do |path|
    return JSON.parse(File.read(path)) if File.exist?(path)
  end
  
  []
end
```

### Step 3: DAG Execution in Mission Plans

Add DAG-based phase ordering to the rake task. The mission plan already has `dag_execution_order` and `dependencies` fields — the engine needs to evaluate them:

```ruby
def resolve_phase_dependencies(mission_plan)
  phases = mission_plan["phases"]
  ordered = []
  visited = Set.new
  
  phases.each do |phase_def|
    visit_phase(phase_def, phases, ordered, visited)
  end
  
  ordered
end

def visit_phase(phase_def, all_phases, ordered, visited)
  return if visited.include?(phase_def["phase_id"])
  
  visited.add(phase_def["phase_id"])
  
  phase_def["dependencies"]&.each do |dep_id|
    dep = all_phases.find { |p| p["phase_id"] == dep_id }
    visit_phase(dep, all_phases, ordered, visited) if dep
  end
  
  ordered << phase_def
end
```

### Step 4: LegacyPortAdapter Bridge Integration

For phases that reference legacy port schemas (gas_processing_v2.json has `bridge_config.use_legacy_adapter: true`), the engine should invoke the adapter before executing effects:

```ruby
def apply_bridge_config(phase_data, effects)
  bridge = phase_data.dig("metadata", "bridge_config")
  return effects unless bridge && bridge["use_legacy_adapter"]
  
  adapter = Lookup::LegacyPortAdapter.new
  
  effects.map do |effect|
    next effect unless effect["action"] == "register_to_bus"
    
    # Project legacy port schema to bus topology
    unit_blueprint = find_blueprint(effect["unit"])
    resolved = adapter.resolve_port_schema(unit_blueprint)
    
    if resolved[:schema_version] == "legacy_flat"
      # Map legacy ports to bus connections
      effect["ports"] = map_legacy_to_bus(resolved[:ports_hash], effect["ports"])
    end
    
    effect
  end
end
```

### Step 5: Rake Task Update

Update `luna_mission.rake` to load from profiles_v2:

```ruby
# In the rake task, change profile path resolution:
profile_path = if File.exist?(v2_profile_path)
  v2_profile_path
elsif File.exist?(legacy_profile_path)
  legacy_profile_path
else
  nil
end

# Add DAG execution order from mission plan:
if File.exist?(mission_plan_path)
  mission_plan = JSON.parse(File.read(mission_plan_path))
  execution_order = mission_plan["dag_execution_order"] || ["power_comms", "isru_deployment", "gas_processing", "robot_logistics"]
end
```

---

## Schema Compliance Checklist

Before committing, verify all canonical files pass these checks:

- [ ] All profiles have `template`, `version`, `profile_id`, `mission_plan_ref`
- [ ] All phases have `phase_id`, `name`, `description`, `applicable_body_types`, `documentation_url`
- [ ] All mission plans have `dag_execution_order`, `dependencies`, `critical_path`
- [ ] All files have `documentation_url` pointing to wiki placeholder
- [ ] Venus migration study preserves all V1 economic/parallel/AI data
- [ ] Task index maps all 16 Luna tasks to their phases
- [ ] No schema drift between templates and examples

---

## Stop Conditions

- If any canonical file is missing or malformed — document and stop
- If LegacyPortAdapter API doesn't match expected interface — document and stop
- If rake task has breaking changes needed — escalate before proceeding

---

**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Files created/modified**: [count]
**Output location**: `data/json-data/missions/profiles_v2/` + engine updates
