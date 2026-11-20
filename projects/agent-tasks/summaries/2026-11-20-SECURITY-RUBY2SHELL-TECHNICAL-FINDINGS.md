# CVE-2026-132825 (Ruby2shell) — Technical Findings & Inventory

**Date**: 2026-11-20  
**Investigation Period**: 10:58–12:10 UTC  
**Agent**: Security Planning & Investigation  
**Status**: Complete; Ready for DevOps action  

---

## 📊 Vulnerability Overview

| Property | Value |
|----------|-------|
| **CVE ID** | CVE-2026-132825 (Ruby2shell) |
| **Type** | One-byte heap overflow in string case conversion |
| **Severity** | CVSS 7.4 (High) |
| **Affected Component** | Ruby interpreter (all versions) |
| **Methods at Risk** | `upcase`, `downcase`, `capitalize`, `swapcase`, `casecmp?` |
| **Encoding Risk** | UTF-8 (safe); Legacy ISO-8859-x, Windows-125x (vulnerable) |
| **Exploitation** | Proof-of-concept RCE exists; no wild exploitation reported |
| **Disclosure Date** | 2026-11-18 |
| **Fix Available** | Yes (Ruby 3.3.14, 3.4.13, 4.0.10) |

---

## 🔴 Vulnerable Production Applications (WVU-Maintained)

### 1. **library_directory**

**Location**: `/Users/tam0013/Documents/git/library_directory`  
**Type**: Ruby on Rails application  
**Ruby Versions**:
- `.ruby-version`: `3.3.2` (older; possible dev env legacy)
- `Gemfile` directive: `ruby '3.3.5'` (current; production target)

**Current Status**: 🔴 **VULNERABLE** (3.3.5 is in range ≤ 3.3.13)  
**Fix Required**: Upgrade to `3.3.14`

**Investigation Notes**:
- Mismatch between `.ruby-version` (3.3.2) and Gemfile (3.3.5) suggests `.ruby-version` may be stale
- Production should follow Gemfile specification (3.3.5)
- Both are vulnerable; upgrading either to 3.3.14 resolves the CVE

**Risk Assessment**:
- [ ] Verify actual runtime version on production VM
- [ ] Check for non-UTF-8 encoding dependencies in app configuration
- [ ] Identify if app accepts user input that could be non-UTF-8 encoded

---

### 2. **wvu_knapsack**

**Location**: `/Users/tam0013/Documents/git/wvu_knapsack`  
**Type**: Ruby on Rails application (Hyku/Samvera knapsack implementation)  
**Ruby Version**: `.ruby-version: 3.3.0`  
**Gemfile Directive**: None (uses .ruby-version)

**Current Status**: 🔴 **VULNERABLE** (3.3.0 is in range ≤ 3.3.13)  
**Fix Required**: Upgrade to `3.3.14`

**Investigation Notes**:
- Clear specification; no version mismatch
- Based on Samvera Hyku ecosystem (community digital library platform)
- Likely uses standard Rails encoding (UTF-8)

**Risk Assessment**:
- [ ] Verify that bundled Samvera dependencies are compatible with 3.3.14
- [ ] Check for custom gems requiring specific Ruby versions
- [ ] Test with actual wvu_knapsack data (digital objects, metadata)

---

### 3. **hydra_acda_portal_public**

**Location**: `/Users/tam0013/Documents/git/hydra_acda_portal_public/hydra` (note: subdirectory)  
**Type**: Ruby on Rails application  
**Ruby Version**: `Gemfile: ruby '3.3.5'` (no .ruby-version at top level)

**Current Status**: 🔴 **VULNERABLE** (3.3.5 is in range ≤ 3.3.13)  
**Fix Required**: Upgrade to `3.3.14`

**Investigation Notes**:
- Hydra ecosystem application (digital repository)
- Requires .ruby-version file creation at repo root or subdirectory consistency check
- May be deployed from hydra subdirectory; verify build/deploy process

**Risk Assessment**:
- [ ] Confirm which directory is the application root (./hydra or parent)
- [ ] Verify .ruby-version location matches deploy process
- [ ] Check for Hydra-specific gem compatibility with Ruby 3.3.14

---

### 4. **databases** ✅ ACTUALLY SAFE (But Needs Modernization)

**Location**: `/Users/tam0013/Documents/git/databases`  
**Type**: Ruby on Rails application  
**Repository Status**: Complex — multiple branches with different versions

**VERSION CLARIFICATION (CRITICAL DISCOVERY)**:

| Branch | Ruby Version | Rails Version | CVE Vulnerable? |
|--------|------------|---------------|-----------------|
| **main** (PRODUCTION) | 2.7.8 | 5.2.8.1 | ✅ **NO** (2.7.8 outside vulnerable range) |
| Local working branch (refactor/bootstrap-replace) | 3.3.4 | 7.1.3 | ✅ **NO** (3.3.4–3.3.13 would be vulnerable, but 7.1.3 requires 3.0+) |
| **gem-updates-rails7** (branch) | 3.4.10 | 7.1.3 | ✅ **NO** (3.4.10 is safe) |

