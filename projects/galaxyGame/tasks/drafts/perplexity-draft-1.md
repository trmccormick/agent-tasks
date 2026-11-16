# Galaxy Wiki Reorganization — Currency Governance and GCC Issuance Separation

You are working in a dedicated wiki_reorganization session for the Galaxy game.

## Scope

Prepare the next canonical documentation package that separates:

1. The game-wide multi-currency architecture.
2. Reusable compute-backed currency/issuance capability.
3. GCC’s specific LDC-controlled bootstrap issuance policy.
4. External Earth fiat currencies, beginning with USD and extensible to other Earth currencies.
5. Future independently governed, consortium, protocol, and service-credit currency possibilities.
6. Physical resource extraction/production from virtual-ledger currency issuance.
7. The GCC mining satellite as a manufactured satellite/craft asset.

The target is the NEW `wiki_reorganization/` documentation structure only.
Do not treat the original `wiki/` as the authoritative target.

## Authority and evidence rules

Treat the following as settled human design decisions:

- Galaxy is a multi-currency economy.
- GCC and USD are the initial supported currencies.
- Additional Earth currencies may be added when needed.
- Future off-Earth currencies may use different governance models.
- A currency’s governance, issuance authority, eligibility, recipient routing,
  supply rules, audit rules, and exchange behavior are currency-policy
  properties; they are not implied by accounts, compute equipment, or craft.
- GCC is centrally managed, crypto-inspired, and nonphysical.
- During initial launch and Luna bootstrap, 1 GCC = 1 USD.
- GCC issuance uses LDC-controlled simulated compute mining.
- Compute capacity alone is not GCC mint authority.
- All newly issued GCC is intended to flow to the existing LDC GCC account.
- LDC distributes existing GCC through authorized transfers, markets,
  contracts, services, rewards, liquidity, and other disbursements.
- Physical extraction creates material/cargo and never directly creates GCC.
- Future permissionless or independently governed compute currencies remain
  possible, but are deferred and do not change GCC’s current policy.
- Manufactured satellites are craft; natural satellites are celestial bodies.
- `recommended_fit` is a practical NPC/test/reference assembly configuration,
  not a rig and not finalized player-fitting architecture.
- Rigs are host-preserving attachable modifications, not loadouts.

Treat the following as source-level implementation findings, NOT as canonical
GCC policy:

- Current mining paths use generic account deposits.
- Recipient routing varies among satellite, owner, and caller-supplied LDC
  account paths.
- Tick and mission paths may cause duplicate credits because `mine_gcc`
  mutates a balance and callers can make another deposit.
- The inspected paths do not show explicit LDC authorization enforcement.
- Satellite-level rate fields and configured supply controls have no proven
  active runtime enforcement/consumption in the mining payout path.
- Mining currently has mixed tick, scheduler, and mission triggers.
- The wiki must not claim these implementation gaps are resolved.

## Required workflow

1. Inventory existing relevant pages in `wiki_reorganization/`, including:
   - economy pages;
   - Phase 4 canonical index/site map/relocation plans;
   - craft/satellite analysis;
   - terminology materials;
   - current GCC mining satellite language.
2. Identify the current canonical page owner for each affected concept.
3. Produce an alignment proposal BEFORE modifying any wiki page.
4. Preserve useful existing wording and do not rewrite from memory.
5. Classify all material statements as exactly one:
   - Verified current behavior.
   - Canonical design decision.
   - Planned implementation.
   - Deferred future design.
   - Conflicting/stale claim.
   - Unverified/insufficient evidence.
6. If a proposed page would contradict the Phase 4 canonical index, recommend
   the smallest index/site-map change rather than creating a competing page.
7. Do not invent runtime behavior, numeric issuance rates, authorization
   mechanisms, future cryptocurrency mechanics, or player-fitting behavior.
8. Do not modify code, tests, blueprints, operational data, config, or legacy
   wiki content.

## Required deliverable: proposal only

Return one compact but complete proposal, not edits, with these sections:

A. Current canonical ownership
- Table: concept | current/new wiki location | authority state | conflict/gap.

B. Canonical policy vocabulary
Define, with concise approval-ready wording:
- multi-currency economy;
- external fiat currency;
- system-managed ledger currency;
- permissionless/protocol currency (deferred model);
- consortium/regional currency (deferred model);
- service/asset credit (deferred model);
- currency policy;
- issuance;
- mint authority;
- eligible issuance infrastructure;
- circulation/disbursement;
- physical extraction;
- manufactured satellite;
- natural satellite;
- compute capability versus authority.

C. Proposed page structure
Use the existing reorganized site-map conventions where possible.
For each page/section list:
- path;
- canonical purpose;
- status label;
- content that is safe to state now;
- claims that need an implementation-alignment notice;
- claims that must remain deferred/open.

At minimum assess:
- currencies and accounts;
- currency governance/issuance models;
- GCC bootstrap policy;
- GCC mining satellite;
- GCC issuance versus physical extraction;
- craft taxonomy / manufactured satellites;
- rigs and recommended fits.

D. Exact proposed status/alignment callouts
Draft concise blocks for:
- GCC policy versus current source-level mining behavior;
- unverified rate/cadence/cap/halving configuration;
- future permissionless/protocol currency option;
- deferred player fitting;
- natural versus manufactured satellite terminology.

E. Legacy/prototype handling
Provide a wording pattern that preserves the historical Bitcoin-like prototype
as implementation history without describing it as present canonical GCC policy.

F. Open review items
Separate:
- human design decisions;
- implementation verification/code-alignment issues;
- documentation-structure approvals.

G. Recommended edit sequence
Give the smallest safe order of future wiki edits after human approval.

## Completion statement

End exactly with:
- No repository files were modified.
- No implementation task was dispatched.
- No unresolved architecture was silently decided.