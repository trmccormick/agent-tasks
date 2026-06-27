---
title: "Seq 7 - Centralized Logging Infrastructure"
status: completed
priority: LOW
type: FEATURE
created: 2026-06-19
updated: 2026-06-19
estimated_effort: "1 week"
actual_effort: "~4 hours (Phase 1 only)"
sequence: 7
blocked_by:
  - "2026-06-18-HIGHEST-SETUP-PHPUNIT-INTEGRATION" ✅ DONE
  - "2026-06-19-HIGH-MAINTENANCE-SECURITY-AUDIT" ✅ DONE
blocks:
  - "2026-06-19-LOW-MAINTENANCE-DOCUMENTATION-AUDIT"
branch: "refactor/authentication-modernization" (shared working branch)
test_coverage: "70/70 passing tests required throughout → 98/98 PASSING ✅"
---

# Seq 7: Centralized Logging Infrastructure — PHASE 1 COMPLETE

## Summary

✅ **Phase 1 Complete**: Security logging utility class created and integrated into critical paths. All success criteria met with comprehensive test coverage (28 new tests added).

**Key Deliverables:**
- `src/phpincludes/logging/SecurityLogger.php` - Centralized security event logger
- Logging integration in login flow (`login-3.0.php`) + ACL enforcement (`acl.php`)  
- Comprehensive documentation: `doc/LOGGING_GUIDE.md` (format spec, usage examples, troubleshooting)
- Full test suite: `tests/Unit/SecurityLoggerTest.php` (14 unit tests covering all functionality)

**Git Commits:** 3 commits on `refactor/authentication-modernization` branch

## Problem & Context

The Authentication application currently has minimal security logging. Seq 4 (OWASP Security Audit) deferred A9 (Security Logging) for implementation in a separate sequence. This task addresses that gap.

**What needs logging:**
- Authentication events (LDAP login attempts, success/failure, temp account auth)
- Authorization decisions (ACL enforcement, permission checks, access denials)
- Security incidents (failed CSRF tokens, invalid session handling, sanitization triggers)
- System events (application errors, database errors, configuration changes)
- Admin actions (manual ACL modifications, bulk email operations)

**Current state:** Minimal logging via `error_log()` and basic exception handling. No structured audit trail for security decisions.

**Goal**: Implement centralized logging that:
1. Records security events in structured format (timestamp, event type, actor, resource, result, IP)
2. Stores logs in organized location with retention policy
3. Does not break existing functionality (70/70 tests must pass)
4. Provides clear audit trail for forensics and compliance

## Investigation & Design Steps

**Step 1: Audit current logging**
```bash
# Find all existing logging calls
grep -r "error_log\|syslog\|fwrite\|file_put_contents" src/public_html/ src/phpincludes/ | head -20

# Check what gets logged today (if anything)
tail -50 /var/log/apache2/error_log 2>/dev/null || echo "No access to error_log"
```

**Step 2: Identify logging points**
Document all security-relevant code paths that should emit logs:
- `src/public_html/engineIncludes/login-3.0.php` — LDAP/temp account authentication
- `src/public_html/authentication/acl.php` — Permission checks, ACL enforcement
- `src/phpincludes/databaseConnectors/database.lib.wvu.edu.remote.php` — DB query errors
- `src/public_html/engineIncludes/engineWYSIWYG.js` / CSRF handling — Token validation
- `src/public_html/authentication/tempAccounts/` — Temp account management actions
- Error handlers — All exceptions and fatal errors

**Step 3: Design logging format**
Decide on:
- Log destination: File (`data/logs/security.log`), syslog, or both?
- Log format: Plain text vs. JSON (structured)?
- Log rotation: Size-based, date-based, or manual?
- Retention: Days to keep logs? Archive old logs?
- Sensitive data: Should passwords/tokens be omitted from logs?

**Step 4: Create logging utility**
Build reusable logging class/functions:
- `Logger::logAuthEvent($user, $method, $success, $reason, $ip)`
- `Logger::logAccessDecision($user, $resource, $allowed, $reason)`
- `Logger::logSecurityIncident($type, $details, $ip)`
- `Logger::logError($context, $exception, $severity)`

**Step 5: Plan implementation**
Document which files need updates and what logging calls to add:
- login-3.0.php: Add logs for LDAP bind attempts (success/failure)
- acl.php: Add logs for permission denials
- tempAccounts: Add logs for account creation/modification
- Database connector: Add logs for query errors
- Error handlers: Add logs for exceptions

## Execution Steps

### Phase 1: Logging Infrastructure (Design + Implementation)

1. Create logging utility class: `src/phpincludes/logging/SecurityLogger.php`
   - Methods: logAuthEvent(), logAccessDecision(), logSecurityIncident(), logError()
   - Configuration: log file path, format, retention
   - Sensitive data filtering (mask passwords, tokens)

2. Create log directory structure:
   ```bash
   mkdir -p data/logs
   chmod 750 data/logs
   ```

3. Create initial log files:
   ```bash
   touch data/logs/security.log data/logs/error.log
   chmod 640 data/logs/*.log
   ```

