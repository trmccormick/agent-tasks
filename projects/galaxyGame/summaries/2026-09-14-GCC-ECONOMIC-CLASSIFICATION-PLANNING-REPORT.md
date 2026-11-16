# GCC Economic Classification — Planning Report

**Date**: 2026-09-14  
**Prepared by**: Qwen (Planning Agent)  
**For**: Tracy + Gemini review before P0 dispatch  
**Status**: REVIEW REQUIRED — do not dispatch P0 until reviewed  

---

## 1. Files Reviewed

### Economy Wiki Docs (docs/wiki_reorganization/economy/)
| File | Status | GCC Classification Accuracy |
|------|--------|---------------------------|
| `02-currencies-and-accounts.md` | CORRECT ✅ | Explicitly states "GCC is a currency, not a material." No production field. Minting via LDC only. Pre-seeding as second source. |
| `03-market-and-pricing.md` | CORRECT ✅ | "LDC is the sole issuer of GCC." GCC supply backed by Luna's productive capacity (descriptive, not commodity claim). 1:1 USD peg described as value-anchor, not conversion mechanism. |
| `04-bonds-and-financing.md` | NEEDS CORRECTION ⚠️ | Section 3.2 "GCC Mining Bonds" uses "collateral: crypto_mining_satellite_01" language that could conflate GCC with physical collateral. Should clarify these are bonds **denominated in** GCC, not bonds about mining a commodity. |
| `05-launch-and-operational-fees.md` | CORRECT ✅ | Launch payments use GCC/USD as currencies. No commodity language. |
| `06-contracts-and-players.md` | CORRECT ✅ | Player contracts pay in GCC. No commodity language. |
| `07-npc-economy-lifecycle.md` | CORRECT ✅ | NPC economy uses GCC as currency. No commodity language. |
| `AUDIT-ECONOMY-DOCS.md` | CORRECT ✅ | "GCC minting description: no USD-conversion step exists in the actual minting path; mining rate is hardware-dependent (base rate + fitted computer/GPU component bonuses), not a flat number." "The 1:1 USD peg is a separate value-anchor fact, not a conversion mechanism." |
| `GAPS.md` | NEEDS CORRECTION ⚠️ | Gap D uses "emission" language ("Hybrid GCC Supply Model," "Emission Schedule Enforcement") that could be misread as physical emission. Should use "issuance" or "minting" instead. |

### Code Evidence
| File | Finding |
|------|---------|
| `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` (line 10) | `def mine_gcc` — method exists in a concern module, not on the satellite model directly |
| `data/json-data/operational_data/craft/satellites/crypto_mining_satellite_data.json` | Has `base_mining_rate_gcc_per_hour: 1000` (flat constant) AND references fitted hardware bonuses |
| `data/json-data/blueprints/crafts/space/satellites/generic_satellite_bp.json` | `compatible_units` whitelist does NOT include `satellite_battery` — confirmed mismatch with recommended fit |

### P0 Task Draft
| File | Status |
|------|--------|
| `2026-09-14-HIGH-FEATURE-GCC-MINING-SATELLITE-FITTING-DRIVEN-OUTPUT-GAMELOOP-INTEGRATION.md` | DRAFT — needs terminology correction before dispatch |

---

## 2. Current Wording Conflicts

### Conflict A: "GCC Mining Bonds" (04-bonds-and-financing.md §3.2)
**Current wording**: "Long-term financing secured by mining satellite asset; creates recurring GCC demand sink." / "collateral: crypto_mining_satellite_01"  
**Problem**: "Secured by mining satellite asset" and "collateral" language could imply GCC is a physical commodity being mined, rather than a fiat currency whose issuance mechanism happens to be called "mining" in-universe.  
**Recommended fix**: Clarify that the bond is **denominated in** GCC (space-side obligation), and the satellite is the productive capacity backing the issuer's ability to service the debt. The collateral is the satellite asset itself, not GCC.

### Conflict B: "Emission" Language (GAPS.md Gap D + H)
**Current wording**: "Hybrid GCC Supply Model," "Emission Schedule Enforcement," "daily emission rate"  
**Problem**: "Emission" in a space-industrial context reads as physical gas/chemical emission, not currency issuance. This is the same confusion the design clarification explicitly addresses.  
**Recommended fix**: Replace all instances of "emission" with "issuance" or "minting." Use "GCC issuance schedule" instead of "emission schedule."

