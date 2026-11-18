# M365 Copilot Task Compliance Review Template

**Use this template for initial task compliance review by M365 Copilot (WVU/Samvera projects).**

Replace all `[BRACKETS]` with actual values. Keep the structure and style consistent.

---

## REVIEW OBJECTIVE

**Reviewer**: M365 Copilot  
**Task**: Initial compliance review of [NUMBER] draft task file(s)  
**Scope**: Template conformance, structure, clarity — NOT implementation or content depth  
**Review Package** (GitHub commit `[COMMIT_HASH]`):
1. Task file(s): [GITHUB_PATH_1], [GITHUB_PATH_2]
2. `TASK_TEMPLATE.md`
3. `REVIEW_AGENT_GUIDE.md`

---

## REVIEW INSTRUCTIONS

### Phase A — Template Conformance (Mandatory)

Compare each draft task against `TASK_TEMPLATE.md`. Check for:

**YAML Frontmatter**
- [ ] `resume:` field present
- [ ] `system_domain:` field present
- [ ] `mvp_alignment:` field present
- [ ] `local_worker_safe:` field present
- [ ] `status: draft` (if in drafts folder)

**Draft-Only Sections** (required when status: draft)
- [ ] DRAFT STATUS NOTE section present
- [ ] DRAFT REVIEW CHECKLIST section present

**Core Sections**
- [ ] Full Agent Dispatch Interface block (complete, not abbreviated)
- [ ] Task Readiness Checklist section present
- [ ] All required subsections per TASK_TEMPLATE.md

**Blockers to Flag as REVISE**
- Missing YAML fields
- Missing draft-only sections
- Abbreviated or custom dispatch interface
- Leftover `[PLACEHOLDER]` text (except allowed `<agent-tasks>` substitution)
- Pre-assigned worker without human authorization statement

### Phase B — Content Assessment (optional depth beyond template)

If template passes, optionally assess:
- Acceptance criteria clarity (measurable? verifiable?)
- Scope boundaries (focused? no scope creep?)
- Dependencies clearly stated
- File paths accurate (spot-check 2-3 paths in repo)
- No internal contradictions between sections

---

## DISPOSITION OPTIONS

**GO** — Template compliant; ready for human approval to move to active/ and dispatch  
**REVISE** — Template deficiencies or content issues must be fixed; specify exactly what  
**STOP** — Critical authority or scope issues; human guidance required before proceeding

---

## REQUIRED REPORT FORMAT

```markdown
# DRAFT TASK COMPLIANCE REVIEW

## Task Identification
- Task file(s): [FILENAMES]
- Repository: [REPO]
- Review date: [DATE]
- Reviewer: M365 Copilot

## 1. Disposition
**[GO | REVISE | STOP]**

[1-2 sentence rationale]

## 2. Phase A — Template Conformance
- Result: **PASS** | **FAIL**
- [List any missing/incorrect sections with evidence]

## 3. Phase B — Content Assessment (if assessed)
[Optional: spot-check findings on clarity, scope, dependencies, paths]

## 4. Findings
[Number each finding; include Severity, Section, Evidence, Why it matters, Specific correction]

## 5. Recommended Changes
- **Mandatory corrections** (before approval)
- **Optional improvements** (can defer)

## 6. Final Recommendation
[State whether tasks should be approved for active/, revised and re-reviewed, or held for human guidance]
```

---

## NOTES

- **GitHub Access**: You have read-only access to the linked repository
- **No Edits During Review**: Review only; do not make changes
- **Template is Authority**: TASK_TEMPLATE.md defines requirements; use it as the standard
- **Evidence Matters**: Quote or reference specific sections for every finding
- **Clear Disposition**: One of three only (GO/REVISE/STOP); no ambiguous middle ground