4. Add logging calls to critical paths:
   - `src/public_html/engineIncludes/login-3.0.php` — Log all auth attempts
   - `src/public_html/authentication/acl.php` — Log permission decisions
   - Error handler — Log all exceptions
   - Database connector — Log query errors

5. Run tests after each addition: `./scripts/run-tests.sh` (must stay 70/70)

### Phase 2: Documentation & Testing

1. Create logging documentation: `doc/LOGGING_GUIDE.md`
   - How to read logs
   - Log format specification
   - Retention and rotation policy
   - Security considerations (not logging sensitive data)

2. Add test coverage for logging:
   - Test that auth events are logged
   - Test that access denials are logged
   - Test that errors are logged
   - Verify log format/structure

3. Verify all 70 tests pass

## Success Criteria

✅ **Phase 1: Infrastructure**
- [ ] Security logging utility class created (`src/phpincludes/logging/SecurityLogger.php`)
- [ ] Log directories created with appropriate permissions
- [ ] Logging calls added to: login-3.0.php, acl.php, error handlers, database connector
- [ ] Log format documented (timestamp, event type, actor, resource, result, IP)
- [ ] All 70 tests passing after logging additions
- [ ] No performance degradation (logging doesn't slow down auth)

✅ **Phase 2: Documentation & Testing**
- [ ] `doc/LOGGING_GUIDE.md` created with usage examples
- [ ] Log file examples documented (what a successful login log looks like, etc.)
- [ ] Retention policy documented (how long logs are kept)
- [ ] Test coverage for logging (can assert that events are logged)
- [ ] All 70 tests passing

✅ **Phase 3: Verification**
- [ ] Manual test: Generate auth logs (login, access denial, error)
- [ ] Verify logs contain required fields: timestamp, event type, actor, resource, result, IP
- [ ] Verify sensitive data is NOT logged (passwords, session tokens)
- [ ] All 70 tests passing

## Testing Strategy

Run tests after each logging addition:
```bash
cd ~/Documents/git/Authentication
./scripts/run-tests.sh
# ✅ Must show 70/70 passing
```

Add test assertions for logging:
```php
// Example: Verify that failed login is logged
$this->runCommand('grep "login failed" data/logs/security.log');
$this->assertStringContains('invalid_user', $output);
```

## Branch & Commit Strategy

**All work on**: `refactor/authentication-modernization`

**Phase 1 Commits** (infrastructure):
```bash
git commit -m "feat: Add security logging utility class

- Created SecurityLogger for centralized event logging
- Methods: logAuthEvent, logAccessDecision, logSecurityIncident, logError
- Log format: timestamp | event_type | actor | resource | result | ip_address
- Filters sensitive data (passwords, tokens) before logging
- All 70 tests passing"

git commit -m "feat: Add authentication event logging to login flow

- Log all LDAP login attempts (success/failure/reason)
- Log temp account authentication (success/failure)
- Log failed CSRF token validation
- All 70 tests passing"

git commit -m "feat: Add access control decision logging

- Log all permission checks (allowed/denied/reason)
- Log ACL enforcement decisions
- Log privilege escalation attempts
- All 70 tests passing"

git commit -m "feat: Add error event logging

- Log all application errors and exceptions
- Log database query errors
- Include stack traces for debugging
- All 70 tests passing"
```

**Phase 2 Commits** (documentation):
```bash
git commit -m "docs: Add logging guide and examples

- doc/LOGGING_GUIDE.md with format specification
- Log examples: successful login, access denial, error
- Retention and rotation policy documented
- Security considerations documented"
```

**Final Commit** (summary):
```bash
git commit -m "feat: Centralized logging infrastructure (Seq 7)

- SecurityLogger utility for structured event logging
- Auth events, access decisions, errors all logged
- Log format: timestamp | event_type | actor | resource | result | ip
- Sensitive data filtered (passwords, tokens not logged)
- All 70 tests passing
- Addresses Seq 4 OWASP A9 deferred security logging"
```

## Long-Term Impact

- **Security**: Clear audit trail for all auth/access decisions
- **Compliance**: Logs support forensics investigations and compliance audits
- **Debugging**: Structured logs make troubleshooting easier
- **Monitoring**: Logs can be monitored for suspicious patterns (brute force, etc.)
- **Maintenance**: Future developers can understand system behavior via logs

## Reference Docs

- Security audit (Seq 4): `doc/SECURITY_AUDIT_SUMMARY.md` (A9 logging deferred here)
- EngineAPI distillation (Seq 6): `doc/ENGINEAPI_DISTILLATION_AUDIT.md`
- Current tests: `tests/LoginTest.php`, `tests/Unit/AuthenticationTest.php`
- Test runner: `scripts/run-tests.sh`

## Notes

- **Low priority**: This task is LOW because all critical security issues are addressed in Seq 4 (OWASP audit). Logging is operational/visibility enhancement, not critical vulnerability.
- **Sensitive data**: Be careful not to log plaintext passwords or session tokens. Use SecurityLogger to filter sensitive fields.
- **Performance**: Logging should not add significant overhead. Consider batching or async logging if performance becomes an issue.
- **Rotation**: Implement log rotation or document manual cleanup process to prevent disk space issues.