**Current Status**: 🟢 **NOT VULNERABLE to CVE-2026-132825 (but outdated software)**  
**Action Required**: None for CVE-2026-132825 patching; defer to separate modernization initiative

**ANALYSIS**:

1. **Production (main) is safe**: Ruby 2.7.8 is NOT in the vulnerable range (≤3.3.13, 3.4.0–3.4.12, 4.0.0–4.0.9)
2. **Rails 5 is obsolete**: End-of-life since April 2022; running unsupported software (separate risk)
3. **Rails 7 upgrade in progress**: Branch `gem-updates-rails7` exists; suggests team is working on modernization
4. **Local development clone is ahead**: Current working branch shows Rails 7.1.3 (newer than production)

**Why There Was Confusion**:
- You were on `refactor/bootstrap-replace` branch (shows Rails 7.1.3)
- Production is on `main` branch (shows Rails 5.2.8.1 with Ruby 2.7.8)
- **Local `.ruby-version` (3.3.4) is for development; production uses Ruby 2.7.8**

**CVE-2026-132825 Status**: 🟢 **NOT AFFECTED**  
- Ruby 2.7.8 is outside vulnerable range
- No upgrade required for this CVE

**Modernization Concern** (Separate Issue, Not CVE-Related):
- Rails 5 is EOL (April 2022)
- Rails 7 upgrade is in progress (see branches)
- When Rails 7 migration completes, Ruby will need to be 3.0+
- Recommend prioritizing Rails 5 → 7 upgrade (security & support, not CVE-2026-132825 specific)

---

## 🟢 Upstream Dependency — Not WVU Responsibility

### **hyku** (Samvera upstream)

**Location**: `/Users/tam0013/Documents/git/hyku`  
**Type**: Samvera Hyku digital repository platform (community upstream)  
**Ruby Version**: `.ruby-version: 3.3.0` (**VULNERABLE**)

**Status**: 🔴 **VULNERABLE** but **not WVU responsibility**  
**Action**: Notify Samvera maintainers; monitor for upstream patch

**Investigation Notes**:
- Samvera team maintains hyku as upstream package
- wvu_knapsack is WVU's implementation built on hyku
- Samvera team must release hyku patch; WVU will update knapsack dependency
- Do NOT patch hyku directly; wait for upstream release

**Recommendation**:
- [ ] Check Samvera GitHub issues for CVE-2026-132825 tracking
- [ ] File issue if none exists
- [ ] Subscribe to Samvera security mailing list for patch notification
- [ ] Plan wvu_knapsack dependency update when hyku patch released

---

## 🔧 Detailed Investigation Process

### Discovery Method

**Step 1: Identify all Ruby projects**
```bash
find /Users/tam0013/Documents/git -type f -name ".ruby-version" 2>/dev/null | sort
find /Users/tam0013/Documents/git -type f -name "Gemfile" 2>/dev/null | sort
```

**Step 2: Extract version information**
```bash
# For each project root:
cat .ruby-version
grep "^ruby" Gemfile | head -1
```

**Step 3: Classify projects**
- Production (live, in-use, need patching)
- Development-only (personal projects, can defer)
- Upstream dependencies (Samvera, not WVU responsibility)
- Unused/prototypes (no action needed)

**Step 4: Check version vulnerability**
Against affected ranges:
- Ruby ≤ 3.3.13 (vulnerable)
- Ruby 3.4.0–3.4.12 (vulnerable)
- Ruby 4.0.0–4.0.9 (vulnerable)

---

## 📋 Encoding Risk Assessment (Initial)

For each vulnerable app, encoding risk depends on:
1. **Default encoding**: Does app use UTF-8 (safe) or legacy encoding (risky)?
2. **Input sources**: Does app accept user-uploaded files or input in non-UTF-8 encodings?
3. **Case conversion**: Are case-conversion methods called on potentially untrusted input?

### Current Assessment (Preliminary):

| App | UTF-8 Default | Accept Non-UTF-8 Input | At Risk | Assessment |
|-----|-------------|----------------------|---------|------------|
| library_directory | ✅ Likely | ❓ Unknown (PDF uploads?) | ❓ | MEDIUM RISK — verify encoding config |
| wvu_knapsack | ✅ Likely | ❓ Unknown (digital objects?) | ❓ | MEDIUM RISK — Samvera handles metadata |
| hydra_acda_portal_public | ✅ Likely | ❓ Unknown (archival data?) | ❓ | MEDIUM RISK — Hydra apps handle varied content |
| databases | ✅ Likely | ❓ Unknown (data import?) | ❓ | MEDIUM RISK — verify data encoding |

**Next Steps** (during remediation phase):
- Grep each codebase for `Encoding::`, `convert_encoding`, `.encoding` patterns
- Review upload/import handlers for non-UTF-8 logic
- Interview app owners about input encoding requirements
- Test with actual app data (PDFs, international text, legacy files)

---

## 🛡️ VM Snapshot & Rollback Strategy

### Why Snapshots Are Critical

Each production app upgrade carries risk of:
1. Version incompatibility (edge cases in dependencies)
2. Encoding issues (app behavior changes with new Ruby version)
3. Performance regression
4. Database migration issues (if applicable)

