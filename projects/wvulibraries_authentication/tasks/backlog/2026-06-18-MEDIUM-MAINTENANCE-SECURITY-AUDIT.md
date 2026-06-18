---
title: "PHP Security Audit: Deprecated Patterns and Vulnerabilities"
status: backlog
priority: MEDIUM
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "2-3 weeks"
---

# Task: PHP Security Audit: Deprecated Patterns and Vulnerabilities

## Problem
The Authentication system is built on legacy PHP code (some dating to PHP 4/5 era). Running on PHP 8.3 requires review for:

- **Deprecated functions**: ereg_*, split(), preg_* without flags, etc.
- **Weak input validation**: Global superglobals without sanitization
- **LDAP injection risk**: LDAP bind strings not properly escaped
- **Session security**: No CSRF token regeneration on login
- **Password handling**: Plaintext password transmission (LDAP only, but worth checking)
- **SQL injection risk**: Remaining mysql_* functions and hand-crafted queries
- **XSS vulnerabilities**: htmlspecialchars() usage, sanitization levels
- **Weak random**: Old rand() calls (should be random_bytes())

## Scope
**Security review areas**:
1. **LDAP authentication flow** (src/phpincludes/engineAPI/engine/login/ldap.php)
   - LDAP injection prevention (currently using sAMAccountName binding — OK)
   - LDAP filter escaping (check for wildcards, special chars)

2. **Input validation** (entire application)
   - Login form (username/password)
   - GET/POST parameter handling
   - File uploads (if any)

3. **Cryptographic functions**
   - Session ID generation
   - Token generation
   - Password handling

4. **Output encoding**
   - HTML entity encoding
   - JavaScript escaping
   - URL encoding

5. **Error handling**
   - Information disclosure (error messages)
   - Logging of sensitive data

## Success Criteria
- [ ] Security audit report completed
- [ ] OWASP Top 10 checklist filled out
- [ ] High/Critical findings have mitigation plans
- [ ] No hardcoded credentials or secrets found
- [ ] Session handling validated
- [ ] LDAP binding validated
- [ ] Recommendations documented with priority levels

## Related
- Deprecated MySQL functions (SQL injection vectors)
- Login functionality testing
- EngineAPI review (engine-level security)

## Notes
- Use security audit tools: PHP CodeSniffer with security rules, PHPStan, SonarQube if available
- Focus on LDAP authentication (core functionality) and login page (attack surface)
- Document findings for future secure code guidelines
- Consider third-party security audit if high sensitivity determined
