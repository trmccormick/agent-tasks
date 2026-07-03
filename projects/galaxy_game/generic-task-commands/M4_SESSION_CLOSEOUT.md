# M4 Session Closeout Guidance

When Phase 4 implementation is complete, follow these steps:

## Step 1: Move Task File to Completed
```bash
cd /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/
mv active/2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md \
   completed/2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md
```

## Step 2: Update Task File Status
Open the file you just moved. Change the YAML header from:
```
status: active
```
to:
```
status: completed
```

## Step 3: Commit Task Move
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git add completed/2026-06-22-HIGH-IMPLEMENTATION-PHASE4-ROBOT-LOGISTICS-SITE-HARDENING.md
git commit -m "task: Complete Phase 4 Robot Logistics implementation

- Created phase_4_robot_logistics.json with 4 tasks
- Wired Phase 4 into luna_settlement_profile_v1.json
- Validated final rake: 12/12 Phase tasks passing
- Settlement ready for Phase 5 (Lava Tube Construction)"
git push origin main
```

## Step 4: Update Project Status.md
Navigate to: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md`

Add to the **Completed Work** section:
```
### Phase 4 Implementation (2026-06-22)
- Created phase_4_robot_logistics.json with 4 tasks (deploy_robot_charging_port, car_300_charge_cycle, isru_stockpile_initiation, construction_zone_leveling)
- Integrated Phase 4 into luna_settlement_profile_v1.json
- Final rake validation: 12/12 Phase tasks passing (3+2+3+4)
- All task effects executing cleanly; no blueprint/port errors
- Settlement state validated for Phase 5 readiness
```

Also update the **Last Updated** timestamp and **Current Status** section.

## Step 5: Commit Status.md
```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add docs/new_agent/projects/galaxy_game/status.md
git commit -m "docs: Update status.md — Phase 4 implementation complete"
git push origin main
```

## Step 6: Report to Strategist
Provide brief summary:
- ✅ Phase 4 fully implemented and validated
- 📋 Rake shows 12/12 Phase tasks passing
- 🎯 Ready for Phase 5 (Lava Tube Construction)
- ⚠️ Any blockers or deferred items (if applicable)

---

**Do NOT skip these steps.** Task completion workflow ensures continuity for next session.
