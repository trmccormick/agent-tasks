---

## Acceptance Criteria
- [ ] `strategy_selector_spec.rb` Phase 4 examples all pass (lines 737, 767, 787, 802)
- [ ] No regressions in remaining strategy_selector specs
- [ ] ai_manager suite: 746 examples, 3 pre-existing failures only, 4 pending
- [ ] No changes to spec files, factories, or any file outside strategy_selector.rb

---

## Stop Conditions — escalate immediately if:
- Any previously passing spec in strategy_selector_spec.rb now fails
- ai_manager suite failure count exceeds 3
- `current_population` method does not exist on settlement — do not add it, escalate

---

## Commit Instructions
Host only, not inside Docker:
```bash
git add app/services/ai_manager/strategy_selector.rb
git commit -m "bug-fix: strategy_selector — re-implement Phase 4 consumption-aware ordering and precursor phase gate"
git push
```

---

## Dependencies
**Blocked by**: Task 1 (market_price_service_spec syntax fix) — run Task 1 first,
confirm import_request_generator_spec passes, then dispatch this task
**Blocks**: none
**Related tasks**: none

---

## Completion Report
*Filled in by implementing agent*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed

### Issues discovered

### Follow-up tasks needed

### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: strategy_selector.rb | Phase 4 constants + helpers + loop logic added | Run full suite to confirm baseline restored