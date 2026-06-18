---
title: "PHPUnit Integration & Test Generation Infrastructure"
status: active
priority: HIGHEST
type: SETUP
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1 week"
sequence: 1
branch: "refactor/authentication-modernization"
branch_strategy: |
  Single refactor branch for all modernization work.
  - Create from main: git checkout -b refactor/authentication-modernization
  - All tasks (PHPUnit, tests, MySQL functions, security audit, Docker upgrade) work on this branch
  - Production VM stays on main (never disturbed during refactor)
  - Only merge back to main when ENTIRE refactor branch passes all tests
  - Merge criteria: Full test suite passing, all blockers complete, no regressions
blocks:
  - "2026-06-18-HIGH-FEATURE-LOGIN-TEST-SUITE"
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS"
  - "2026-06-18-MEDIUM-MAINTENANCE-SECURITY-AUDIT"
---

# Task: PHPUnit Integration & Test Generation Infrastructure

## Problem
Authentication application has **zero automated test coverage**. Before any code changes (deprecated MySQL functions, security hardening, etc.), we need:
1. Testing framework installed and configured
2. Test environment isolated from production
3. Test fixtures for mock LDAP responses
4. CI/CD hook for running tests before merge to main
5. Baseline tests for current functionality (regression tests)

Without this, any changes risk breaking production login flow with no safety net.

**Branch Strategy**: All modernization work (PHPUnit setup, tests, MySQL migration, security audit, Docker upgrade) happens on a single `refactor/authentication-modernization` branch off main. Production VM stays on main and is never disturbed until the entire refactor branch is complete and verified. Only merge back to main when full test suite passes.

## Scope

### Phase 1: PHPUnit Setup (Docker + Local)
1. Add PHPUnit to `composer.json` (or manual install)
2. Create `tests/` directory structure:
   ```
   tests/
   ├── Unit/
   │   ├── LDAP/
   │   ├── Database/
   │   ├── Session/
   │   └── Security/
   ├── Integration/
   │   ├── LoginFlow/
   │   ├── TemporaryAccounts/
   │   └── ACL/
   ├── Fixtures/
   │   ├── MockLDAPServer.php
   │   ├── sample-ldap-responses.json
   │   └── test-database-seed.sql
   └── phpunit.xml
   ```

3. Docker test environment:
   - Separate MySQL instance for tests (in-memory or temp database)
   - Mock LDAP server (can use publicly available mock or phpldapadmin container)
   - Isolated from production database/LDAP

4. PHPUnit configuration:
   - `phpunit.xml` with proper bootstrap
   - Coverage reporting (target: 60% baseline)
   - Test runner script: `scripts/run-tests.sh`

### Phase 2: Test Generation Scaffolding
1. Generator helper for LDAP mock responses:
   - Create mock LDAP bind scenarios (success, failure, timeout, etc.)
   - JSON fixtures for common responses

2. Database test fixtures:
   - Seed temp accounts table with test data
   - SQL snapshots for schema validation

3. Session test helpers:
   - Mock $_SESSION for testing
   - Helper functions for asserting session state

4. Error handling fixtures:
   - Common error responses (network error, permission denied, etc.)

### Phase 3: Integration with Development Workflow
1. Git pre-commit hook (optional):
   - Run tests before allowing commits to feature branches
   - Allows override with `--no-verify` for WIP commits

2. CI/CD placeholder (GitHub Actions):
   - Test script callable from Actions
   - Ready for automation when needed

3. Documentation:
   - How to write tests (TESTING.md)
   - How to run tests locally (README.md update)
   - Test fixtures guide

## Success Criteria
- [ ] PHPUnit installed and configured
- [ ] `tests/` directory structure created with proper namespace
- [ ] Docker compose includes test MySQL instance
- [ ] Mock LDAP server fixture working (returns valid responses)
- [ ] Sample tests created for:
     - Database connection (mysql.php module)
     - Session initialization
     - Basic LDAP bind scenarios
- [ ] `scripts/run-tests.sh` executes all tests cleanly
- [ ] Coverage report generated (baseline established)
- [ ] All sample tests PASS against current main branch
- [ ] Documentation: TESTING.md created with examples
- [ ] Zero test failures or errors at completion

## Implementation Notes

### PHPUnit Installation
```bash
# Option 1: Composer (preferred)
composer require --dev phpunit/phpunit

# Option 2: Manual phar install
wget https://phar.phpunit.de/phpunit.phar
chmod +x phpunit.phar
```

### Docker Test Database
```yaml
# Add to docker-compose.yml (or separate docker-compose.test.yml)
db-test:
  image: mysql:5.7
  environment:
    MYSQL_ROOT_PASSWORD: testpass
    MYSQL_DATABASE: authentication_test
  ports:
    - "3307:3306"  # Different port to avoid conflicts
```

### Mock LDAP Server Options
1. **phpLDAPadmin container** (easiest):
   - Pre-configured LDAP server with test data
   - Runs in Docker, simple to set up

2. **PHPUnit LDAP mock library**:
   - Mock LDAP backend without real server
   - Lightweight, good for unit tests

3. **Hybrid approach**:
   - Mock for unit tests (fast)
   - Real Docker LDAP for integration tests (thorough)

### Test File Structure Example
```php
<?php
// tests/Unit/LDAP/LDAPConnectionTest.php
namespace Tests\Unit\LDAP;

use PHPUnit\Framework\TestCase;

class LDAPConnectionTest extends TestCase
{
    public function testValidLDAPBind()
    {
        // Arrange
        $ldapServer = new MockLDAPServer();
        
        // Act
        $result = $ldapServer->bind('testuser', 'testpass');
        
        // Assert
        $this->assertTrue($result);
    }
}
?>
```

## Related Tasks
- Blocked by: None (this is foundational)
- Blocks: Login Test Suite, Deprecated MySQL Functions, Security Audit
- Related: EngineAPI Distillation (distillation can be tested via this framework)

## Stop Conditions
- If mock LDAP server setup proves complex, simplify to unit tests first, integration tests later
- If PHPUnit installation conflicts with EngineAPI, document workaround and flag for EngineAPI distillation task

## Notes
- **MySQL 8.0 confirmed (2026-06-18)**: Production verified running MySQL 8.0.46; dev environment updated to match. Test environment now aligns with production.
- **Critical for long-term maintenance**: Low-priority projects NEED testing to prevent regressions
- **Regression safety**: Once Login tests pass, any change to core functions must pass tests before merge
- **Branch strategy**: All feature branches MUST pass full test suite before merge to main
- **Team sustainability**: Testing infrastructure is reusable for future Authentication work
- **Foundation for distillation**: When EngineAPI distillation begins, tests ensure extracted code works
- **CI/CD ready**: Framework supports GitHub Actions (or other CI) integration for future automation
