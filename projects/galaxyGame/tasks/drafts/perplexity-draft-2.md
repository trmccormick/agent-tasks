# DRAFT ONLY — NOT DISPATCHED
# GCC Mining: Separate Calculation from Ledger Credit

## Problem

The inspected GCC mining paths combine mining calculation and balance mutation
in `CryptocurrencyMining#mine_gcc`, while callers can make additional deposits
using the returned amount.

Source-level evidence reports:
- `mine_gcc` calculates output and deposits to an account.
- `Craft::Satellite::BaseSatellite#process_tick` calls `mine_gcc` and then
  performs another deposit to the owner’s GCC account.
- Mission deployment mining calls `mine_gcc` and then performs another deposit
  to `accounts[:ldc]`.
- Scheduled job mining calls `mine_gcc` directly.

If all paths execute as inspected, one mining result can produce more than one
GCC balance credit.

## Affected systems

- Cryptocurrency mining concern/service boundary.
- Manufactured satellite tick processing.
- Scheduled satellite mining job.
- Mission task mining action.
- Financial account deposits and transaction records.
- MiningLog accounting/audit relation.

## Expected behavior

One authorized mining/issuance event must create exactly one GCC monetary
credit, exactly one associated issuance/audit record, and no duplicate balance
mutation through caller follow-up deposits.

The calculation of potential mining output must be distinguishable from the
operation that settles an authorized ledger credit.

## Actual behavior

[FILL IN: repository-confirmed implementation summary, including all current
entry points and recipient paths.]

## Required outcome

- Establish one clearly owned balance-mutation/settlement path for a mining
  event.
- Ensure all supported entry points use that one settlement path or are made
  mutually exclusive.
- Preserve a calculation result for operational/logging use without allowing
  callers to accidentally settle it again.
- Keep future currency-policy extensibility; do not hardwire all currencies
  into GCC behavior at this step.
- Keep ordinary non-mining account deposits and transfers working.

## Acceptance criteria

- A single mining event results in exactly one GCC balance increase across all
  accounts.
- Satellite tick, scheduled job, and mission/deployment entry points cannot
  duplicate-credit the same calculated amount.
- The single credit has one corresponding audit record or a proven one-to-one
  relationship between the financial transaction and MiningLog.
- Tests cover each supported entry point and assert aggregate account-balance
  change.
- No unrelated exchange-rate, market, physical-mining, or player-UI work is
  introduced.
- Documentation notes are updated only if separately approved.

## Dependencies and blockers

- Must coordinate with the separate GCC authorization/recipient-routing
  decision.
- Must coordinate with the separate mining time/cadence decision.
- Do not select a final issuance-policy architecture without explicit approval.

## Luna impact

Blocks reliable NPC economic simulation because duplicated issuance can distort
balances, liquidity, profitability, settlement affordability, and later market
state.