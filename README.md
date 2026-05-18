# Agent Tasks Repository

**Purpose**: Centralized task tracking for multi-project agent coordination across Galaxy Game, Samvera (Hyku, Hyrax), and WVU Libraries projects.

**Why separate repo?**: 
- Keeps project repositories clean (no task clutter in git history)
- Single source of truth for active/backlog/completed tasks
- Accessible offline on all development systems
- Easy to sync across Intel Mac, M4 MacBook, and Windows Ryzen

---

## Structure

```
agent-tasks/
├── galaxy_game/
│   ├── active/           # Current sprint/work
│   ├── backlog/          # Planned but not started
│   ├── completed/        # Finished tasks (archive only)
│   ├── depricated/       # Obsolete tasks (no longer relevant)
│   ├── documentation/    # Task documentation & guides
│   ├── planning/         # Long-term strategy & planning
│   └── testing/          # Testing-focused tasks
├── samvera_hyku/         # Placeholder for Hyku tasks
├── samvera_hyrax/        # Placeholder for Hyrax tasks
├── wvulibraries_acda_portal/  # Placeholder for ACDA tasks
├── wvulibraries_knapsack/     # Placeholder for Knapsack tasks
└── README.md
```

---

## Usage

### Cloning on New Systems
```bash
cd /Users/tam0013/Documents/git
git clone https://github.com/trmccormick/agent-tasks.git
```

### Creating a New Task
```bash
# Create in appropriate project folder and status
# Example: new Galaxy Game backlog task for May 2026
galaxy_game/backlog/2026-05/2026-05-XX-[PRIORITY]-[TYPE]-[DESCRIPTION].md
```

### Task Lifecycle
1. **active/**: Tasks in progress or next up for work
2. **backlog/**: Planned but not yet started
3. **completed/**: Finished (archived for reference)
4. **depricated/**: No longer relevant (kept for context)

---

## Sync Across Systems

### Both Laptops (Intel Mac, M4 MacBook)
```bash
# On system with changes
git add .
git commit -m "Task update: [description]"
git push

# On other system
git pull
```

### Home Network (Pi)
Optional: Can mount Pi share for real-time task access when on home network.
Setup documented in: https://github.com/trmccormick/home-server

---

## Integration with Project Repos

Each project repo has:
- **Agent rules/guidance** → stays in project's `docs/new_agent/` (in git)
- **Task files** → reference `../../agent-tasks/[project]/` (separate repo)

Example paths:
- Galaxy Game rules: `galaxyGame/docs/new_agent/rules/DECISIONS.md`
- Galaxy Game tasks: `agent-tasks/galaxy_game/backlog/`

---

## Notes

- Do NOT commit completed tasks permanently; move them to completed/ folder periodically
- Keep .gitignore clean; no system files (.DS_Store, etc.)
- Use consistent naming: `YYYY-MM-DD-[PRIORITY]-[TYPE]-[DESCRIPTION].md`
