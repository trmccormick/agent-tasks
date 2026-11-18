# Task Compliance Review Template

**Use this template for initial task compliance review (all reviewers: M365 Copilot, Perplexity, Gemini).**

This is a **simple prompt** that points reviewers to the standard guide. Replace `[BRACKETS]` with actual values.

---

## PROMPT

```
Review the following [NUMBER] draft task files using the standard compliance review process.

Review Package (commit [COMMIT_HASH]):
- Task file(s): [TASK_FILE_1], [TASK_FILE_2], etc.
- TASK_TEMPLATE.md (provided below)
- REVIEW_AGENT_GUIDE.md (provided below — follow "Reviewing Draft Tasks for Scope Approval" section)

Use REVIEW_AGENT_GUIDE.md to conduct Phase A (template conformance) and Phase B (content assessment).

Return your disposition: GO, REVISE, or STOP.

---

TASK FILES

[PASTE: task_file_1.md]

[PASTE: task_file_2.md]

---

TASK_TEMPLATE.md

[PASTE: TASK_TEMPLATE.md]

---

REVIEW_AGENT_GUIDE.md

[PASTE: REVIEW_AGENT_GUIDE.md]
```

---

## NOTES

- **Files provided inline**: No GitHub access; all files pasted directly
- **Instructions in guide**: REVIEW_AGENT_GUIDE.md contains Phase A and Phase B procedures
- **Report format**: REVIEW_AGENT_GUIDE.md specifies the required report structure
- **Keep it simple**: Prompt should be basic; complexity lives in the guide