### Conflict C: P0 Task Draft — "Mining Output" Language
**Current wording**: "mining output for a given day," "GCC mining satellite," "authorized GCC throughput"  
**Problem**: The task draft uses "mining output" which could be read as physical production. While "GCC mining" is established in-universe terminology, the task should explicitly state this is currency issuance, not material extraction.  
**Recommended fix**: Add a terminology note at the top of the task: "GCC 'mining' is authorized currency issuance, not commodity extraction. All references to 'mining output' mean 'authorized GCC issuance per tick.'"

### Conflict D: 03-market-and-pricing.md — "GCC Supply Backed By"
**Current wording**: "GCC supply is backed by Luna's productive capacity — resource extraction output, infrastructure value, and settlement economic activity."  
**Problem**: "Backed by" in monetary economics implies commodity backing (like gold standard). The design intent is fiat-style with a peg, not commodity-backed.  
**Recommended fix**: Change to: "GCC supply grows through authorized LDC issuance (mining satellites), anchored to Luna's productive capacity as the economy's growth indicator."

---

## 3. Gemini Review Required

The following items need Gemini's economic-design review before P0 dispatch:

| Item | Question for Gemini |
|------|-------------------|
| GCC classification | Confirm GCC is fiat-style ledger currency, not a material/commodity. Does the wiki need any additional clarification? |
| USD peg | The 1:1 initial peg is described as "bootstrap price calibration." Is this accurate? Are there any other peg-related docs that need correction? |
| Issuance mechanism | LDC is sole issuer via mining satellites + pre-seeding. Is there any other issuance path (e.g., bond creation, NPC earning) that should be documented? |
| Capacity model | The design intent is "authorized GCC throughput = ∑ capacity of qualifying active, powered fitted hardware." Does this align with the operational data in `crypto_mining_satellite_data.json`? |
| Power/battery constraints | Which power and battery constraints apply to GCC issuance per tick? Is the battery smoothing behavior (eclipse recovery) documented anywhere? |
| Economy inputs/costs | What economy inputs/costs are in scope for the P0 task vs. deliberately deferred? Specifically: does GCC issuance have a power cost deducted from the satellite's account, or is it purely a capacity calculation? |
| Wiki pages needing correction | Which wiki pages need correction/clarification per the conflicts above? |

---

## 4. Code-Evidence Findings

### `mine_gcc` Method Location
- **File**: `galaxy_game/app/models/concerns/cryptocurrency_mining.rb` (line 10)
- **Type**: Module concern, not a model method — must be included in the satellite model to be available

### Fields Read by `mine_gcc` (Needs Confirmation)
The P0 task draft lists this as an open question. Before implementation:
1. Does `mine_gcc` read `base_mining_rate_gcc_per_hour` from operational data?
2. Does it aggregate fitted-unit hash rates (from `compatible_units`)?
3. Does it apply rig effects (GPU coprocessor bonuses)?
4. Does it check power/battery availability before computing output?

**Recommended verification**: Read `cryptocurrency_mining.rb` in full and trace every field access. Do not infer from names alone.

### Fitting Data Mismatches
1. **`generic_satellite_bp.json` compatible_units whitelist** does NOT include `satellite_battery`, but the recommended fit installs one. Confirm whether this whitelist is enforced anywhere.
2. **`crypto_mining_satellite_data.json`** has both a flat `base_mining_rate_gcc_per_hour: 1000` AND references to fitted hardware bonuses. These are two different sources of truth — confirm which one `mine_gcc` actually uses.

---

## 5. Recommended Terminology

### Canonical Definitions (for wiki contract)

**GCC (Galactic Construction Credit)**:
> GCC is the game's standard fiat-style ledger currency, issued exclusively by the Luna Development Corporation (LDC). It has no physical form, no commodity backing, and no exchange mechanism. The initial 1:1 USD peg is a bootstrap price calibration reference only — not a redemption guarantee or permanent relationship.

