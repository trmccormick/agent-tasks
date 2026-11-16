# Galaxy Game — Docker Command Wrapper Pattern

**Last Updated**: 2026-06-03  
**Project**: Galaxy Game (Rails + Docker)

---

## Critical Finding: Working Directory Already Set

When executing RSpec commands in the Galaxy Game container:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH]'
```

**Important**: The `cd /home/galaxy_game` is redundant! The Dockerfile sets:
```dockerfile
WORKDIR /home/galaxy_game
```

So when you run `docker exec -it web bash`, you're already in `/home/galaxy_game`.

---

## Command Patterns

### ✅ Correct Pattern (Use This)

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH]'
```

### ❌ Incorrect Pattern (Avoid This)

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH]'
```

---

## Why This Matters

1. **Cleaner commands**: No redundant `cd` instruction
2. **Less typing**: One less thing to remember
3. **Consistency**: All agents should use the simplified pattern
4. **Error prevention**: Avoids confusion about working directory state

---

## Files That Need Updating

Search for and replace:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec'
```

With:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec'
```

---

## Priority Order for Updates

1. **High Priority** — Active task files in `tasks/backlog/2026-06/*.md` and any new task files
2. **Medium Priority** — Completed task files in `tasks/completed/2026-06/*.md` and `tasks/completed/2026-05/*.md`
3. **Low Priority** — Deprecated files in `tasks/deprecated/*/*.md`

---

## Session Notes

This is a **Galaxy Game project-specific** Docker configuration. Other projects may have different WORKDIR settings, so always check the Dockerfile first before assuming the working directory state.
