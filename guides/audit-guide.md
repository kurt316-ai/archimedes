# Archimedes — Project Audit Guide

**Audience:** `for-claude`
**Purpose:** Gives a cold-start auditor session a concrete checklist for evaluating any Archimedes-managed project. The auditor has zero prior context — this guide tells it what to look for and how to report findings.
**When to Load:** Before running any audit (cold-read, self-audit, or reconciliation).
**Last updated:** Sat 14 Mar 2026

---

## The Three-Question Frame

Every audit validates the three-layer model (see `ref-5-kurt-design-principles-for-claude.md`, Principle #4). The three questions, in order:

**Q1. Is the design principle correct?** — Human layer. Does each principle, SOP, or convention actually solve the problem it claims to? A wrong principle propagated faithfully to 8 files is worse than a right principle missing from a few. Test on merit (Principle #1).

**Q2. Do we know the exact translation?** — Translation layer mapping. For each principle, what exactly should each downstream file say, given its file type and optimization contract? Is the 1-to-many mapping complete? The SOP registry (`ref-6`) is the primary validation tool.

**Q3. Is the translation machinery working — and is the output high quality?**
- **Triggers:** Is the right machinery firing at the right time? Manual triggers (propagation targets tables at write time, SOP registry lookups). Automated cadences (cleanup checklist sections, reconciliation sweeps).
- **Output quality:** Is the Claude-facing layer conforming to markdown best practices — effective (right behavior, right context loaded) AND efficient (minimal tokens, right file type, front-loaded, earns its place)?

**The causal chain:** Q1 wrong → everything downstream wrong. Q1 right + Q2 incomplete → files missing or mis-encoded. Q1 + Q2 right + Q3 triggers broken → files drift. Q1 + Q2 + Q3a right + Q3b poor → files exist but waste context budget.

The tactical checklist sections below map to these questions:

| Question | Sections |
|----------|----------|
| Q1 — Principle correct? | 5 (Roadmap vs Reality), 6 (Writing Style) |
| Q2 — Translation complete? | 3 (Naming Compliance), 4 (Cross-Reference Integrity), 10 (Template Compliance) |
| Q3 — Machinery working + output quality? | 1 (Cold-Start Navigation), 2 (Folder Structure), 7 (Security), 8 (Mailbox & Standing Jobs), 9 (Stale Terminology), 11 (Markdown Quality) |

---

## When to Audit

| Trigger | Audit Type |
|---------|-----------|
| Before first real deployment or public release | Full (all sections) |
| After completing a major milestone (3+ roadmap items done) | Full |
| Before standing up Archimedes in a new project (greenfield or brownfield) | Structure-only (Sections 1–4) |
| Kurt requests it | Whatever Kurt specifies |
| Recurring cadence (quarterly or after batch convention changes) | Full |

**Who audits:** A separate Claude session with zero project context. The auditor reads only what's on disk — no conversation history, no builder bias. Use Opus for audits (needs to catch subtle issues).

**How to start:** Open the project folder in a new Cowork session (or use a subagent with Read/Grep/Glob tools). Read the project's CLAUDE.md first, then follow the checklist below.

---

## Audit Checklist

Work through these in order. For each section, record findings in the audit feedback file using the template at `templates/auditor-feedback-template.md`. Be specific — quote filenames, line numbers, and exact discrepancies.

### 1. Cold-Start Navigation

Starting from CLAUDE.md alone:

- [ ] Can you determine what this project is and what it produces?
- [ ] Can you find the roadmap, cleanup checklist, and overview doc from CLAUDE.md references?
- [ ] Do all file paths in CLAUDE.md resolve to real files?
- [ ] Is the task routing table accurate — does each route point to a file that exists and covers what the route claims?
- [ ] Does CLAUDE.md have Standing Jobs, After Compaction gate, and Autonomy Rules?

### 2. Two-Stack Folder Structure

- [ ] Does the actual folder structure match what's documented in CLAUDE.md?
- [ ] Are there files in the wrong folder? (builder content in `output/`, deliverables in `for-claude/`)
- [ ] Is `for-claude/` flat (no subfolders)?
- [ ] Does `archimedes-mailbox/` exist at project root with `inbox.md` and `outbox.md`?
- [ ] Is `CLAUDE.md` at the project root (not inside either subfolder)?

### 3. Naming Convention Compliance

Read `output/templates/naming-conventions.md` (from the Archimedes repo) first. Then check:

- [ ] Do all `for-claude/` files follow `[type]-[seq]-[topic]-for-[audience].md`?
- [ ] Are sequence numbers logical within each type prefix?
- [ ] Are there any version numbers in filenames? (There shouldn't be.)
- [ ] Do `output/` files follow principle #7 — subfolders organize, no type prefixes, no audience tags?
- [ ] Are exempted files (`CLAUDE.md`, `README.md`, `inbox.md`, `outbox.md`) the only ones that break the pattern?

### 4. Cross-Reference Integrity

For every internal file reference across all files:

- [ ] Does the referenced file exist at the stated path?
- [ ] Is the description of what the referenced file contains accurate?
- [ ] Are there orphaned files (files nothing points to)?
- [ ] Are there circular references that could confuse a cold-start session?

### 5. Roadmap vs. Reality

Read the roadmap:

- [ ] Do items marked `done` correspond to files/features that actually exist and are complete?
- [ ] Do items marked `not-started` correspond to things that genuinely don't exist yet?
- [ ] Are dependency chains logical?
- [ ] Is the Decision Log consistent with what the files actually say?
- [ ] Are there decisions referenced in files that aren't in the Decision Log?
- [ ] Are Active Item Detail entries current (not stale for done items)?

### 6. Writing Style Compliance

Spot-check 2–3 files of each audience type present in the project:

- [ ] Do `for-claude` files read as technical, structured, no-narrative?
- [ ] Do `for-kurt` files read as conversational, plain English?
- [ ] Do `for-kurt-and-claude` files balance both?
- [ ] Are there files whose writing style doesn't match their audience tag?

### 7. Security Check

If the project has a public repo, run the pre-push security checklist from `output/guides/security-conventions-guide.md`:

- [ ] Any PII matches from the project's PII search terms list (in the private builder repo)? Search for family names, home nicknames, personal emails, addresses, vehicles.
- [ ] Any other real personal data?
- [ ] Any references to private repo structure or filenames?
- [ ] Any PII in files destined for public repos?

### 8. Mailbox & Standing Jobs

- [ ] Does CLAUDE.md have a Standing Jobs section?
- [ ] Does the cleanup checklist include a mailbox check?
- [ ] Is the mailbox entry format consistent with the template?
- [ ] Are there any `unread` entries in inbox or outbox that haven't been processed?

### 9. Stale Terminology

Search all files for:

- [ ] `{project}-builder/` as a folder path (should be `for-claude/`)
- [ ] Version numbers in filenames (`-v01`, `-v02`)
- [ ] Old folder names that have been renamed
- [ ] References to files that have been deleted or merged

### 10. Template Compliance

For each template the project uses (roadmap, cleanup checklist, glossary, CLAUDE.md router):

- [ ] Does the project's instance follow the structure the template prescribes?
- [ ] Are there required sections missing?
- [ ] Flag any "do as I say, not as I do" inconsistencies

### 11. Markdown Quality (Q3b — Output Quality)

For each `for-claude` file, validate against markdown architecture principles and file type optimization guide:

- [ ] Does the file have Purpose, When to Load, and Last Updated in its front matter?
- [ ] Is the file within its type's token budget? (Routers <1K, guides 1K–3K, design surfaces 3K–5K, references up to 5K)
- [ ] Is critical content front-loaded (not buried in the middle)?
- [ ] Is the writing declarative and terse — no narrative, no filler?
- [ ] Does the file type prefix match its actual behavior? (A file that keeps growing might be a capture pretending to be a guide)
- [ ] Are there tokens that don't earn their place — content that serves the human but wastes Claude's context, or content efficient for Claude but serving no human need?
- [ ] Is information in the right file? (Does it load only when needed, or is it forced always-on?)

For routers specifically:
- [ ] Does the router only route, or does it also store? (Any content beyond identity + routing table is bloat)

For capture files specifically:
- [ ] Has the file been harvested recently? (Check "last harvested" date)
- [ ] Are entries consistently formatted (date, tag, content)?

---

## Output

1. Write findings to a `capture-` file in `for-claude/` using the auditor feedback template: `capture-{seq}-audit-findings-for-claude.md`
2. For issues you can fix without changing architecture — fix them and note the fix
3. For issues that need Kurt's input — tag with `[KURT ACTION]`
4. Write a summary entry to `archimedes-mailbox/outbox.md` with status `unread` — this ensures Archimedes sees cross-project patterns

---

## What the Builder Session Does with Audit Feedback

The builder session checks for audit feedback files at the start of each session (see cleanup checklist Section 4, Audit Feedback Check).

1. Read any new `capture-*-audit-findings-for-claude.md` files
2. Address Critical findings first (may be blocking)
3. Add Important findings to the roadmap queue or improvement backlog
4. Batch Minor findings into the next cleanup pass
5. Write patterns and anti-patterns to the lessons-learned file (tagged `for-archimedes` if broadly useful)
