# DRAFT ONLY — NOT DISPATCHED
# GCC Issuance Policy: LDC Authorization and Recipient Routing

## Problem

Current mining behavior appears to retain a generic Bitcoin-like prototype:
objects with accounts, computer units, and power can execute mining behavior,
while deposit recipients vary by entry path.

The canonical GCC policy is different:
- GCC is centrally managed, crypto-inspired, nonphysical ledger currency.
- LDC-controlled or explicitly LDC-authorized infrastructure may issue GCC.
- Compute capacity alone is not mint authority.
- All newly issued GCC is credited to the existing LDC GCC account.
- LDC later distributes existing GCC via explicit authorized disbursements.

The inspected code does not show an explicit LDC authorization guard, a
canonical issuer/facility eligibility rule, or a unified LDC recipient route.

## Affected systems

- Currency policy/issuance domain boundary.
- GCC mining operation.
- Satellite/settlement compute capability.
- LDC account resolution.
- Financial transaction and mining-log audit behavior.
- Future multi-currency extensibility.

## Expected behavior

The system must distinguish:
1. Reusable compute-capable mining/issuance infrastructure.
2. GCC-specific authorization and recipient policy.
3. Future currencies that might use different governance models.

For GCC, a compute-capable host must not create new GCC unless it is eligible
under the GCC issuance policy. An authorized issuance event must credit the
existing LDC GCC account.

## Required outcome

- Introduce or identify an appropriate policy/service/domain boundary for
  currency-specific issuance rules.
- Resolve the existing LDC account through a repository-confirmed mechanism.
- Verify eligible GCC issuance infrastructure through an explicit,
  testable authorization rule.
- Route all new GCC issuance to the resolved LDC GCC account.
- Preserve generic account deposits, standard transfers, USD handling, and
  future per-currency policy extensibility.
- Do not make `Financial::Account#deposit` globally reject legitimate deposits
  for all currencies unless repository evidence demonstrates that this is the
  correct abstraction.

## Acceptance criteria

- Unauthorized compute-capable craft, satellites, or settlements cannot create
  net-new GCC.
- Authorized GCC issuance is credited only to the existing LDC GCC account.
- The issuer/facility eligibility rule and recipient selection are test-covered.
- Mining/issuance has an auditable identity distinct from routine transfers;
  if MiningLog is retained for this distinction, its relationship to the
  credited financial transaction is testable.
- USD and ordinary multi-currency account operations remain unaffected.
- The implementation does not introduce a permissionless-GCC exception.
- The design remains capable of supporting a future currency with a different
  issuance policy, without enabling it now.

## Non-goals

- Adding a new player-mineable cryptocurrency.
- Defining a real proof-of-work protocol.
- Implementing a GCC exchange float, managed band, supply-demand pricing, or
  Phase 2/3 monetary policy.
- Creating generic settlement/craft authorization for every future currency.
- Changing physical extraction/resource outputs.

## Dependencies and blockers

- Human confirmation of the desired authorization expression:
  [FILL IN: LDC ownership, designated facility role, authorization record,
  account/faction relationship, or a combination.]
- Coordinate with duplicate-credit remediation.
- Coordinate with issuance-event audit design.

## Luna impact

High. This protects the NPC bootstrap economy from arbitrary currency creation
and ensures the intended LDC-led initial distribution model.