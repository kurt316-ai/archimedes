# Auditor Feedback — [Project Name]

**Date:** [DDD DD MMM YYYY]
**Auditor session:** Cold read (no prior context for this project)
**Files reviewed:** [list key files read]

---

## Audit Type

Check one or more:
- [ ] **Substance** — Does the system work correctly? Are the design decisions sound?
- [ ] **Reliability** — Can a fresh Claude session follow the docs and be productive immediately?
- [ ] **Structure** — Is the file organization logical? Are naming conventions followed?

---

## Findings

### Critical (must fix)

| # | Finding | File(s) Affected | Suggested Fix |
|---|---|---|---|
| 1 | | | |

### Important (should fix)

| # | Finding | File(s) Affected | Suggested Fix |
|---|---|---|---|
| 1 | | | |

### Minor (nice to have)

| # | Finding | File(s) Affected | Suggested Fix |
|---|---|---|---|
| 1 | | | |

### Escalation Convention

Tag any finding that requires human judgment with **`[KURT ACTION]`** inline. This marks items Claude cannot resolve autonomously — architecture decisions, ambiguous intent, risk tradeoffs, or anything outside existing conventions. The builder session that reads this feedback should surface `[KURT ACTION]` items to Kurt before proceeding with fixes.

Apply `[KURT ACTION]` in any file where Claude records findings or decisions — not just audit feedback. It's a general escalation marker.

---

## Fresh-Session Standard Check

Could a brand new Claude session read this project's files and be productive immediately?

- [ ] CLAUDE.md / README exists and routes to the right files
- [ ] Roadmap reflects current state (phases, completed items, decisions)
- [ ] Glossary covers all non-obvious terms
- [ ] No files reference context that only exists in conversation
- [ ] Naming conventions are consistent across all files
- [ ] No stale references to deleted or renamed files

**Verdict:** [PASS / FAIL — with notes]

---

## Patterns Worth Sharing

Anything this project does well that other projects (or Archimedes itself) should adopt:

1. [Pattern description]

---

## Anti-Patterns Observed

Anything this project does that other projects should avoid:

1. [Anti-pattern description]

---

## Notes for Builder Session

Instructions for the builder session that reads this feedback:

- Review Critical findings first. These may be blocking.
- Important findings go to the improvement queue or roadmap.
- Minor findings can be batched into a cleanup pass.
- Patterns and anti-patterns should be captured in the project's lessons-learned file (tagged `for-archimedes` if broadly useful).

---

*File convention: `for-claude/capture-{seq}-audit-findings-for-claude.md` — follows standard naming. Sequence numbers distinguish multiple audits. Full audit checklist: `guides/audit-guide.md` in the Archimedes repo.*
