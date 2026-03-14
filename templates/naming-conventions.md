# Naming Conventions — System-Wide Reference

**Last updated:** Fri 13 Mar 2026
**Type:** Reference (lookup table — scan, don't read end-to-end)
**Audience:** `for-claude` and `for-kurt`
**Companion doc:** `output/naming-conventions-rationale.md` — explains *why* each convention was chosen.

This is the master naming conventions reference for all systems Kurt and Claude use together. Each system (Cowork project folders, Notion, Google Drive, GitHub, scheduled tasks) gets its own section with system-specific rules derived from the shared design principles.

**Part of the three-doc architecture package** (see overview doc, Design Principle #5):
- **This doc** — how files are named and organized (system-wide, all surfaces)
- `output/guides/file-type-optimization-guide.md` — what each file type is, how to optimize it, and its failure modes. The type prefix in a filename triggers that guide's rules.
- `output/guides/markdown-architecture-guide.md` — structural engineering principles for all markdown files

---

## 1. System-Wide Design Principles

These principles govern naming across every system. System-specific sections derive their rules from these.

1. **Surface type determines case convention.**
   - **kebab-case** — machine-facing surfaces: filenames, git repos, git branches, cron job names, CLI identifiers, env var keys. Safe across every OS, filesystem, CLI tool, and git implementation. No edge cases.
   - **Title Case** — human-facing surfaces: Notion page titles, Google Doc titles, Google Drive folder names. Optimized for scanning in a UI, not a terminal.
   - **snake_case** — structured data surfaces: database property names, API field names, config keys. TBD — confirm when Notion conventions are designed.

2. **Naming carries metadata.** A good name tells you what kind of thing it is before you open it. In filenames, the type prefix triggers optimization rules. In other systems, the naming pattern signals the object's role.

3. **Convention per system, not per project.** A Notion page follows Notion conventions regardless of which project it belongs to. A filename follows file conventions regardless of which system it supports.

4. **Elements earn their place — both ways.** Every element in a name (type prefix, sequence number, audience tag) must serve a purpose for that specific context. Don't include elements that add no signal — but don't drop elements that do. "Simplicity" is not a reason to strip useful metadata. The test: does removing this element lose information that someone (Claude, Kurt, or a future reader) would have to go find elsewhere?

5. **Alpha sort is UX.** Element order determines sort order. Sort order determines what you see first. Design the element order for how you want to browse.

6. **Audience tag controls writing style** (Cowork project files only). `for-claude` = technical, structured, no narrative. `for-kurt` = conversational, plain English. `for-kurt-and-claude` = structured but readable.

7. **Folders replace metadata in repos; prefixes replace folders locally.** Two contexts, two strategies. In a **git repo browsed on GitHub** (like `output/`), subfolders (`guides/`, `modules/`, `templates/`) do the organizing — type prefixes, sequence numbers, and audience tags are redundant because the folder already tells you what kind of file it is and who it's for. In a **local working directory scanned by Claude** (like `for-claude/`), the folder is flat — type prefixes, sequence numbers, and audience tags do all the organizing because there are no subfolders to lean on. Same information, different encoding. The rule: never double-encode (prefix + folder saying the same thing) and never under-encode (flat folder with no prefix).

**Applied guidance** (derived from the principles above, not standalone principles):

- **No version numbers in filenames.** Application of principle #4: in files under git, version numbers don't earn their place — `git log` and `git diff` do versioning better. May reconsider for files shared outside git (e.g., templates distributed via download).

---

## 2. Cowork Project Folders

Everything below applies to Cowork project folders — the Two-Stack folder structure that Archimedes equips every project with (Stack 1: builder, Stack 2: product).

### 2.1 Project Skeleton

```
project-folder/
  CLAUDE.md                    ← router, exempt from naming pattern
  [project]-overview-for-kurt.md  ← Kurt's file(s), at root
  archimedes-mailbox/          ← two persistent files (inbox.md, outbox.md)
  for-claude/                  ← flat, type-prefixed, no subfolders
  output/                      ← mirrors the public GitHub repo
```

### 2.2 Folder Rules

| Folder | Purpose | Structure | Naming optimized for |
|--------|---------|-----------|---------------------|
| Project root | Kurt's files + CLAUDE.md | Max 3 `.md` files. At 4+, create `for-kurt/`. | Human browsing |
| `for-claude/` | Claude's working files — the workshop | Flat. No subfolders. Type prefix does the routing. | Claude's single-scan routing |
| `output/` | The deliverable. Maps to the public GitHub repo. | Consumer-first: `guides/`, `modules/`, `references/` for Claude; human files at root; `templates/` at root. See §2.6. | Consumer routing via subfolders (principle #7) |
| `archimedes-mailbox/` | Cross-project coordination with Archimedes | Two files only: `inbox.md`, `outbox.md` | Chronological entry scanning |

**Cowork-specific principles** (derived from system-wide principles):

- **Naming conventions and folder structure are substitutes, not complements.** The better the naming, the flatter you can go. Folders are a speed tax for Claude — every subfolder is another tool call. Use folders only at genuine workflow boundaries.
- **Flat is faster for Claude.** A 40-file directory listing is ~4KB — trivial for one scan. Sequential folder navigation (list → pick → list → pick) is slower even with fewer files per folder.
- **Type prefix = optimization contract.** The leading element in a `for-claude/` filename tells both Claude and Kurt what kind of file it is. That triggers the optimization rules from the file type guide before the file is even opened.

### 2.3 Git Mapping

Each project has two git repos with different `.git` locations:

| Repo | `.git` location | Tracks | Visibility |
|------|----------------|--------|------------|
| `{project}-builder` | **Project root** | `for-claude/`, `archimedes-mailbox/`, root Kurt files (`[project]-overview-for-kurt.md`, etc.) | Private |
| `{project}` | **Inside `output/`** | Everything in `output/` | Public (or private per project) |

**`.gitignore` at project root** excludes `output/` and `CLAUDE.md` from the builder repo. This means the builder repo's `git add` and `git commit` run from the **project root**, not from inside `for-claude/`.

`CLAUDE.md` is local only — not in either repo (it contains routing specific to the local environment).

---

### 2.4 Filename Pattern — `for-claude/`

```
[type]-[seq]-[topic]-for-[audience].md
```

Apply design principle #4 (elements earn their place — both ways) to each candidate element:

| Element | Include? | Why |
|---------|----------|-----|
| `[type]` | **Yes** | Triggers optimization rules from the file type guide. The leading element so files cluster by type in alpha sort. In a flat folder with 10–15 files, type-based clustering is the primary navigation mechanism. |
| `[seq]` | **Yes** | Controls sort order within a type cluster. Puts the most important file first. A solo file still gets `-1-` — costs nothing, prevents a rename when a second file arrives. |
| `[topic]` | **Yes** | What the file is about. Always needed. |
| `for-[audience]` | **Yes** | Controls writing style. Tells the next Claude session (or Kurt) what register to expect before opening the file. |
| `[project]` | **No** | Files live inside the project folder — the folder already provides project context. Adding it would push the topic deeper into the filename, hurting scannability. |
| `-v##` | **No** | Git (builder repo) tracks history. |

### Type Prefixes

| Prefix | File type | What it signals | Typical count per project |
|--------|-----------|-----------------|--------------------------|
| `design-` | Design surface | Source of truth. Kurt edits. 3,000–5,000 tokens. | 1–2 (roadmap, maybe constraints) |
| `guide-` | Guide | Teaches Claude how to do something. Declarative. 1,000–3,000 tokens. | 2–5 |
| `ref-` | Reference | Lookup table. Consistent entry format. Up to 5,000 tokens. | 1–3 |
| `module-` | Module | Domain knowledge for a specific tool. Self-contained. 1,000–3,000 tokens. | 0–3 (conditional on project) |
| `capture-` | Capture file | Accumulates entries over time. Append/harvest pattern. | 1–3 |
| `research-` | Research (lifecycle stage) | Investigation in progress. Will mature into guide/ref/module or be archived. | 0–5 |

### Sequence Number Rules

- Counts within type, not globally. `research-1`, `research-2`, `guide-1` are independent sequences.
- Single digit. If you hit 10 of any type, consolidate rather than expanding to two digits.
- Controls sort order within a type cluster. Use it to put the most important file first.
- A solo file still gets `-1-` — costs nothing, prevents a rename when a second file arrives.

### Examples — Archimedes Builder Files

```
for-claude/
  capture-1-lessons-learned-for-kurt-and-claude.md
  capture-2-handoff-log-for-claude.md
  design-1-roadmap-for-claude.md
  guide-1-cleanup-checklist-for-claude.md
  guide-2-cowork-best-practices-for-claude.md
  ref-1-glossary-for-claude.md
  ref-2-project-registry-for-claude.md
  research-1-config-mechanisms-for-claude.md
  research-2-universal-preferences-for-claude.md
```

---

### 2.5 Filename Pattern — Project Root (Kurt's Files)

```
[project]-[topic]-for-[audience].md
```

Apply design principle #4 (elements earn their place — both ways) to each candidate element:

| Element | Include? | Why |
|---------|----------|-----|
| `[project]` | **Yes** | File appears in GitHub repo listings, search results, and Finder outside its folder. Project prefix carries context that the folder would otherwise provide. |
| `[topic]` | **Yes** | What the file is about. Always needed. |
| `for-[audience]` | **Yes** | Controls writing style. A root file without an audience tag forces the reader to guess the register. |
| `[type]` | **No** | Root files don't cluster by type — there are too few to need type-based routing. |
| `[seq]` | **No** | Too few files at root to need sort control. If file count grows to 4+, move to `for-kurt/` instead. |
| `-v##` | **No** | Git (builder repo) tracks history. |

- **Examples:** `archimedes-overview-for-kurt.md`, `cato-notes-for-kurt.md`
- **Threshold:** max 3 `.md` files at root. At 4+, create `for-kurt/` and move them there.
- **Claude rule:** treat root-level `.md` files (other than `CLAUDE.md`) as Kurt's, not as part of `for-claude/`.

---

### 2.6 Folder Structure — `output/`

The `output/` folder mirrors the public GitHub repo. Organized **consumer-first** — who reads or uses the content determines where it lives.

#### Three consumer types

| Consumer | Where in `output/` | Case / naming |
|----------|-------------------|---------------|
| **Claude** | `guides/`, `modules/`, `references/` subfolders | kebab-case, descriptive names. Subfolders do the organizing (principle #7) — no type prefixes or audience tags needed. |
| **Human users** | Root level (if few files) or `for-humans/` (if 4+) | kebab-case, descriptive names |
| **Machine consumers** | Root level or dedicated folder (e.g., `config/`) | kebab-case, follows the consuming system's conventions |

**Why no `for-claude/` wrapper in `output/`:** `for-claude/` means exactly one thing — the flat, type-prefixed builder folder. Using it again inside `output/` creates a naming collision that confuses cold-start Claude sessions. The subfolders (`guides/`, `modules/`, `references/`) sit directly under `output/` instead. Application of principle #7: folders do the organizing in repos.

#### `output/` skeleton

```
output/
  README.md                            ← human entry point, routes to everything
  start-here.md                        ← Claude's entry point for project setup
  [human-facing files at root]         ← guides for-kurt, rationale docs, etc.
  guides/                              ← how-to docs Claude reads
  modules/                             ← conditional domain knowledge
  references/                          ← lookup tables
  templates/                           ← starter files copied into projects (serves all audiences)
  [machine artifacts as needed]        ← config files, deployment specs, API schemas
```

#### Filename pattern and element reasoning

Since `output/` maps to a GitHub repo, file naming follows **Section 5 (GitHub)** conventions. See §5.3 for the full element reasoning table, subfolder conventions, and special files.

**Quick summary:** `[topic]-[descriptor].md` in kebab-case. Subfolders do the organizing (principle #7) — no type prefixes, sequence numbers, or audience tags.

**Examples:** `output/guides/session-discipline-guide.md`, `output/templates/roadmap-template.md`, `output/naming-conventions-rationale.md` (human file at root)

---

### 2.7 Archimedes Mailbox

#### Structure

```
archimedes-mailbox/
  inbox.md     ← Archimedes writes, project reads
  outbox.md    ← Project writes, Archimedes reads
```

#### Entry Format (both files)

```markdown
## Entry — DDD DD MMM YYYY HHMM PT
**Status:** unread | harvested | actioned
**Topic:** [short description]
**Content:**
[structured content — keep entries independent and scannable]
```

#### Rules

- **Append-only.** New entries go at the bottom. Never reorder.
- **Status lifecycle:** `unread` → `harvested` (reader has processed) → `actioned` (insight incorporated into target file).
- **Archimedes write access:** Archimedes has a narrow write exception to update `inbox.md` in any project's `archimedes-mailbox/`. This is the only cross-project write permission.
- **Entry independence.** Each entry must be useful without reading other entries. Include enough context.

---

### 2.8 Audience Tags

| Tag | Writing style | When to use |
|-----|--------------|-------------|
| `for-claude` | Technical audience (the next Claude session). Structured lists/tables. No narrative. Every word earns its place. | Files Claude reads to do its job |
| `for-kurt` | Conversational. Plain English. Enough context to orient after weeks away. | Files Kurt reads to understand the project |
| `for-kurt-and-claude` | Structured content Claude can act on, with enough plain-English context Kurt can read it too. | Files both need (roadmaps, lessons learned) |
| `for-archimedes` | Same style as `for-claude`. Signals this file feeds back into Archimedes via the project registry. | Lessons-learned files that Archimedes will harvest |

---

### 2.9 Exemptions

These files keep their standard names. Each exemption has a reason that outweighs the naming pattern (design principle #4 — the standard name carries more signal than our pattern would):

| File | Why exempt |
|------|-----------|
| `CLAUDE.md` | System-recognized filename. Cowork and Claude Code look for this exact name. Renaming it would break routing. |
| `README.md` | GitHub convention. Rendered automatically by GitHub. Every developer knows this name. |
| `SKILL.md` | System-recognized filename. The skills framework looks for this exact name. |
| `.gitignore` | Git convention. Git itself requires this name. |
| `inbox.md` / `outbox.md` | Mailbox convention. Fixed pair — the names encode directionality (who writes, who reads). Adding project prefixes would break the cross-project consistency. |

---

### 2.10 Date Format

Kurt's standard for all timestamps:

```
DDD DD MMM YYYY HHMM PT
```

Examples: `Thu 12 Mar 2026 0007 PT`, `Fri 13 Mar 2026 1430 PT`

Military (24-hour) time, Pacific. Used on all `for-kurt` files, READMEs, `Last updated` headers.

---

### 2.11 Decision Flowchart — Where Does This File Go?

```
Is it CLAUDE.md, README.md, or SKILL.md?
  → YES: Exempt. Standard name, standard location.

Is it Kurt's file (overview, personal notes)?
  → YES: Project root. Pattern: [project]-[topic]-for-[audience].md

Is it a template, guide, module, or reference that ships to users?
  → YES: output/ — follow GitHub conventions

Is it a mailbox entry?
  → YES: archimedes-mailbox/ — append to inbox.md or outbox.md

Everything else:
  → for-claude/ — use [type]-[seq]-[topic]-for-[audience].md
```

---

## Per-System Conventions (Sections 3–7)

Each section below captures known constraints for a specific system. Most are TBD — designed when a project actively needs that system. **TBD does not mean "wait before using the system."** If your project uses one of these systems, follow the constraints listed and apply the shared design principles (Section 1) to derive anything missing. Document project-specific decisions in your `for-claude/` files and flag them in an Archimedes mailbox outbound entry so the convention can be formalized.

---

## 3. Notion — TBD

Conventions for Notion page titles, database names, property names, and page hierarchy. Will be designed when a project needs user-facing Notion surfaces.

**Known constraints:**
- Title Case for page names (human-facing surface)
- Property naming convention TBD (likely snake_case for database properties)
- Hierarchy/nesting conventions TBD

---

## 4. Google Drive — TBD

Conventions for file titles, folder structure, and naming within your Google Workspace domain. Includes PDF naming for the scanner intake pipeline (physical paper → ScanSnap → PDF → Google Drive).

**Known constraints:**
- Title Case for document and folder names (human-facing surface)
- PDF scanner pipeline naming TBD

---

## 5. GitHub

Conventions for repository names, branch names, file naming within repos, and commit messages.

### 5.1 Repository Names

- **kebab-case** (machine-facing, principle #1): `{project}`, `{project}-builder`
- **Two-repo pattern:** `{project}` = public deliverable (maps to `output/`), `{project}-builder` = private workshop (maps to `for-claude/`)

### 5.2 Branch Names

- **kebab-case** (machine-facing, principle #1)
- Convention TBD — currently `main` only for most projects

### 5.3 File Naming Within Repos

This is the convention that governs files inside `output/` — because `output/` *is* the GitHub repo.

**Core rule (principle #7):** Subfolders do the organizing. No type prefixes, no sequence numbers, no audience tags. Descriptive kebab-case names only.

**Why this differs from `for-claude/`:** In a repo browsed on GitHub, folders already tell you the type and audience (`guides/`, `modules/`, `templates/`). Adding a `guide-` prefix to a file inside `guides/` double-encodes. In the flat `for-claude/` builder folder, there are no subfolders — so prefixes carry that information instead. Same metadata, different encoding.

| Element | Include? | Why |
|---------|----------|-----|
| `[topic]` | **Yes** | What the file is about. Always needed. |
| `[descriptor]` | **Optional** | Disambiguates when the topic alone isn't enough (e.g., `session-discipline-guide.md`). Omit when topic is self-explanatory. |
| `[type]` | **No** | Subfolder already signals the type. |
| `[seq]` | **No** | Files are browsed by topic, not priority. |
| `for-[audience]` | **No** | Subfolder already signals the audience. Exception: human-facing files at `output/` root may include `for-kurt` to distinguish from `README.md`. |
| `[project]` | **No** | The repo name carries project context. |
| `-v##` | **No** | Git tracks history. |

**Subfolder conventions:**

| Subfolder | Contains | Consumer |
|-----------|----------|----------|
| `guides/` | How-to docs, process guides, convention docs | Claude |
| `modules/` | Conditional domain knowledge (loaded per project) | Claude |
| `references/` | Lookup tables, glossaries | Claude |
| `templates/` | Starter files copied into projects at setup | All audiences |
| Root (`output/`) | README, human-facing docs, start-here | Human users + Claude entry point |

**Special files at root:**
- `README.md` — human entry point. GitHub renders this automatically.
- `start-here.md` — Claude's entry point for project setup.
- Human-facing files (≤3 at root; at 4+, create `for-humans/`).

### 5.4 Commit Messages

- Convention TBD
- Current practice: imperative mood summary line, optional body with context

---

## 6. Scheduled Tasks — TBD

Conventions for cron job names, scheduled task identifiers, and related naming in Marvin and other orchestration systems.

**Known constraints:**
- kebab-case for task identifiers (machine-facing)
- Naming pattern TBD (likely `[project]-[action]-[cadence]` or similar)

---

## 7. Slack — TBD

Conventions for Slack workspace names, channel names, and canvas naming. Kurt uses Slack across multiple workspaces (personal, Northslope).

**Known constraints:**
- Title Case for workspace display names (human-facing surface)
- kebab-case for channel names (machine-facing, Slack convention)
- Workspace organization and channel naming patterns TBD
