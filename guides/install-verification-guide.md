# Install Verification Guide — Archimedes QC for Project Installs

**Purpose:** Archimedes-side checklist for verifying a project's Archimedes install was done correctly. Run this from an Archimedes session — it reads the target project's folder and checks every convention.
**When to Load:** When Kurt says "check if {project} installed properly", "verify {project}'s setup", or during a full cleanup sweep (Section 10) for newly installed projects.
**Last Updated:** Sat 14 Mar 2026

---

## How This Differs from TEMPORARY-install-qc-checklist.md

| | Project-side (TEMPORARY) | Archimedes-side (this guide) |
|---|---|---|
| **Who runs it** | The project's own session, during install | An Archimedes session, after install |
| **Where it lives** | Copied into the project root, deleted when done | Permanently in Archimedes `output/guides/` |
| **Perspective** | Self-assessment ("did I do everything?") | External inspection ("did they do everything?") |
| **When** | During install | After install, on request, or during sweeps |

---

## How to Run

1. **Get the project's path** from the Archimedes project registry (in the private builder repo)
2. **Read the project folder** — Archimedes has read access to all project folders
3. **Run each section below**, checking files against Archimedes conventions
4. **Report to Kurt** — pass/fail summary, specific issues, recommended fixes
5. **Write to the project's `archimedes-mailbox/inbox.md`** if there are issues the project session should fix next time it runs

---

## Section 1: Folder Structure

- [ ] `for-claude/` exists and is flat (no subfolders)
- [ ] `archimedes-mailbox/` exists at project root
- [ ] `archimedes-mailbox/inbox.md` exists
- [ ] `archimedes-mailbox/outbox.md` exists
- [ ] No stray working files at project root that belong in `for-claude/`
- [ ] `TEMPORARY-install-qc-checklist.md` has been deleted (install is complete)

---

## Section 2: CLAUDE.md Router

Read the project's `CLAUDE.md` and verify all required sections from `templates/router-readme-template.md`:

- [ ] **"What This Project Is"** — one-sentence description + "This project follows Archimedes protocols" + Archimedes repo URL
- [ ] **Standing Jobs** — 8 outbound message types listed
- [ ] **Working Folder** — Mac path filled in (not a placeholder), GitHub repos listed
- [ ] **Working Folder** — git push block convention mentioned (`cd` to Mac path, `rm -f .git/index.lock .git/HEAD.lock`)
- [ ] **Folder Structure** — Two-Stack Model diagram present (customized for project)
- [ ] **Start Here** — ordered reading list with real file paths that exist
- [ ] **After Compaction** — gate instruction to run light cleanup before acting
- [ ] **Task Routing table** — at least 4 rows, file paths resolve to real files
- [ ] **Naming Convention** — references `[type]-[seq]-[topic]-for-[audience].md`
- [ ] **Writing Style** — all 4 audience styles listed
- [ ] **Archimedes Mailbox** — section with entry format
- [ ] **Autonomy Rules** — NACK-based flow, `[KURT ACTION]` tag, pre-authorized list
- [ ] **Key Principles** — first principles, intelligent pushback, research, primary reader = next Claude session

### Cross-reference check

- [ ] Every file referenced in Task Routing actually exists in the project folder
- [ ] Every file referenced in Start Here actually exists
- [ ] Folder structure diagram matches actual folder contents

---

## Section 3: Core Files

### Required files exist

| Check | File | Expected Location |
|---|---|---|
| [ ] | Overview doc | `{project}-overview-for-kurt.md` at root |
| [ ] | Roadmap | `for-claude/design-1-roadmap-for-claude.md` |
| [ ] | Cleanup checklist | `for-claude/guide-1-cleanup-checklist-for-claude.md` |
| [ ] | Glossary | `for-claude/ref-1-glossary-for-claude.md` |

### File quality spot-checks

- [ ] **Roadmap** has a "What's Next" section or equivalent priority table
- [ ] **Cleanup checklist** has Light/Full modes and a Section 0 meta-check
- [ ] **Glossary** includes universal Archimedes terms section (Two-Stack Model, Standing Jobs, NACK, etc.)
- [ ] **Overview doc** is `for-kurt` style (conversational, plain English)

---

## Section 4: Naming Conventions

- [ ] Every file in `for-claude/` follows `[type]-[seq]-[topic]-for-[audience].md`
- [ ] Type prefixes are valid: `design-`, `guide-`, `ref-`, `capture-`, `research-`, `module-`
- [ ] Audience tags are valid: `for-kurt`, `for-claude`, `for-kurt-and-claude`
- [ ] No version numbers in any filename
- [ ] Overview doc at root follows `{project}-[topic]-for-[audience].md`
- [ ] Exempt files use standard names: `CLAUDE.md`, `inbox.md`, `outbox.md`

---

## Section 5: Mailbox

- [ ] `archimedes-mailbox/outbox.md` has a `project-announce` entry
- [ ] `project-announce` entry has all required fields: What, Claude account, GitHub, Mac path, Tech stack, Archimedes compliance
- [ ] Entry status is `unread` (or `harvested` if Archimedes has already processed it)
- [ ] Entry date uses `DDD DD MMM YYYY HHMM PT` format
- [ ] `archimedes-mailbox/inbox.md` exists (can be empty)

### Registry sync

- [ ] Project appears in the Archimedes project registry (in the private builder repo)
- [ ] Registry entry matches project's actual state (phase, folder path, tech stack)

---

## Section 6: Security

- [ ] `.gitignore` exists at project root
- [ ] `.gitignore` excludes: `CLAUDE.md`, `.env`, `.env.*`
- [ ] `.gitignore` excludes overview doc from public repos
- [ ] No PII in any file — run PII search terms from the Archimedes builder repo against the project's `output/` folder (if it has one)
- [ ] No secrets, API keys, or credentials in any committed file

---

## Section 7: Bootstrap Cleanup

- [ ] No Archimedes `output/` or `archimedes-ref/` folder lingering in the project (these are bootstrap artifacts)
- [ ] No `START HERE` folders lingering
- [ ] No `TEMPORARY-install-qc-checklist.md` lingering
- [ ] No seed `CLAUDE.md` coexisting with the full router

---

## Reporting

### To Kurt

Summarize:
- **Total checks:** {count}
- **Passed:** {count}
- **Failed:** {list with specific issues}
- **Severity:** Critical (blocks project function) / Minor (convention violation, fixable)
- **Recommended actions:** What Kurt or the project session should fix

### To the project (via inbox)

If there are issues, write an entry to the project's `archimedes-mailbox/inbox.md`:

```markdown
## Entry — {DDD DD MMM YYYY HHMM PT}
**Status:** unread
**Type:** install-verification
**Topic:** Archimedes install QC results for {project name}
**Content:**
- **Checks passed:** {count}/{total}
- **Issues found:**
  1. {issue and fix}
  2. {issue and fix}
- **Action needed:** Fix these items during your next session. The cleanup checklist should catch them going forward.
```
