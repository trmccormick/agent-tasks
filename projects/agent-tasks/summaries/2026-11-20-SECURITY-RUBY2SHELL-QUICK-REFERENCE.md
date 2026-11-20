# CVE-2026-132825 (Ruby2shell) — Quick Reference Card

**Date**: 2026-11-20  
**Status**: Investigation Complete; Waiting for DevOps VM Snapshots  
**Thanksgiving Break**: Nov 25–28; Implementation resumes Dec 1+

---

## 🔴 The Problem (In 30 Seconds)

4 of your production Ruby on Rails apps use Ruby versions vulnerable to CVE-2026-132825, a critical heap overflow in string case conversion that can cause RCE.

**No wild exploitation yet, but PoCs exist.**

**Fix**: Upgrade each app's Ruby version to a patched release.

**Timeline**: Snapshots this week → Implementation Dec 1–4.

---

## ⚠️ BLOCKER: VM Snapshots Required First

**DevOps must take snapshots of all 4 production VMs BEFORE any upgrades happen.**

This is the critical blocker. Without snapshots, rollback if something breaks becomes difficult.

**Once snapshots confirmed**: Implementation can start immediately after Thanksgiving.

---

## 📋 The 4 Vulnerable Production Apps

| # | App | Current Ruby | Fix To | Status |
|---|-----|------------|--------|--------|
| 1 | library_directory | 3.3.5 | 3.3.14 | Snapshot pending |
| 2 | wvu_knapsack | 3.3.0 | 3.3.14 | Snapshot pending |
| 3 | hydra_acda_portal_public | 3.3.5 | 3.3.14 | Snapshot pending |
| 4 | databases | 3.3.4 (mismatch with Gemfile 3.4.10) | Clarify then fix | Snapshot pending |

**Plus**: hyku (Samvera upstream, not WVU's responsibility) is also vulnerable at 3.3.0.

---

## ✅ What's Done (TODAY)

- [x] Identified all 4 vulnerable apps
- [x] Created incident response task (ready for backlog)
- [x] Created technical findings document
- [x] Created executive summary for DevOps
- [x] Staged work for post-Thanksgiving handoff
- [x] All documents committed to git

---

## 🎯 Your Action Items (Next 24–48 Hours)

### IMMEDIATE (Next 1–2 hours):
1. [ ] Review the 3 committed documents (see file locations below)
2. [ ] Approve incident response task to move from drafts/ to backlog/

### THIS WEEK (Before Nov 25):
1. [ ] Send DevOps the executive summary (1-page, action-oriented)
2. [ ] Get DevOps to confirm VM snapshot schedule
3. [ ] (Optional) Notify Samvera team of hyku vulnerability

### AFTER THANKSGIVING (Dec 1+):
1. [ ] Confirm all VM snapshots are ready
2. [ ] Dispatch incident response task to implementation agent
3. [ ] Monitor upgrades; app owners handle post-deploy verification

---

## 📁 Document Locations

| Document | Purpose | Audience |
|----------|---------|----------|
| `projects/agent-tasks/tasks/drafts/2026-11-20-CRITICAL-SECURITY-RUBY2SHELL-CVE-2026-132825-URGENT-REMEDIATION.md` | Full incident response task; ready for review & approval | Planning/Implementation |
| `projects/agent-tasks/summaries/2026-11-20-SECURITY-RUBY2SHELL-INCIDENT-RESPONSE.md` | 1-page executive summary (send to DevOps) | DevOps, Execs |
| `projects/agent-tasks/summaries/2026-11-20-SECURITY-RUBY2SHELL-TECHNICAL-FINDINGS.md` | Detailed technical findings; full investigation log | Eng, Planning |
| `projects/agent-tasks/summaries/2026-11-20-SECURITY-RUBY2SHELL-QUICK-REFERENCE.md` | This file; quick reference | Everyone |

---

## 🗓️ Timeline

| Date | Action | Owner |
|------|--------|-------|
| Nov 20 (TODAY) | Investigation complete; docs committed | ✅ Done |
| Nov 20–22 | Review docs; approve task; request VM snapshots | You + DevOps |
| Nov 25–28 | Thanksgiving break | — |
| Dec 1–4 | Execute 4 Ruby upgrades (1 day each) | Implementation Agent |
| Dec 4 EOD | All apps patched and monitoring | Eng + Ops |

---

## 🛡️ Safety Net

Each app has a VM snapshot taken BEFORE upgrades begin.

If production breaks: **Instant rollback to snapshot.**

This is why DevOps action is critical BEFORE we touch anything.

---

## 🔧 What Implementation Looks Like (Dec 1+)

For each app (in this order: library_directory → databases → hydra → knapsack):

1. **Test locally** on personal dev clone
2. **Stage** in QA/test environment & run full test suite
3. **Monitor production** for 1 hour post-upgrade
4. **Confirm** no encoding/regression issues

**Estimated time per app**: 1 day  
**Total**: 4 days if all proceed smoothly

---

## 📞 Who to Contact

| Issue | Contact | Channel |
|-------|---------|---------|
| VM snapshots | DevOps team | Slack / Email |
| App testing | App owners (lib_dir, knapsack, hydra, databases) | Slack / Meeting |
| Samvera coordination | Samvera upstream team | GitHub issues / Mailing list |
| Task approval | Planning/Review agent | This chat |

---

## ⚡ Key Decisions Already Made

1. **Deployment order**: library_directory → databases → hydra → knapsack (easiest to hardest)
2. **Ruby versions**: Use 3.3.14 for 3.3.x apps; can also use 3.4.13 or 4.0.10 if preferred
3. **Encoding risk**: Assumed UTF-8 (safe); will verify during testing
4. **Upstream (hyku)**: Monitor Samvera for patch; don't patch directly
5. **Rollback**: VM snapshots mandatory (taken before Nov 25)

---

## 🚨 If Anything Goes Wrong

**VM snapshot available for instant rollback.**

Do NOT hesitate to rollback if:
- App doesn't start after upgrade
- Encoding/data corruption detected
- Performance regression
- Unexpected errors in logs

**Rollback procedure**:
1. DevOps reverts VM to snapshot
2. App restarts automatically
3. Continue with next app while investigating issue

---

## 📚 External Resources

- **CVE Details**: https://www.ruby-lang.org/en/news/2026/11/18/
- **Security Analysis**: https://www.ox.security/blog/ruby2shell-ruby-memory-corruption-flaws-expose-applications-to-remote-code-execution-but-is-it-an-actual-risk/
- **Ruby Releases**: https://cache.ruby-lang.org/pub/

---

## ✋ Questions or Changes?

If requirements change or new info emerges:
- Update this quick reference
- Update the technical findings document
- Notify all stakeholders in Slack

---

**Last Updated**: 2026-11-20 12:15 UTC  
**Investigation Status**: COMPLETE ✅  
**Next Phase**: DevOps VM snapshots (blocker); Implementation Dec 1+ (post-Thanksgiving)
