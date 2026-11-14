# MySQL 8.4.10 Upgrade — Databases Project (2026-07-28)

## Summary
Successfully upgraded the `rails7-circleci-test` branch (the VM's active branch) to MySQL 8.4.10 and aligned all configuration with the library_directory project.

## Work Completed
1. **Identified the issue**: `rails7-circleci-test` had unpinned MySQL 8 (`mysql:8`) and MySQL 8.0 in CircleCI (`cimg/mysql:8.0`)
2. **Applied fixes across all files**:
   - Pinned MySQL to 8.4.10 in both docker-compose files
   - Updated CircleCI to use cimg/mysql:8.4.10
   - Fixed databases/Gemfile mysql2 constraint to `'~> 0.5.6'` (MySQL 8 compatible)
   - Corrected JEMALLOC LD_PRELOAD path in Dockerfile (libjemalloc.so → libjemalloc.so.2)
3. **Tested locally**: Docker build successful, database setup successful with MySQL 8.4.10
4. **Pushed to remote**: Created `review-rails7` branch with all changes
5. **Cleaned up**: Deleted obsolete `feature/mysql-8-upgrade` branch

## Key Discovery
- `rails7-circleci-test` already had MySQL 8 but unpinned and mismatched versions
- VM is running `rails7-circleci-test` (not `main` which is still on MySQL 5.7)
- library_directory project uses MySQL 8.4.10 as reference

## Testing Status
- ✅ Docker build: SUCCESS
- ✅ MySQL 8.4.10 connection: SUCCESS
- ⚠️ RSpec tests: Shoulda gem configuration issue (not related to MySQL upgrade)

## Files Modified
- `.circleci/config.yml`
- `Dockerfile`
- `docker-compose.dev.yml`
- `docker-compose.yml`
- `databases/Gemfile`

## Branches
- `review-rails7`: Active branch with all changes (pushed to remote)
- `feature/mysql-8-upgrade`: Deleted (obsolete)

## Next Steps
1. Review PR for review-rails7
2. Fix Shoulda gem configuration in spec tests
3. Merge to rails7-circleci-test
4. Deploy to production when ready
