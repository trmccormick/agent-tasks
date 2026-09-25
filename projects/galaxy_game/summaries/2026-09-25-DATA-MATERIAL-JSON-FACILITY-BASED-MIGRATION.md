## STATUS SYNTHESIS REPORT

**Task**: 2026-09-24-MEDIUM-DATA-MATERIAL-JSON-FACILITY-BASED-MIGRATION
**Status**: backlog → active
**Date**: 2026-09-25

### What I'm About to Do
Re-confirm the offender list from the architecture synthesis, migrate epoxy_resin.json as Phase A exemplar, then remaining offenders in Phase B after human approval. No service code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| summaries/2026-09-24-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md | Contract + audit | done |
| epoxy_resin.json and other offenders | Migration targets | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ (git mv), status: active, one copy verified
- ✅ Read contract synthesis (summaries/2026-09-24-ARCHITECTURE-MATERIAL-DATA-CONTRACT-FACILITY-BASED.md)
- ✅ Understand Phase A then Phase B

### Expected Outcomes
- Zero location-keyed sourcing blocks in materials tree
- Zero body-named production keys in offenders
- Valid JSON; no acquisition service edits

### Critical Gotchas I Will Avoid
- ❌ Service rewrites — instead ✅ JSON only
- ❌ Edit all 207 files — instead ✅ offenders only
- ❌ Skip Phase A approval — instead ✅ stop after exemplar

---
**SYNTHESIS COMPLETE.** Ready for Phase A after human OK on synthesis.
