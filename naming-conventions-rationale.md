# Naming Conventions — Rationale & Design Decisions

**Last updated:** Fri 13 Mar 2026
**Audience:** `for-kurt` — the "why" behind the conventions. The lookup table is in `templates/naming-conventions.md`.

This doc explains the reasoning behind each naming convention so future-Kurt (or anyone wondering "why do we do it this way?") has the full picture. The reference doc tells you *what* to do. This doc tells you *why*.

---

## Why System-Wide Conventions?

We started with naming conventions for Cowork project folders only. But Kurt and Claude work across multiple systems — Notion, Google Drive, GitHub, scheduled tasks — and each system has its own naming surface. Without a unified framework, every new system gets ad hoc conventions that may conflict.

The master naming conventions doc establishes **shared design principles** that govern all systems, then derives **system-specific rules** from those principles. This means you never have to ask "what case convention does Notion use?" from scratch — the answer flows from "Notion is a human-facing surface, so Title Case."

---

## Surface Type → Case Convention

The single most important design decision: **the surface determines the case convention, not the project or the content.**

**kebab-case for machine-facing surfaces.** Filenames, git repos, branch names, cron job identifiers. Why kebab-case specifically? It's the only case convention that's safe across every OS (Mac, Linux, Windows), every filesystem (APFS, ext4, NTFS), every CLI tool, and every git implementation. Spaces break CLIs. Underscores are invisible in some link renderers. CamelCase has ambiguity (is it "GitHub" or "Git Hub"?). Kebab-case has no edge cases.

**Title Case for human-facing surfaces.** Notion page titles, Google Doc titles, Google Drive folder names. These surfaces are browsed in a UI by humans, not parsed by machines. Title Case is natural to read and matches how these platforms present content natively. Forcing kebab-case onto Notion pages would look robotic.

**snake_case for structured data surfaces (TBD).** Database property names, API field names. This is tentative — we'll confirm when we design Notion database conventions. The reasoning: snake_case is the most common convention in database schemas and API responses, and it avoids the ambiguity of spaces in property names that Notion technically allows but causes headaches in formulas and API calls.

---

## Why No Version Numbers in Filenames

We went through three iterations on this:

1. **v1: Version everything.** Every file got `-v01`. Problem: most files sat at v01 forever. The version number was noise, not signal.
2. **v2: Conditional versioning.** Version numbers on structurally evolving files (design, guide, research), omitted for accumulation files (capture) and stable-structure lookups (glossary). Problem: required a judgment call at creation time, created inconsistency, and every structural rewrite triggered a rename that cascaded through cross-references.
3. **v3 (current): No version numbers anywhere.** Git tracks versions. Every file in the project lives in a git repo — `for-claude/`, `archimedes-mailbox/`, and root files go to the builder repo; `output/` goes to the public repo. `CLAUDE.md` is the only file not in git, and it's exempt from naming conventions anyway.

The deciding insight: **git does versioning better than filenames.** `git log --follow filename` gives you full history with diffs and commit messages — far more informative than a version bump from v02 to v03. And critically, git versioning doesn't require renaming the file, which means no cascading cross-reference updates.

The practical benefit: the filename pattern is now simply `[type]-[seq]-[topic]-for-[audience].md`. One pattern, no conditionals, no judgment calls.

---

## Why Flat Over Folders

Claude navigates files by making tool calls. Each folder level is an additional call: list the directory, pick a subfolder, list again, pick a file. A flat `for-claude/` directory with 15 type-prefixed files is one scan — Claude sees everything and routes instantly.

Subfolders are for **genuine workflow boundaries** (the `for-claude/` vs `output/` vs `archimedes-mailbox/` split), not for grouping within a workflow. The type prefix does the grouping work that subfolders would otherwise do, but without the navigation tax.

The math: a 40-file flat directory is ~4KB of listing. That's trivial for Claude to scan. The alternative — 5 folders with 8 files each — requires 6 tool calls (list root + list each subfolder) instead of 1.

---

## Why Audience Tags in Filenames

Could we infer the audience from the folder? Everything in `for-claude/` is for Claude, right? No — `for-claude/` can contain files written `for-kurt-and-claude` (like the roadmap, which Kurt engages with through conversation but Claude reads directly). The audience tag controls *writing style*, and that varies within a single folder.

The tag also serves as a **writing style trigger** — the same way the type prefix triggers optimization rules from the file type guide, the audience tag triggers a writing style. Seeing `for-claude` in a filename means: technical, structured, no narrative. Seeing `for-kurt` means: conversational, plain English. This is enforced at the naming level so the next Claude session knows how to write (or audit) the file before opening it.

---

## Why Single-Digit Sequence Numbers

Sequence numbers (`-1-`, `-2-`, `-3-`) do two things: control sort order within a type cluster, and leave room for future files.

**Single digit, not two:** if you've hit 10 files of one type, you have a consolidation problem, not a numbering problem. Ten guides means some of them should be merged or some are actually modules or references.

**Solo files still get `-1-`:** costs nothing in the filename, prevents a rename when a second file arrives. Without it, adding a second guide means renaming the first one from `guide-topic` to `guide-1-topic` — a file rename that propagates to every cross-reference.

---

## TBD Systems: What We Know So Far

The TBD sections (Notion, Google Drive, GitHub, Scheduled Tasks) aren't designed yet, but the design principles already constrain them:

- **Notion** will use Title Case for pages (human-facing) and likely snake_case for database properties (structured data). The hierarchy and nesting conventions need real project experience to design well.
- **Google Drive** will use Title Case for documents and folders. The PDF scanner pipeline (ScanSnap → Google Drive) needs its own naming pattern for intake.
- **GitHub** already has partial conventions (repo names follow `{project}` / `{project}-builder`). Branch naming and commit message conventions need design.
- **Scheduled Tasks** will use kebab-case for identifiers (machine-facing). The naming pattern will emerge when Marvin's cron job system is built.

Each system will be designed when a project actually needs it — not before. Designing conventions without real usage leads to conventions that don't fit.
