# {PROJECT_NAME} — TEMPORARY Install QC Checklist

**Created:** {DDD DD MMM YYYY HHMM PT}
**Purpose:** Verify Archimedes bootstrap completed correctly. Punch through each item as you complete the corresponding start-here.md step. Delete this file when all items pass.
**Delete when done:** This file is temporary scaffolding. Once all checks pass and Kurt confirms, delete it.

---

## How to Use

1. **Create this file first** — before starting `start-here.md`
2. **Mark items as you go** — change `[ ]` to `[x]` after each step completes
3. **Self-audit at the end** — re-read each file you created and compare against the template source
4. **Report to Kurt** — summarize pass/fail, flag anything you couldn't verify
5. **Delete this file** — it's done its job

---

## Section 0: Pre-Setup

- [ ] **Archimedes access verified** — files found via: _{local `output/` folder / cloned to `archimedes-ref/` / Kurt ran clone command}_
- [ ] `start-here.md` located and read. Path: _{write path here}_
- [ ] Determined project type: greenfield / brownfield
- [ ] `archimedes-mailbox/outbox.md` created (this happens before anything else per seed CLAUDE.md Step 2)

---

## Section 1: Project Understanding (start-here.md Step 1)

- [ ] Project one-liner captured: _{write it here}_
- [ ] End product identified: _{write it here}_
- [ ] Tools/platforms identified (or "TBD — brain dump will clarify"): _{write it here}_
- [ ] Complexity level: _{simple / multi-session / large}_
- [ ] Greenfield confirmed (or brownfield → switch to CLAUDE-update.md path)
- [ ] Brain dump / idea bin needed: _{yes / no}_

---

## Section 2: Folder Structure (start-here.md Step 2)

### Directories exist

- [ ] `for-claude/` — flat, no subfolders
- [ ] `archimedes-mailbox/` — at project root level

### Directory rules

- [ ] `for-claude/` contains only `.md` files with correct naming convention
- [ ] No stray files at project root that should be in `for-claude/`

---

## Section 3: Core Files (start-here.md Step 3)

### Universal files — verify each exists AND matches its template

| Check | File | Location | Template Source | Status |
|---|---|---|---|---|
| [ ] | Overview doc | `{project}-overview-for-kurt.md` (root) | No template — written from Step 1 | _{created / skeletal / deferred to idea bin}_ |
| [ ] | Roadmap | `for-claude/design-1-roadmap-for-claude.md` | `templates/roadmap-template.md` | _{created / skeletal / deferred to idea bin}_ |
| [ ] | Cleanup checklist | `for-claude/guide-1-cleanup-checklist-for-claude.md` | `templates/cleanup-checklist-template.md` | |
| [ ] | Glossary | `for-claude/ref-1-glossary-for-claude.md` | `templates/glossary-template.md` | |
| [ ] | Mailbox inbox | `archimedes-mailbox/inbox.md` | `templates/archimedes-mailbox-message-template.md` | |
| [ ] | Mailbox outbox | `archimedes-mailbox/outbox.md` | `templates/archimedes-mailbox-message-template.md` | |

### Conditional files

| Check | File | Needed? | Status |
|---|---|---|---|
| [ ] | Idea bin (`for-claude/capture-1-idea-bin-for-kurt-and-claude.md`) | _{yes / no}_ | |
| [ ] | Constraints doc | _{yes / no}_ | |

### Naming convention audit

- [ ] Every file in `for-claude/` follows `[type]-[seq]-[topic]-for-[audience].md`
- [ ] Type prefixes used correctly: `design-`, `guide-`, `ref-`, `capture-`, `research-`, `module-`
- [ ] Audience tags used correctly: `for-kurt`, `for-claude`, `for-kurt-and-claude`
- [ ] No version numbers in any filename
- [ ] Overview doc at root follows `{project}-[topic]-for-[audience].md`

---

## Section 4: CLAUDE.md Router (start-here.md Step 4)

Verify the project CLAUDE.md contains all required sections from `templates/router-readme-template.md`:

