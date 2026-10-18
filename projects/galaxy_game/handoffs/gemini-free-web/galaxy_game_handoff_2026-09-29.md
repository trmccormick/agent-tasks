Handoff Summary for Claude: Economic Infrastructure & Branch Integration
1. Context & Objective
Goal: Integrate the valuable architecture and fee logic from the unmerged market-fee-hold branch into main via selective cherry-picking, avoiding the stubbed-out services and premature code deletions.

Architecture State: Models (Financial::LedgerEntry, VirtualLedgerService, Craft::Satellite::BaseSatellite) and multi-currency mechanics (GCC/USD 1:1 peg) are stable and verified. The wiki_reorganization documentation package recently passed independent review.

2. Key Findings from market-fee-hold Audit
What to Keep (High Value):

12 New Economic Architecture Docs: (~2,500 lines) detailing market design and fee structures.

SettlementFees Concern (app/models/concerns/settlement_fees.rb): Adds 6 configurable fee parameters in operational_data['fees'] JSONB on settlements, along with calculation helpers (calculate_broker_fee, calculate_transaction_fee).

Comprehensive Specs: spec/services/ai_manager/per_location_fees_spec.rb (17 tests covering accessors, defaults, and calculations).

What to Avoid / Leave Behind:

The branch's massive deletions and stubbed-out services (e.g., commented-out DB writes in ContractCreationService and stubbed survey tasks) must not be brought over, as they would regress functional code on main.

3. Action Items / Work Needed for Claude
Cherry-Pick & Integrate SettlementFees:

Port app/models/concerns/settlement_fees.rb and include it in BaseSettlement and OrbitalSettlement if not already present.

Port spec/services/ai_manager/per_location_fees_spec.rb and ensure the spec suite runs green.

Wire Up Fee Calculations (Active Implementation):

Unlike the old branch (where the fee logic was completely inert), wire calculate_broker_fee and calculate_transaction_fee into the relevant pricing and order-placement services so the parameters actively affect transactions.

Port Architecture Documentation:

Bring over the 12 new economic design documents into the workspace documentation path to align with our canonical wiki structure.

Cleanup:

Safely delete the local market-fee-hold branch once the cherry-picked items are committed to main to prevent future drift.