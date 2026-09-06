# FOLLOWUP: Live Game-Loop Deep Dive (Three Questions)

**Task**: Follow-up to 2026-08-29-HIGH-ARCHITECTURE-LIVE-GAME-LOOP-REALITY-CHECK.md
**Agent**: Qwen local via Copilot (Implementation Agent)
**Date**: 2026-08-31
**Type**: Research / architecture (read-only, no code changes)

---

## Q1: Does GameSimulationJob's scheduled execution actually call Game#advance_by_days?

**YES — confirmed by direct trace.**

Call path in `galaxy_game/app/jobs/game_simulation_job.rb`:
- Line 11: `def perform` (Sidekiq job entry point)
- Line 12: `game_state = GameState.first_or_create`
- Line 15: `return unless game_state.running` (gate check)
- Lines 18-19: calculates `days_to_simulate` from real elapsed time / game speed ratio
- **Line 24**: `game = Game.new(game_state: game_state)`
- **Line 25**: `game.advance_by_days(days_to_simulate)` ← direct call, not inferred

This is a live Sidekiq job that calls `advance_by_days` on every successful tick. The call path is unambiguous — no intermediate indirection or proxy.

---

## Q2: Is advance_by_days ever invoked anywhere outside test/rake code?

**YES — exactly one production caller: GameSimulationJob.**

All callers of `advance_by_days` in production code (app/lib, excluding specs):
1. `galaxy_game/app/jobs/game_simulation_job.rb:25` — the Sidekiq live loop (production)
2. `galaxy_game/lib/tasks/gcc_mining_sat.rake:227` — rake task (test/harness)

No other production code path calls `advance_by_days`. It is NOT called by:
- Controllers
- Services
- Other jobs
- Console commands
- Seeds or initializers

**Verdict**: `advance_by_days` serves dual purpose — it's the live loop's time-advance method AND was originally designed as a testing utility. In production, only the Sidekiq job invokes it.

---

## Q3: Does anything in the codebase ever set GameState#running to true?

**YES — but only via user-facing toggle, never automatically.**

### Who sets running=true:
1. **Controller action**: `galaxy_game/app/controllers/game_controller.rb:53-56`
   ```ruby
   def toggle_running
     @game_state = get_or_create_game_state
     @game_state.toggle_running!
     ...
   end
   ```
2. **Model method**: `galaxy_game/app/models/game_state.rb:22-26`
   ```ruby
   def toggle_running!
     self.running = !self.running
     self.last_updated_at = Time.current if self.running
     save!
   end
   ```
3. **Frontend button**: `galaxy_game/app/javascript/game_interface.js:454`
   ```javascript
   fetch('/game/toggle_running', { ... })
   ```
4. **UI button**: `galaxy_game/app/views/game/index.html.erb:271`
   ```erb
   fetch('/game/toggle_running', { ... })
   ```

### Who does NOT set running=true:
- **No seed scripts** — none found in `db/`
- **No initializers** — none found in `config/initializers/`
- **No factories** — no factory default sets it to true
- **GameState#set_defaults** (line 12-19): only sets `running = false if running.nil?`

### Routes:
- No explicit route entry for `/game/toggle_running` found in `config/routes.rb` — likely handled by Rails resource routing or a catch-all.

**Verdict**: The loop is **off by default** and can only be turned on via the game's UI toggle button (which calls the controller → model → DB). Nothing automatically enables it at startup, seed time, or initialization.

---

## Critical Implication

The live loop exists and works correctly when enabled, but:
1. It's **off by default** — new deployments start with `running = false`
2. It requires a **manual UI toggle** to enable — no automatic activation
3. The only production caller of `advance_by_days` is the Sidekiq job itself
4. All MVP assets (Luna precursor, GCC sat, Venus skimmer) remain driven by rake/AIManager

So "the loop" is currently running in **zero deployed instances** unless someone explicitly toggled it on via the game UI. The previous findings stand: the live loop exists but doesn't touch craft or AIManager assets. This followup confirms that even when the loop IS running, it only processes settlements/units/planets — not the specific MVP assets.

---

HANDOFF SUMMARY: followup doc created | key discovery: loop is off by default + requires manual UI toggle to enable; only 1 production caller of advance_by_days (the Sidekiq job itself) | next action needed: user to scope whether to auto-enable loop, wire craft into it, or both