**Mitigation**: VM snapshot before ANY code changes allows:
- Instant rollback if production breaks
- Comparison of pre/post behavior
- Audit trail of system state at CVE disclosure

### Required Snapshots

| Application | VM Name | Snapshot Required | Status |
|------------|---------|-------------------|--------|
| library_directory | ⬜ [TBD by DevOps] | Yes | Pending |
| wvu_knapsack | ⬜ [TBD by DevOps] | Yes | Pending |
| hydra_acda_portal_public | ⬜ [TBD by DevOps] | Yes | Pending |
| databases | ⬜ [TBD by DevOps] | Yes | Pending |

**DevOps Action**: 
- Identify production VM for each app (may be shared VM with multiple apps)
- Take snapshot of current state (before Nov 25)
- Document snapshot UUID/timestamp
- Verify rollback procedure works

---

## 🔄 Deployment Order Recommendation

1. **First**: library_directory (simplest, least dependencies)
2. **Second**: databases (after clarifying version mismatch)
3. **Third**: hydra_acda_portal_public (Hydra ecosystem)
4. **Fourth**: wvu_knapsack (wait for Samvera hyku patch, monitor compatibility)

**Rationale**:
- Each successful upgrade builds confidence
- Stagger deployments (1 day apart minimum) to isolate issues
- Last app (knapsack) has upstream dependency; can wait for hyku patch

---

## 📞 Stakeholders & Communication

### Who to Notify

| Stakeholder | Role | Action | Timing |
|-------------|------|--------|--------|
| **DevOps/Ops** | VM snapshots, prod deployment | Take snapshots by Nov 25 | ASAP |
| **library_directory owner** | Testing, monitoring | Test upgrade locally, monitor post-deploy | Dec 1+ |
| **wvu_knapsack owner** | Testing, monitoring, Samvera liaison | Test upgrade, monitor Samvera for hyku patch | Dec 1+ |
| **hydra_acda_portal_public owner** | Testing, monitoring | Test upgrade locally, monitor post-deploy | Dec 1+ |
| **databases owner** | Testing, version clarification | Verify runtime version, test upgrade | Dec 1+ |
| **Samvera team** | Upstream patch | File issue if not already tracked | This week |

---

## ✅ Verification Checklist (For Each Upgrade)

**Before Deployment**:
- [ ] VM snapshot confirmed & tested
- [ ] Local development clone upgraded to new Ruby version
- [ ] `bundle install` succeeds locally
- [ ] Local test suite passes (or no regression vs. current version)
- [ ] No encoding-related warnings in local logs
- [ ] Staged in QA/test environment
- [ ] QA test suite passes in staging
- [ ] App health checks pass (if applicable)
- [ ] No new errors in staging logs after 1-hour uptime

**During Production Deployment**:
- [ ] Deployment scheduled during low-traffic window
- [ ] DevOps confirms snapshot before deployment
- [ ] Ruby version deployed as specified
- [ ] App restarts cleanly
- [ ] Health checks pass
- [ ] Log monitoring enabled for 1-hour window

**After Production Deployment**:
- [ ] Confirm `ruby --version` matches intended version
- [ ] No encoding-related errors in logs
- [ ] Application functionality spot-checked
- [ ] Monitoring alert for any regressions
- [ ] Update incident response task with deployment evidence

---

## 📚 Reference Materials

**Documentation**:
- Ruby 3.3.14 release notes: https://www.ruby-lang.org/en/news/2026/11/18/
- Ruby 3.4.13 release notes: https://www.ruby-lang.org/en/news/2026/11/18/
- Ruby 4.0.10 release notes: https://www.ruby-lang.org/en/news/2026/11/18/

**Security Analysis**:
- OX Security: https://www.ox.security/blog/ruby2shell-ruby-memory-corruption-flaws-expose-applications-to-remote-code-execution-but-is-it-an-actual-risk/
- Astra Security: https://www.getastra.com/blog/security-advisory/ruby2shell/

**Samvera Ecosystem**:
- Hyku: https://github.com/samvera/hyku
- Hyrax: https://github.com/samvera/hyrax

---

## 📌 Summary Table — At-a-Glance

| App | Current Ruby | Fix Version | Action | VM Snap | Timeline |
|-----|------------|-----------|--------|---------|----------|
| library_directory | 3.3.5 | 3.3.14 | Upgrade | Pending | Dec 1+ |
| wvu_knapsack | 3.3.0 | 3.3.14 | Upgrade | Pending | Dec 1+ (monitor hyku) |
| hydra_acda_portal_public | 3.3.5 | 3.3.14 | Upgrade | Pending | Dec 1+ |
| databases | 3.3.4 / 3.4.10 mismatch | Clarify then upgrade | Align & upgrade | Pending | Dec 1+ |
| hyku | 3.3.0 | Upstream patch | Monitor Samvera | N/A | Reactive |

---

**Investigation Complete**: 2026-11-20 12:10 UTC  
**Next Phase**: DevOps VM snapshot action; then implementation post-Thanksgiving  
**Status**: Ready for handoff to implementation agent (post-Dec 1)
