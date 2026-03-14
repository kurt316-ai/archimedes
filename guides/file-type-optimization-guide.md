# File Type Optimization Guide

**Purpose:** Optimization targets, structure, and failure modes for each markdown file type. Apply when creating, reviewing, or restructuring files.
**Audience:** `for-claude`
**Companion doc:** `archimedes-system-guide-for-kurt.md` (project root) Part 2 has human-readable explanations.
**Prerequisite:** Read `markdown-architecture-guide.md` first — general principles apply to all types. This guide adds type-specific constraints.
**Last updated:** Sat 14 Mar 2026

## Core Principle: Type Prefix = Optimization Contract

In the `for-claude/` folder, every filename starts with a type prefix: `design-`, `guide-`, `ref-`, `module-`, `capture-`, `research-`. This prefix does double duty:

1. **Routing** — Claude and Kurt scan the flat folder and jump to the right file cluster instantly.
2. **Optimization contract** — the prefix tells you which section of this guide governs how the file should be written, read, and maintained. Seeing `guide-` in a filename triggers the Guide rules below before the file is opened.

**Cross-reference:** `naming-conventions.md` defines the full filename pattern. This guide defines what each type means and how to optimize it.

## When to Load

- Creating a new file (identify the type first, then follow its structure)
- Restructuring an existing file
- Running a reconciliation cycle or audit
- File feels wrong but you can't articulate why (check: is it the wrong type?)
- Naming a file (the type prefix must match the type's optimization rules)

---

## The Seven Types + Two Working Types

Seven structural file types define the permanent taxonomy. Two additional working types (`research-` and mailbox entries) handle lifecycle stages and coordination. All nine have optimization rules below.

### 1. Router

**What it does:** Routes Claude to the right file for any task. That's all.
**Loading pattern:** Always-on. Every session, every task.
**Examples:** Personal `CLAUDE.md`, project `CLAUDE.md`.

**Surface-aware budgets (see Design Principle #4 — Dual Optimization):**

| Surface | Budget | Rationale |
|---------|--------|-----------|
| **Project CLAUDE.md** (Cowork — has cleanup checklist, can route to additional files) | Under 500 ideal. Under 1,000 hard ceiling. | Routing mechanism exists — use it. Tier 1 SOP one-liners only. |
| **Personal CLAUDE.md** (Chat/Code — standalone, no routing mechanism) | Under 1,500 hard ceiling. | No cleanup checklist to load additional files. Must carry Tier 1 SOPs as full text + key behavioral cues inline. |
| **Web preferences** (claude.ai paste text — different consumption surface entirely) | No hard ceiling — governed by claude.ai's input limits. | This is paste text for a web form, not a markdown file in the routing system. Optimize for completeness, not token budget. |

| Property | Target |
|----------|--------|
| Token budget | See surface-aware table above. |
| Optimization target | Smallest file that routes every task correctly. For standalone surfaces: smallest file that gives Claude correct behavior without access to additional files. |
| Internal structure | Identity (2–3 sentences) → Tier 1 SOP one-liners (if project router) or Tier 1 full text (if standalone) → Task router (if X → read Y) → Nothing else. |
| Test for inclusion | Could any task skip this info? → Doesn't belong here. Is this a Tier 2 SOP? → Doesn't belong here — it goes in its domain file. |

**Failure modes:**
- **Bloat** — #1 risk. Preferences, conventions, and knowledge accumulate here because it's easy. Each addition seems small; they compound. A project router past 1,000 tokens is storing, not routing.
- **SOP accumulation** — Tier 2 SOPs creep into routers as inline full text. This is the most common bloat vector. Tier 2 SOPs belong in domain files; routers carry only Tier 1 one-liners.
- **Stale routes** — pointing to renamed/moved/deleted files. Wastes a file read + context budget.
- **Missing routes** — new file or task category exists but router doesn't know. Claude guesses or asks.
- **Over-specific routing** — enumerating every task instead of routing by category. Route to domains, not tasks.

---

### 2. Design Surface

**What it does:** Holds the authoritative design. Kurt actively writes and edits it. Source of truth that spawns other files as concepts mature.
**Loading pattern:** On-demand, but loaded frequently for design and review work.
**Examples:** Overview doc, roadmap.

| Property | Target |
|----------|--------|
| Token budget | 3,000–5,000 acceptable. Over 5,000: review for extraction, not split — the doc stays whole, but mature concepts get pulled into standalone guides. |
| Optimization target | Single source of truth that's easy for Kurt to edit AND for Claude to extract actionable information from. |
| Internal structure | Varies by function (overview vs roadmap), but: clear sections, extractable principles, explicit decisions. Narrative is acceptable — this is `for-kurt`. |
| Test for extraction | Has a concept been stable for 2+ sessions and is now driving Claude behavior? → Extract to a guide. Leave a brief reference in the design surface pointing to the new guide. |

**Failure modes:**
- **Concept overstay** — a principle matures but stays embedded in the design surface instead of being extracted to a guide. Claude re-reads the entire doc to access one stable rule.
- **Uncontrolled growth** — past 5,000 tokens without extraction. The file becomes expensive to load and hard to navigate.
- **Stale sections** — some parts actively edited, others untouched for weeks. Stale sections mislead Claude into treating outdated content as current design intent.
- **Missing extraction references** — a concept gets extracted to a guide but the design surface doesn't note where it went. Kurt edits the design surface expecting the concept is still there.

---

### 3. Guide

**What it does:** Teaches Claude how to do something. Actionable instructions. Relatively stable once written.
**Loading pattern:** On-demand, task-triggered. Loaded when Claude needs to follow a process.
**Examples:** Session discipline guide, markdown architecture guide, anti-patterns guide.

| Property | Target |
|----------|--------|
| Token budget | 1,000–3,000 sweet spot. Over 3,000: split by sub-topic. |
| Optimization target | Claude can follow the instructions immediately, no external context needed beyond the file itself. |
| Internal structure | Purpose → When to load → Core instructions (front-loaded, declarative) → Cross-references → Last reconciled date. |
| Self-containment test | Could Claude follow this guide if it were the only file loaded? If not, either inline the dependency or add a clear "read X first" prerequisite. |

**Failure modes:**
- **Narrative drift** — instructions slip into explanatory prose. `for-claude` guides should be declarative. If explanation is needed, it goes in the `for-kurt` paired version.
- **Budget creep** — guide grows past 3,000 tokens as new instructions accumulate. Split by task type or sub-domain.
- **Stale instructions** — conventions change in conversation but the guide doesn't get updated. The write-it-down rule catches this, but it's the guide's primary decay vector.
- **External dependency** — instructions that only make sense if Claude has also read another file, without stating that prerequisite.

---

### 4. Reference

**What it does:** Lookup table. Claude finds a specific answer by scanning, doesn't need to read end-to-end.
**Loading pattern:** On-demand, triggered when Claude encounters something to look up.
**Examples:** Glossary, naming conventions.

| Property | Target |
|----------|--------|
| Token budget | Up to 5,000 acceptable — entries are independently scannable, so Claude doesn't need the whole file in working memory. But watch growth: a reference past 5,000 should split by domain. |
| Optimization target | Fast lookup. Consistent entry format. Claude finds the answer in one scan. |
| Internal structure | Purpose → Entries in consistent format (tables strongly preferred) → Cross-references. |
| Entry format test | Could every entry be expressed as one table row? If an entry needs a paragraph of explanation, it's a guide topic, not a reference entry. |

**Failure modes:**
- **Inconsistent format** — some entries are tables, some are prose, some are nested lists. Slows lookup and makes automated scanning unreliable.
- **Narrative entries** — an entry that requires explanation is a guide topic masquerading as a reference entry. Extract it.
- **Missing entries** — terms or conventions used in conversation but never written to the reference. The gap only surfaces post-compaction when Claude encounters the term cold.
- **Duplicate entries** — same concept defined differently in two places. Reconciliation cycle should catch this.

---

### 5. Module

**What it does:** Self-contained domain knowledge for a specific tool or platform. A project either needs the whole module or none of it.
**Loading pattern:** Conditional — loaded when the project uses the tool/domain. Once loaded, stays relevant for all tasks involving that domain within the project.
**Examples:** Railway module, Notion module.

| Property | Target |
|----------|--------|
| Token budget | 1,000–3,000. If larger, split by sub-domain within the tool. |
| Optimization target | Self-contained. A Claude session with only the module and the project CLAUDE.md can work effectively in that domain. |
| Internal structure | Purpose → Applicability test (does this project use X?) → Core knowledge → Gotchas/anti-patterns (front-loaded after core) → Cross-references. |
| Self-containment test | Does this module assume knowledge from another module? → Inline it or remove the assumption. Modules must not depend on other modules. |

**Failure modes:**
- **Cross-module dependency** — Railway module assumes Docker knowledge from a Docker module. Each module must stand alone.
- **Scope creep** — module covers adjacent tools ("while we're at it, here's how Docker works too"). Stay within the domain boundary.
- **General knowledge** — information that applies regardless of tool choice. That belongs in a guide, not a module.
- **Stale platform knowledge** — APIs change, platforms update. Modules need version dates and periodic verification.

---

### 6. Capture File

**What it does:** Accumulates structured entries over time. Written during work, harvested during reconciliation.
**Loading pattern:** On-demand. Two modes: append (during work) and harvest (during reconciliation or cross-project sweep).
**Examples:** Lessons learned, checkpoints, decision logs.

| Property | Target |
|----------|--------|
| Token budget | Grows over time. Individual entries: under 100 tokens each. File total: archive or split when past 5,000. |
| Optimization target | Easy to append, easy to harvest. Each entry independently useful without reading the whole file. |
| Internal structure | Purpose → Entry format template (shown once at top) → Entries (sequential, newest at bottom for append-only; or newest at top for scan-first) → Harvest status (date of last harvest). |
| Entry format | Every entry must have: date, tag/category, content. Consistent structure so harvesting can be automated or pattern-matched. |

**Failure modes:**
- **Unstructured entries** — prose paragraphs instead of tagged, dated entries. Makes harvesting expensive (Claude must re-read and interpret each entry).
- **Unharvested growth** — file grows past 5,000 without entries being processed. Stale accumulation — entries that should have become guide updates, anti-patterns, or module additions sit inert.
- **Wrong type masquerading** — an entry that's really a guide update or a decision. Capture files hold raw observations; processed insights belong in their target files.
- **Missing context tags** — entries without dates, project tags, or categories. Undiscoverable during cross-project sweeps.

---

### 7. Template

**What it does:** Gets copied once during project setup. The copy becomes a live file of another type. The template itself is never part of the live tree.
**Loading pattern:** One-time at project setup. Never loaded during regular work.
**Examples:** Roadmap template, cleanup checklist template, glossary template.

| Property | Target |
|----------|--------|
| Token budget | Matches the target type's budget. A roadmap template should be in design surface range. A guide template should be in guide range. |
| Optimization target | Minimal post-copy editing. Clear placeholders. The copy should be immediately useful with just placeholder replacement and section fill-in. |
| Internal structure | Mirrors the target file type's ideal structure. `[PLACEHOLDER]` markers for project-specific content. Inline guidance as HTML comments (`<!-- instructions for filling this section -->`). |
| Freshness test | Does this template match current conventions? Templates that drift from what guides prescribe cause projects to start wrong. |

**Failure modes:**
- **Convention drift** — template prescribes one structure, current guides prescribe another. Projects created from the template start out of compliance. Fix: reconciliation cycle includes template-to-guide consistency check.
- **Over-guidance** — too many inline comments turn the template into a guide. The template should show structure; the guide should explain it.
- **Under-guidance** — too few placeholders or instructions. Claude guesses what goes where and gets it wrong.
- **Phantom placeholders** — `[PLACEHOLDER]` markers that survive into the live copy because they weren't obviously placeholders. Use a consistent, conspicuous format.

---

### 8. Research (Working Type)

**What it does:** Investigation in progress. Not a permanent file type — research files are a lifecycle stage. They mature into a guide, reference, or module, or they get archived when superseded.
**Loading pattern:** On-demand, during active investigation. Loaded less frequently once the research is complete and its findings have been absorbed.
**Filename prefix:** `research-`
**Examples:** Config mechanisms research, tech stack research, docs monitor.

| Property | Target |
|----------|--------|
| Token budget | 2,000–4,000. Longer than guides (research includes raw findings, comparisons, and analysis), but not unlimited. Past 4,000: extract conclusions into a guide/reference and archive the research file. |
| Optimization target | Structured findings that can be harvested into permanent file types. Clear separation of raw data vs conclusions vs open questions. |
| Internal structure | Purpose → Research question → Findings (structured, scannable) → Conclusions → Open questions → Recommended next steps → Date of research (for staleness detection). |
| Maturity test | Have the conclusions been stable for 2+ sessions? → Extract to guide/reference/module. The research file becomes archival — keep for provenance, but it's no longer the source of truth. |

**Failure modes:**
- **Permanent residency** — research file never graduates. Its conclusions drive behavior but live in a research file instead of the appropriate permanent type. The research prefix signals "this is temporary" — honor that signal.
- **Unstructured findings** — prose dump of what was discovered. Makes harvesting expensive. Structure findings as scannable entries from the start.
- **Stale research** — findings from months ago treated as current. Research files need a research date and a staleness threshold. If the domain moves fast (APIs, pricing, platform features), flag for re-research at 3 months.
- **Conclusion-free** — raw findings without synthesis. A research file without a Conclusions section is incomplete. The conclusions are what get harvested.

---

### 9. Mailbox Entry (Working Type — Capture File Specialization)

**What it does:** Coordinates between Archimedes and individual projects via two persistent files (`inbox.md`, `outbox.md`) in the `archimedes-mailbox/` folder. A specialization of the capture file pattern, optimized for cross-session, cross-project communication.
**Loading pattern:** Checked at session start (light cleanup) and session end (outbound check). Not loaded during regular work.
**Filename:** Fixed — `inbox.md` and `outbox.md`. No type prefix or versioning (the files are persistent; entries within them are timestamped).

| Property | Target |
|----------|--------|
| Token budget | Grows over time. Individual entries: under 200 tokens each. Archive or summarize when file exceeds 3,000 tokens. |
| Optimization target | Fast scan for unread entries. Each entry independently actionable. Status tracking enables both sides to see what's been processed. |
| Internal structure | Purpose line → Entry format reference → Entries (newest at bottom, append-only). |
| Entry format | `## Entry — DDD DD MMM YYYY HHMM PT` → `**Status:** unread \| harvested \| actioned` → `**Topic:**` → `**Content:**` |

**Status lifecycle:**
- `unread` — freshly written, not yet processed by the reader
- `harvested` — reader has processed and extracted what's needed
- `actioned` — insight has been incorporated into its target file (guide, template, convention, etc.)

**Failure modes:**
- **Unharvested backlog** — entries sit at `unread` across multiple sessions. The mailbox becomes noise. Harvest cadence should match session frequency.
- **Overly detailed entries** — entries that try to be complete guides or research docs. Mailbox entries point to the insight and its target; they don't contain the full fix.
- **Missing status updates** — entries get read and acted on but status never changes. The other side can't tell what's been processed.
- **Bidirectional confusion** — writing to the wrong file. Rule: you write to *your* output file. Projects write to `outbox.md`. Archimedes writes to `inbox.md`.

---

## Type Identification Flowchart

When creating a new file, determine its type:

1. Does it route to other files with minimal content of its own? → **Router**
2. Will Kurt actively write and edit it as a design artifact? → **Design surface**
3. Does it teach Claude how to do something? → **Guide**
4. Is it a lookup table of definitions or conventions? → **Reference**
5. Is it domain knowledge for a specific tool, loaded conditionally? → **Module**
6. Will it accumulate entries over time? → **Capture file**
7. Will it be copied once to create a new project file? → **Template**
8. Is it an active investigation that will mature into another type? → **Research**
9. Is it cross-project coordination between Archimedes and a project? → **Mailbox entry** (append to inbox.md or outbox.md)

If a file spans two types, it's usually a sign it should be split. The exception is design surfaces, which naturally contain some reference material and routing — that's inherent to their role as source of truth.

## Type-to-Prefix Mapping

For files in `for-claude/`, the type determines the filename prefix:

| Type | Prefix | Lives in `for-claude/`? |
|------|--------|------------------------|
| Router | (exempt — `CLAUDE.md`) | No — project root |
| Design surface | `design-` | Yes |
| Guide | `guide-` | Yes |
| Reference | `ref-` | Yes |
| Module | `module-` | Yes |
| Capture file | `capture-` | Yes |
| Template | `template-` | No — `output/` |
| Research | `research-` | Yes |
| Mailbox entry | (fixed — `inbox.md`/`outbox.md`) | No — `archimedes-mailbox/` |

---

## Cross-References

**Part of the three-doc architecture package** (see overview doc, Design Principle #5):
- `naming-conventions.md` → how files are named and organized (system-wide, all surfaces)
- **This doc** → what each file type is, how to optimize it, and its failure modes
- `markdown-architecture-guide.md` → structural engineering principles for all markdown files

Other references:
- `output/naming-conventions-rationale.md` — the "why" behind each naming convention
- Cleanup checklist → reconciliation cycle checks type-specific constraints
- `anti-patterns-guide.md` — real examples of type failures
- Roadmap template → canonical template example
