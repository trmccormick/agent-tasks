You are GPT-4.1 acting as the EXECUTOR agent.

**BEFORE DOING ANYTHING ELSE:**
1. Read: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md`
2. Find and review your EXECUTOR role section (it defines your boundaries)
3. Review: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (task lifecycle rules)

---

Task:
Audit and move the GuaranteedMarketSale task from backlog to completed status.

This is NOT implementation — it is task management cleanup and verification.

Task file location:
`/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-05/2026-05-29-MEDIUM-FEATURE-GUARANTEED-MARKET-SALE-LDC-BUYER.md`

Before changing anything:
1. Read README.md and understand EXECUTOR role
2. Confirm the service implementation exists and tests pass
3. Verify integration with TradeExecutionService
4. Then move files and update metadata

Target files:
- Verify exists: `galaxy_game/app/services/marketplace/guaranteed_market_sale.rb`
- Verify tests: `galaxy_game/spec/transactions/guaranteed_market_sale_spec.rb`
- Move from: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-05/2026-05-29-MEDIUM-FEATURE-GUARANTEED-MARKET-SALE-LDC-BUYER.md`
- Move to: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/completed/2026-05-29-MEDIUM-FEATURE-GUARANTEED-MARKET-SALE-LDC-BUYER.md`
- Update: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/TASK_OVERVIEW.md`

Requirements:
- Verify GuaranteedMarketSale service file exists
- Run spec: `docker exec web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/transactions/guaranteed_market_sale_spec.rb'`
- Confirm all tests pass (7/7 expected)
- Verify NPC buyer routing in TradeExecutionService
- Move task file to completed/ directory
- Update task file header: `status: completed` + `completion_date: 2026-05-30`
- Move entry in TASK_OVERVIEW.md from backlog to completed section
- Commit changes to agent-tasks repo

Implementation scope:
- Step 1: Verify service exists and tests passing
- Step 2: Verify integration with TradeExecutionService
- Step 3: Move task file from backlog/ to completed/
- Step 4: Update task file YAML header with completion metadata
- Step 5: Update TASK_OVERVIEW.md (move entry to Completed section)
- Step 6: Commit all changes to agent-tasks

Testing:
- Run targeted spec for verification only (do not modify spec file)
- Report pass/fail status and test count
- If any test fails: STOP and report before proceeding

Output required:
1. Verification results (service exists, tests pass/fail count)
2. Integration confirmation (NPC routing found in TradeExecutionService)
3. File operations completed (moved from backlog to completed)
4. Metadata updated (YAML header, TASK_OVERVIEW.md)
5. Commit hashes from agent-tasks repo
6. Any blockers or issues encountered
7. STOP — do not apply changes without explicit approval

Stop immediately if:
- Service file does not exist
- Tests do not pass
- Integration code is missing
- Files cannot be moved (permission issues)
