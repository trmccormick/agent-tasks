# Handoff — 2026-11-18 (claude-free-web), skimmer / mixed volatiles wiki + scratch rule

**Role:** planning and review only. I do not edit repos or run git. Only Tracy commits and pushes (GUARDRAILS Rule 26).
**Labels:** VERIFIED (read from origin or a command this session), REPORTED (stated by a person or agent, or from a pasted copy; `data/` JSON is gitignored).
**Paths (2014 MacBook):** `/Users/tracymccormick/Documents/git/{galaxyGame,agent-tasks}`. Work laptops use `/Users/tam0013/Documents/git/...`. Qwen prompts need complete absolute paths.
**Follows:** `2026-11-17-WEB-REVIEW-GALAXYGAME-WIKI-HANDOFF.md` (same folder).

## Landed on origin (VERIFIED by `git fetch` and `git show origin/main:<file> | grep -c`)

galaxyGame:
- `383c2e3`: `docs/wiki/logistics/skimmer_and_mixed_volatiles.md`, section 2. CO handling is a configurable policy (DIRECTION); optional CO capacity is OPEN; code-alignment bullet added.
- `89f9941`: same page, "Composition and mass". Mixed-volatiles item for all producers (skimmer, TEU, PVE); initial batch composition with some variation (amount OPEN); onboard processing needs finer tracking.
- Earlier today: `f1ce781b` (duplicate Venus-preferred bullet removed), `dc132a3` (placement section).

agent-tasks:
- `4e1a1a3`: `WEB_AGENT_SESSION_START.md` new subsection "Temporary files on the human's machine" plus an End-of-session check. Rule: repo-bound files are saved straight to their repo path; every other temporary file goes in `scratch` in the home folder; at closeout the web agent lists every file it caused to be created in `scratch`, marked safe to delete.
- `c76d639`: Tracy pushed `2026-11-16-WEB-REVIEW-WORKFLOW-CLOSEOUT.md`.

## Committed locally, push not yet verified

- galaxyGame `937d2a0c` (`skimmer_and_mixed_volatiles.md`, +3 lines): "Composition mirrors the atmosphere gas record", "Intake and venting", "Code alignment". First action next session: `git fetch` and confirm `937d2a0c` is on origin/main, with `grep -c "Composition mirrors the atmosphere gas record"` = 1 on origin. If Tracy had not pushed it yet, ask her to run `git -C /Users/tracymccormick/Documents/git/galaxyGame push` first.

## Owner decisions recorded this session (REPORTED, Tracy, 2026-11-18; all DIRECTION or OPEN)

- CO handling at Venus is a configurable policy; with no CO storage fitted, CO is vented back to the source atmosphere.
- An optional CO storage item for the Mk1 Venus craft is OPEN (an adjusted multi-purpose cryogenic tank or a new design). A tank is only a capacity check in code; gas identity and mass live in inventory.
- One `Mixed Volatiles` inventory item (like LOX: one item with an amount). Its metadata mirrors the atmosphere gas record: each gas by chemical formula with mass in kg; percentages derived; `amount` is the total; merging adds each gas's mass. `molar_mass` on the item is OPEN.
- The skimmer removes gas in the same proportions as the source atmosphere's current composition until capacity is full; anything vented goes back with `add_gas`. "Some variation" is OPEN.
- A generic gas transfer does not belong in TerraSim. Candidate homes (not decided): next to `add_gas` and `remove_gas` in `AtmosphereConcern`, or `Resource::AtmosphericHarvester#harvest_gases`.
- Decided NOT to add a NEEDS_REVIEW entry for these: open questions are discussed and settled in the session, then the entry is not written. The existing 2026-11-10 entry (Venus HLT loop, ON HOLD) is untouched.

## Code facts found (VERIFIED at galaxyGame `8fd62da` / `89f9941`)