**GCC Mining**:
> "GCC mining" is in-universe terminology for authorized LDC currency issuance via specialized satellite hardware. It is not cryptocurrency, proof-of-work, or physical extraction. The term describes why specialized computing equipment, deployed capacity, electricity, and uptime determine productive throughput — analogous to how real-world data centers consume power to perform computation. No blockchain, nonce, wallet, mining pool, block reward, or token exchange mechanics are in scope.

**Authorized GCC Throughput**:
> `authorized_gcc_throughput = ∑ (capacity of qualifying active, powered fitted hardware)` per tick. The satellite platform does not have a self-contained flat rate constant. Hardware establishes maximum operational throughput; power and battery availability constrain realized throughput during normal simulation ticks.

### Terms to Avoid in GCC Context
| Term | Why Avoid | Alternative |
|------|-----------|------------|
| "mining output" (without qualification) | Reads as physical production | "authorized GCC issuance" or "GCC issued per tick" |
| "emission schedule" | Reads as gas/chemical emission | "issuance schedule" or "minting schedule" |
| "GCC supply backed by [commodity]" | Implies commodity backing | "GCC supply grows through authorized LDC issuance" |
| "mining rate constant" (flat) | Contradicts fitting-driven design | "base capacity per hardware unit" |
| "collateral: crypto_mining_satellite" | Conflates currency with physical asset | "bond denominated in GCC; collateral is satellite asset" |

---

## 6. Open Questions for Tracy/Gemini

1. **GCC classification**: Is GCC represented as a fiat-style ledger currency only, or does it also have any material/commodity properties (e.g., can it be stored as cargo, transferred between accounts without ledger entries)?
2. **Fitted equipment categories**: Which specific fitted equipment types contribute to GCC capacity? Only computing hardware (CPUs, GPUs), or also power generation (solar panels), power storage (batteries), and cooling systems?
3. **Capacity additivity**: Are fitting capacities additive (∑ of all qualifying units) or is there a diminishing returns curve?
4. **Rate unit**: Is the throughput measured in GCC per tick, GCC per game-day, or GCC per real-world-hour? What is the tick-to-time conversion?
5. **Power/battery constraints**: Does power consumption deduct from the satellite's account balance, or does it simply reduce realized throughput (no separate cost)? How does battery smoothing work during eclipse periods?
6. **Economy inputs in scope**: For P0, which economy inputs/costs are in scope? Specifically: is there a GCC cost per tick for power, or is power purely a simulation constraint with no economic cost?
7. **Wiki pages needing correction**: Per the conflicts above, which wiki pages need correction before P0 dispatch?

---

## 7. Dispatch Recommendation

### Do NOT dispatch P0 until:
1. ✅ Gemini confirms GCC classification (fiat-style ledger currency) and approves the canonical definitions in Section 5
2. ✅ Wiki corrections are applied for conflicts A-D in Section 2
3. ✅ Code evidence confirms which fields `mine_gcc` actually reads (Section 4)
4. ✅ Tracy answers open questions in Section 6

### Recommended P0 revisions after review:
1. Replace all "mining output" with "authorized GCC issuance" in the task draft
2. Add terminology note at top of task: "GCC 'mining' is authorized currency issuance, not commodity extraction"
3. Update acceptance criteria to use correct terminology
4. Clarify that power/battery constraints are simulation-level (throughput reduction), not economic-cost items (unless Gemini confirms otherwise)

### Files needing correction before dispatch:
| File | Correction |
|------|-----------|
| `04-bonds-and-financing.md` §3.2 | Fix "collateral" language; clarify bond is denominated in GCC |
| `GAPS.md` Gap D + H | Replace "emission" with "issuance/minting" |
| P0 task draft | Terminology corrections per Section 7 |

---

## Summary

GCC is definitively a **fiat-style ledger currency**, not a commodity or cryptocurrency. The wiki docs are mostly correct but have four specific wording conflicts that need correction before the P0 task is dispatched. The code evidence shows `mine_gcc` exists in a concern module (`cryptocurrency_mining.rb`) but its exact field accesses need confirmation — do not infer from names alone. The P0 task draft needs terminology corrections to align with the fiat classification.

**Recommendation**: Hold P0 dispatch until Gemini confirms the canonical definitions and wiki corrections are applied. This is a 30-minute review, not a blocking issue — but getting the terminology right now prevents rework later.