- [ ] **"What This Project Is"** — one-sentence description + "This project follows Archimedes protocols" + repo URL
- [ ] **Standing Jobs** — 8 outbound message types listed, "write when you spot them" instruction
- [ ] **Working Folder** — Mac path filled in, GitHub repos listed (or "not yet created")
- [ ] **Folder Structure** — Two-Stack Model diagram (customized if project doesn't have Stack 2 yet)
- [ ] **Start Here** — ordered reading list (roadmap → cleanup checklist → glossary → mailbox → notes)
- [ ] **After Compaction** — gate instruction to run light cleanup (Sections 0–4) before acting
- [ ] **Task Routing table** — at least 4 rows mapping task types to files
- [ ] **Naming Convention** — references `[type]-[seq]-[topic]-for-[audience].md` + full spec link
- [ ] **Writing Style** — all 4 audience styles listed (`for-claude`, `for-kurt`, `for-kurt-and-claude`, `for-archimedes`)
- [ ] **Archimedes Mailbox** — section with inbox/outbox description + entry format
- [ ] **Autonomy Rules** — pre-authorized actions listed + NACK-based flow + "when to ask" + `[KURT ACTION]` tag
- [ ] **Key Principles** — first principles, intelligent pushback, research when it matters, primary reader = next Claude session
- [ ] Seed CLAUDE.md has been **replaced** (not still present alongside the router)

---

## Section 5: Mailbox & Outbox (start-here.md Step 10)

- [ ] `project-announce` entry exists in `archimedes-mailbox/outbox.md`
- [ ] Entry has all required fields: What, Claude account, GitHub, Mac path, Tech stack, Archimedes compliance
- [ ] Entry status is `unread`
- [ ] Entry date uses correct format: `DDD DD MMM YYYY HHMM PT`
- [ ] Any setup gaps, tool insights, or convention conflicts also written as outbox entries

---

## Section 6: Build-Decision-Tree (start-here.md Step 5)

- [ ] Complexity level determined → correct setup path followed
- [ ] Claude interface modules identified and documented in CLAUDE.md (Cowork is default — note it explicitly, plus others if applicable)
- [ ] Tech stack modules identified (or "TBD — pre-build")
- [ ] Missing modules noted as `module-request` outbox entries

---

## Section 7: Security (start-here.md Step 8)

Create `.gitignore` now even if git hasn't been initialized — it's a specification of what will be excluded, not a git operation. Standard entries: `CLAUDE.md`, `.env`, `.env.*`, `output/` (from builder repo), overview doc (from public repos).

- [ ] `.gitignore` created with standard exclusions
- [ ] CLAUDE.md listed in `.gitignore`
- [ ] Overview doc listed in `.gitignore`
- [ ] `.env` / `.env.*` listed in `.gitignore`
- [ ] No secrets in any file

---

## Section 8: Cleanup — Bootstrap Artifacts

These items were needed during setup but should not persist in the project folder permanently. **Timing note:** If the project is about to run the idea bin synthesis pipeline, keep `output/` until synthesis is complete (you'll need `guides/idea-bin-synthesis-guide.md`). Tell Kurt which artifacts are deferred and why.

- [ ] `START HERE - no CLAUDE.md yet/` folder removed (if present) — `[KURT ACTION]` delete manually
- [ ] `START HERE - already have CLAUDE.md/` folder removed (if present) — `[KURT ACTION]` delete manually
- [ ] Archimedes reference files (`output/` or `archimedes-ref/`): _{removed / deferred until after idea bin synthesis / not applicable}_. `[KURT ACTION]` delete when ready.
- [ ] No other Archimedes bootstrap artifacts lingering
- [ ] `TEMPORARY-install-qc-checklist.md` itself — delete after Kurt confirms (this is expected to still be here)

---

## Section 9: Final Self-Audit

Re-read each file you created. For each one, verify:

- [ ] Content matches the structure of its Archimedes template source
- [ ] Writing style matches the audience tag (`for-claude` = structured/technical, `for-kurt` = conversational)
- [ ] No placeholder text left unfilled (search for `{` characters)
- [ ] Dates use `DDD DD MMM YYYY HHMM PT` format
- [ ] Cross-references between files are correct (e.g., CLAUDE.md task routing points to files that exist). For files deferred to idea bin synthesis (roadmap, overview), verify CLAUDE.md caveats these as `[created at synthesis]` or similar.

---

## Summary

**Total checks:** _{count}_
**Passed:** _{count}_
**Failed/deferred:** _{list any that didn't pass and why}_
**Outbox entries written during install:** _{count}_

**Reported to Kurt:** [ ] Yes / [ ] No

---

_Delete this file after Kurt confirms the install looks good._
