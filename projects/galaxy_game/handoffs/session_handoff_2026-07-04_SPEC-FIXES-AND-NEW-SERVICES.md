# Session Handoff — Galaxy Game — 2026-07-04

**Role**: Planning/Strategist (Claude web)
**Previous Session**: session_handoff_2026-07-01_PHASE_C_AND_LUNA_ISRU_DESIGN.md
**Next Session Should Focus On**: Suite baseline confirmation + Legacy agent repo cleanup

---

## Summary

Long session. Hub Bus Routing and Sabatier Reactor completed and committed.
Three spec regression fixes landed. Two new services added (SurfaceSuitabilityAnalyzer,
register_to_bus engine action). GUARDRAILS consolidation work underway on Ryzen
(separate session). Full suite run completed 2026-07-04.

---

## Completed This Session

| Date | Item | Commit |
|---|---|---|
| 2026-07-03 | Hub Bus Routing — register_to_bus engine action | `bd549c15` |
| 2026-07-03 | Sabatier Reactor JSON files + methane.json update | `1daf2ab4` |
| 2026-07-03 | strategy_selector.rb duplicate method removed | `7ea5bd44` |
| 2026-07-03 | SurfaceSuitabilityAnalyzer Phase 1 service | `a2c6a680` |
| 2026-07-03 | cell_num NameError fixed in SurfaceSuitabilityAnalyzer | `e59cce02` |
| 2026-07-03 | sabatier_reactor_spec.rb paths fixed, redundant tests removed | committed |
| 2026-07-03 | escalation_integration_spec.rb O2/H2O formula fix | committed |

---

## Current Suite Baseline

**4097 examples, 8 failures** — run 2026-07-04

| Failure | Status |
|---|---|
| `escalation_service_spec.rb:429` | Pre-existing tracked — do not touch |
| `surface_suitability_analyzer_spec.rb:71` | Accepted — slope calc out of scope for Phase 1 |
| `surface_suitability_analyzer_spec.rb:152 + :157` | Accepted — atmosphere attribute, wrong schema |
| `orbital_shipyard_service_spec.rb:129` | **NEEDS CLASSIFICATION** — not previously on flaky list |
| `game_data_generator_spec.rb:13` | Confirmed flaky — do not touch |
| `material_lookup_service_spec.rb:251` | Confirmed flaky — do not touch |
| `procedural_generator_spec.rb:144` | Status unclear — was removed from flaky list 2026-06-05, now failing again |

**First task next session**: Run targeted spec on `orbital_shipyard_service_spec.rb:129`
to confirm flaky vs regression before any other work proceeds.

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec \
  spec/services/construction/orbital_shipyard_service_spec.rb:129 \
  --format documentation 2>&1' | tail -20
```

Run it 2-3 times. If it passes at least once it is flaky. If it fails every
time it is a regression and needs a task file.

Same for `procedural_generator_spec.rb:144`:

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec \
  spec/services/star_sim/procedural_generator_spec.rb:144 \
  --format documentation 2>&1' | tail -20
```

---

## Active Task Files (agent-tasks repo)

Two task files still in active — need follow-up:

1. `2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md`
   - GUARDRAILS consolidation work in progress on Ryzen (separate session)
   - Do not dispatch until Ryzen session produces output and it is reviewed

2. `2026-07-03-MEDIUM-DOCUMENTATION-CLEANUP-LEGACY-AGENT-REPO.md`
   - Not yet dispatched to any agent
   - Low priority — do after suite baseline is confirmed clean

---

## Recurring Agent Behavior Problem — git mv

Agents repeatedly copy task files instead of using git mv, leaving
stale copies in active/. This happened 4+ times this session.

GUARDRAILS rule drafted (pass to Ryzen session for consolidation):

> Rule [N] — Task file moves must use git mv, never copy-and-delete.
> After move, agent must run:
>   find agent-tasks/projects/galaxy_game/tasks -name "[FILENAME].md"
> Only one result should exist — the completed path.
> Agent must paste this output before committing.

---

## Parked / Carry Forward

- `surface_suitability_analyzer_spec.rb:71` — buildability :too_steep
  for flat terrain — slope calculation logic bug in service, needs own
  task file before Phase 2 work
- `surface_suitability_analyzer_spec.rb:152 + :157` — Atmosphere model
  does not have `name` attribute — spec needs to use correct attribute
  or be removed; needs own task file
- `sabatier_reactor_spec.rb` — `has_facility?` not implemented on
  BaseSettlement — follow-up task needed when has_facility? is built
- `schedule_harvester_completion` — pre-existing spec fix, task file
  still not drafted (LOW/MEDIUM priority)
- Luna Precursor V2 rake — post-patch run confirmed clean 13/13
- Non-idempotent rake runs — small fix to luna_mission.rake setup block,
  undrafted
- No-Magic Robot Deployment — task file ready to dispatch when capacity
  available

---

## Key Architecture Decisions Locked This Session

- register_to_bus is the hub connection model (not connect_units)
- connect_units remains — do not remove
- Chemical formula convention confirmed: O2, H2O, CH4, N2 in all Ruby
  backend code; display names are UI/JSON layer only
- SurfaceSuitabilityAnalyzer Phase 1: elevation, resource_grid, biomes
  only; no lat/lon mapping, no crater data (Phase 2)
- Craters are geological features, not biomes
- Sabatier Reactor JSON files are world-agnostic (no Luna hardcoding)

---

## GUARDRAILS Consolidation — Ryzen Session Open Items

The Ryzen session closed with the following unresolved items — do not
assume the consolidation is complete until these are verified:

1. **Line count arithmetic never reconciled** — audit report claimed
   773 vs 680 lines. Likely loose estimates, not real data loss, but
   no `wc -l` sum across all destination files was ever run to confirm.
   Run this before trusting the consolidation as complete.

2. **No independent diff of docs/GUARDRAILS.md** — commit message and
   audit report claim completeness but it was self-certified. No one
   did a section-by-section diff against the migration index. Needs
   independent verification before the consolidation task is closed.

3. **agent-tasks/rules/GUARDRAILS.md "Last Updated" header** — still
   needs bumping to reflect tonight's actual edit date. Small but
   important since this is the authoritative file everyone checks for
   freshness.

4. **Workflow standardization audit pending** —
   `2026-07-03-MEDIUM-AUDIT-STANDARDIZE-AGENT-TASK-WORKFLOW` is
   running/pending. Its pre-audit findings already flagged the same
   git mv-on-untracked-files issue hit multiple times this session.
   When it lands, Rule 12 fix should include:
   - `git add` first correction (not switching to plain `mv`)
   - move-verification steps (ls source/destination, find for strays)
   Do NOT close this audit until those two items are explicitly in the
   output.

5. **Stale task audit queue is ready** — `tasks/review/` now holds
   original stale files, `backlog_april_2026/`, and
   `reorganization_attempt_2/`. Ready for 1-2-a-day audit pass.
   Start with Overlap Scan given likely duplication between the last two.

6. **Two-machine target steady state** — one Qwen on implementation,
   one on backlog/audit. Return to this split going forward.

---

## Process Notes

- Always use locked dispatch templates
- Agents must use git mv for all task file moves — never copy
- After move, agent must verify with find command (single result only)
- Claude web cannot save files — produce in chat, Tracy saves manually
- M4 = fast audits and implementation; Ryzen = heavy implementation
- GUARDRAILS consolidation is Ryzen session only — do not cross-dispatch
