Prepare the closing handoff for this planning session.

This is closing-session work. After creating the handoff, do not begin new
research, planning, code work, documentation/wiki edits, data changes, task
creation, task dispatch, or implementation unless the human explicitly starts
a new scoped request.

Do not modify application source, tests, blueprints, operational data,
configuration, migrations, seeds, or committed wiki pages.
Do not dispatch implementation tasks.
Do not silently resolve remaining human decisions.

Create:
projects/galaxy_game/handoffs/planning/2026-09-15-GCC-LUNA-PLANNING-HANDOFF.md

Purpose:
Provide Claude and the next planning/research session a compact, evidence-based
handoff covering this session’s GCC P0 verification, settled design intent,
planning-document updates, rig discussion, deferred power-data research, and
remaining human decisions.

Include these sections:

1. Session objective and scope
- Luna-first, NPC-only simulation support before player entry.
- GCC P0 verification, planning alignment, and documentation-alignment
  preparation.
- No Mars-specific implementation, player UI, lore expansion, Phase 2–3
  monetary implementation, or broad architecture redesign.

2. Completed work and artifacts
List these artifacts and describe each in one line:
- summaries/2026-09-15-GCC-P0-VERIFICATION-EVIDENCE-PACKET.md
- summaries/2026-09-15-GCC-P0-SUPPLEMENTAL-VERIFICATION.md
- summaries/2026-09-15-GCC-P0-PLANNING-DECISION-PACKET.md
- summaries/2026-08-31-ARCHITECTURE-POWER-DATA-TAXONOMY-RESEARCH.md
- summaries/2026-08-31-power-data-taxonomy-inventory.json

Also record:
- Power Data Taxonomy Research task was closed and committed as 8551ef0.
- It is located under tasks/completed/2026-09/.
- No taxonomy migration or application/data changes were made.

3. Confirmed implementation findings
Record only evidence-backed current-code findings:
- speed=3 equals 60 real seconds per game day.
- Production uses seconds_per_game_day.
- Construction uses real-world elapsed days via /86400.
- Missions use EPOCH/86400 real-world day logic.
- Mining uses an independent rake-loop path.
- 28800 in game_loop_integration_spec.rb is comment-only.
- mine_gcc creates net-new GCC via account.deposit().
- Craft-stat mining calculations are disconnected from payout/balance mutation.
- base_mining_rate_gcc_per_hour: 1000 is unconsumed in the payout path.
- *_per_hour mining fields have no demonstrated runtime time basis.
- ExchangeRateService and ExchangeRate model are unsynchronized.
- VirtualLedgerService.exchange_rate_to_gcc = 100.0 is production-active
  through record_in_situ_savings() and makes 100 USD become 1 GCC.
- GCC is not represented as physical cargo, inventory, resources, recipes,
  or commodities in the reviewed schemas.
- Financial::Account#deposit lacks a low-level issuer guard.
- mine_gcc is the only identified production GCC deposit caller.
- Do not claim LDC ownership/authorization or recipient-account behavior is
  verified unless the evidence packet explicitly proves it.

4. Settled GCC design decisions
Record exactly:
- GCC is centrally managed, crypto-inspired, nonphysical virtual ledger
  currency with 8-decimal precision.
- It is a system currency used for player/NPC transactions, markets,
  contracts, and services.
- Accounts are multi-currency; GCC and USD are initial supported currencies;
  future currencies remain possible.
- During initial launch and Luna bootstrap:
  1 GCC = 1 USD.
- This initial peg is a locked stable accounting standard.
- Future uncoupling is deferred; it is not triggered by time, supply alone,
  one trade, or one account.
- Qualitative readiness indicators for future uncoupling include sustained
  GCC trade, meaningful non-LDC circulation, multiple active market nodes,
  local production/services/trade, local price influence, liquidity/history,
  and reliable accounting/conversion.
- A narrow LDC-managed band around parity is a nonbinding future design
  reference only.
- GCC issuance uses LDC-controlled simulated compute mining.
- Initial issuance infrastructure is LDC-operated crypto-mining satellites.
- Later eligible infrastructure may include LDC-authorized crypto-mining data
  centers and other designated LDC compute facilities.
- Compute capacity alone is not mint authority.
- All newly mined GCC flows to the existing LDC account.
- LDC distributes existing GCC by explicit transfers, market liquidity,
  contracts, services, rewards, and other authorized disbursements.
- Physical extraction produces material/cargo and never directly creates GCC.
- Standardized component identity is independent of manufacturing location.
- Mk/Mark means hardware design revision, never build location or fitted
  configuration.