- `gas_handling_policy` in the Venus harvester data (REPORTED copy: store O2 and N2, store CO if value positive, process CO2, `vent: []`) is read by no Ruby code in `app`, `lib` or `spec`.
- `Units::Storage` per-tank gas methods raise `NotImplementedError`. Capacity is checked through `Inventory#available_capacity_for?` and `find_storage_unit` (the latter not read).
- `Item#special_case_name?` already skips the lookup for the exact name `"Mixed Volatiles"`.
- `Inventory#store_in_inventory` finds an existing item by exact `name, owner, metadata`, so two batches with different compositions create two items, not one running item. `Item#add_quantity` changes only `amount`. `stackable_with?` compares composition only for names starting "Processed". `Inventory#remove_item` filters with interpolated `metadata ->> 'key' = 'value'`, which cannot match a nested composition hash (and interpolated SQL is worth fixing on its own). `items.amount` is decimal(10,2); atmosphere gas masses are floats.
- `TerraSim::AtmosphericTransferService#perform_raw_transfer` extracts each gas in proportion to `percentage`, up to a capacity and a per-body limit (Titan 5%, Venus none, others 20%); it delivers 98% to a target atmosphere and the other 2% is not returned anywhere.
- None of the Item/Inventory findings above is recorded in the wiki yet; only the policy and storage-stub facts are (code-alignment bullets).

## Open for Tracy

1. Unlisted gases: what should the harvester do with a gas not listed as stored or processed (the data's `vent` list is empty)? Question not yet asked in full; proposed default is vent.
2. Whether to record the Item/Inventory findings (separate items per composition, `add_quantity`, `remove_item` nested metadata) somewhere: wiki OPEN bullet, a NEEDS_REVIEW entry, or nowhere until backend resumes.
3. Is the "Industrial framing" paragraph in `docs/wiki/infrastructure/terraforming_options.md` Tracy's? It repeats the "Core process" paragraph right after it. Not changed.
4. The skimmer page edits from `9b2e870` (Mk1 limited processing, goal arrival state, "valid commercial loop") still carry no ESTABLISHED INTENT / DIRECTION label or date.
5. `status.md` Session Log entry covering `383c2e3`, `89f9941`, `937d2a0c` and agent-tasks `4e1a1a3` (the current entry runs only to `f1ce781b`). Guard: no staged or modified status.md first.
6. Mk2 multi-purpose cryogenic tank blueprint not seen (needed for capacity and boil-off if a CO item is designed). The pasted Mk1 tank JSON had `//` comments (REPORTED, unchecked against disk).
7. Wiki linter errors, starting with the three outward links in `markets/markets_eap_and_peg.md`; README folder-link decision; stale `status.md` header (2026-10-26).
8. Not reviewed: UI wiki commits `a116a20c` and `dc45cc6b` (`ui/02_player_view_architecture.md`, `ui/03_admin_and_ai_manager.md`).
9. The earlier 2026-11-17 handoff items still stand (which PVE process and TEU/PVE order are intended; unread Lagrange code in `orbital_mechanics.rb` and `orbital_depot.rb`; `projects/galaxy_game/` old-folder-name docs).

## Temporary files (scratch rule)

Files I caused to be created in `scratch` this session, all safe to delete once their commits are verified:
- `/Users/tracymccormick/scratch/mixed-volatiles-flow.diff` (safe to delete after `937d2a0c` is on origin).
Already deleted by Tracy: `venus-dup.diff`, `skimmer-co.diff`, `web-start-scratch-rule.diff`, `skimmer-mixed-volatiles.diff`.
Check: the stray empty file `/Users/tracymccormick/Documents/git/galaxyGame/main` (cause: a pasted `->` line used as a shell redirect); Tracy was given the `rm` command, so confirm it is gone with `git status -sb`.
Not touched, not mine: `~/agent-out/` and its dated subfolders (origin unknown).
On the work laptops, check `ls -ld ~/scratch` before creating it.

## Method that worked (keep)

- One narrow Qwen prompt per change: absolute paths, step-1 guard, exact old/new text, grep checks, stat only, "STOP, do not commit". Tracy saves the diff to `scratch` and attaches it; I review before any commit.
- Path-limited commits, then fetch, `status -sb`, `git pull --no-rebase` if behind, push, then verify on origin.
- Paste only commands into the shell, never output lines containing `->` (they create stray files).

## Files left uncommitted

None from Claude in either repo. This handoff is untracked until Tracy saves it to `projects/galaxyGame/handoffs/claude-free-web/` and commits it by exact path.