5. Rig design discussion
Record the human-settled rig concept:
- Rigs are EVE-inspired attachable/deployable modifications, not loadouts.
- They add or extend compatible host capability without changing the host’s
  base blueprint identity or Mk/design revision.
- A host rig port supplies the required abstracted interfaces, including
  structural attachment, power, data, control, and other compatible
  connections.
- Positive and negative rig effects modify the host craft, station, structure,
  unit, or deployment site.
- Expansion rigs may enable separate equipment:
  solar_expansion_rig attaches to an HLT and enables mounted solar panels;
  thruster_expansion_rig enables mounted compatible thrusters.
- Integrated rigs can directly add capability:
  gpu_coprocessor_rig adds compute capability while adding power, thermal,
  mass, and volume consequences.
- Utility/deployment rigs can apply specialized effects:
  wormhole_anchor_rig is a future-only reference and not Luna scope.
- recommended_fit is an NPC/test/reference configuration, not a rig and not a
  final player-fitting architecture.
- Current generated rig records are:
  solar_expansion_rig, thruster_expansion_rig, gpu_coprocessor_rig,
  wormhole_anchor_rig.
- Other rig subfolders are currently empty scaffolding; do not represent them
  as implemented or populated content.
- Blueprint/schema alignment for rigs is deferred and must begin by
  extracting existing docs/data/code before proposing changes.

6. Power-data taxonomy research status
- Research completed; human review deferred.
- Preserve the distinction under review among energy, power, and
  power_generation.
- Reported stop conditions:
  critical duplicate IDs including power_controller and solar_panel;
  reactor metadata shells lacking operational output/mass/volume/construction
  specifications; power_generation not loaded by the reported lookup path;
  directory/category mismatches; missing compact_fusion_reactor_l1* records.
- Do not migrate, rename, delete, canonicalize, or add power/reaction data
  until human review and a bounded plan.
- Do not treat empty category folders as active gameplay systems.

7. Documentation/wiki alignment process
- Current priority is alignment before new implementation/data tasks.
- Existing relevant docs/wiki must be inventoried and extracted first.
- Classify each existing statement as:
  confirmed current behavior, settled design intent, deferred future design,
  conflicting/stale claim, or unverified.
- Preserve useful existing details; do not rewrite from memory.
- Produce an alignment proposal before any wiki edits.
- No committed wiki changes without explicit human approval.
- Known GCC terminology inventory remains review-only:
  GCC Mining Bonds collateral phrasing;
  GCC supply “backed by” Luna productive capacity phrasing;
  missing bootstrap/deferred marker in virtual-ledger section;
  unsupported claim that ExchangeRateService adjusts supply/demand.
- GAPS.md “emission” → “issuance” is already corrected.

8. Open questions and blockers
Separate into:
A. Immediate P0 correctness:
- Approve and dispatch the non-dispatched task draft to align the active
  in-situ savings USD→GCC conversion with 1 GCC = 1 USD.
- Human choice is whether the P0 correction delegates to ExchangeRateService
  or uses an explicit bootstrap 1.0 constant. Do not decide automatically.
- Note: this definitely affects savings valuation/cross-currency reporting;
  do not claim it directly blocks NPC market pricing unless a dependency is
  traced.

B. GCC issuance:
- Verify or decide how the issuance operation proves eligible
  LDC control/authorization, credits the existing LDC account, and records
  issuance distinctly from transfers.
- Do not impose a blanket restriction on all generic multi-currency deposits.

C. Simulation timing:
- Decide whether construction, missions, and mining must advance on game time
  during accelerated NPC-only buildup, remain wall-clock/event-driven, or use
  a defined hybrid.
- Define the actual intended time basis for mining fields labelled per hour
  before AI/NPC profitability relies on them.

D. Documentation:
- Review existing documentation first, then approve/reject specific wording
  changes.
- No wiki edits currently authorized.

E. Deferred taxonomy:
- Human review of energy/power/power_generation and duplicate-ID/reactor
  findings after GCC work.

9. Recommended next action for Claude
- Read the cited GCC evidence and planning artifacts.
- Verify current branch/task context before any implementation recommendation.
- Do not implement without explicit approval.
- If asked to address the initial peg defect, review the non-dispatched task
  draft and prepare a repository-confirmed implementation plan or execute
  only after human authorization.
- If asked to support documentation work, first inventory existing documents
  and report alignment findings; do not overwrite or edit without approval.
- Keep Luna-first scope and do not initiate Mars/fusion/taxonomy migration
  work.

10. Completion statement
State explicitly:
- No code, test, blueprint, operational-data, configuration, migration, wiki,
  or implementation changes were made by this planning session.
- No implementation task was dispatched.
- No unresolved ambiguity was silently decided.
- Planning session is ending/paused and work is handed back to Claude.
