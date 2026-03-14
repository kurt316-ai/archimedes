# {PROJECT_NAME} — Cleanup Checklist

**Last updated:** {DATE}

---

## Two Modes, One Checklist

This checklist has two modes. Every section is tagged **[Light]**, **[Full]**, or both.

| Mode | When to run | Time | What it covers |
|---|---|---|---|
| **Light** | After every compaction recovery. Also after completing a major task mid-session. | 2–3 min | Context preservation, roadmap sync, self-check. "Did we lose anything?" |
| **Full** | Before every `git push`. End of session. | 10–15 min | Everything in Light, plus file inventory, naming, style, stale content, repo health. "Is the project clean?" |

**Natural triggers:**
- Claude recovers from compaction → **Light**
- Kurt says "let's push" or "let's wrap up" → **Full**
- Kurt says "run the cleanup checklist" → **Full** (unless he specifies light)

**Design principle:** Human input goes first (Sections 0–1), then Claude runs autonomously through the rest. Kurt can walk away after Section 1.

---

## 0. Self-Check [Light] [Full] — REQUIRES KURT

- [ ] Has anything in this session changed the cleanup checklist itself? If so, update this file first.
- [ ] Have any new concepts, conventions, or architecture decisions been added this session? If so, update the relevant docs (roadmap, CLAUDE.md, etc.) so they stay current.

## 1. Kurt Input [Light] [Full] — REQUIRES KURT

Ask Kurt both questions, capture the answers, then proceed autonomously.

- [ ] "Did anything happen this session worth capturing in lessons-learned?" (e.g., a pattern that worked well, a mistake to avoid, a workflow improvement)
- [ ] If yes, append to the lessons-learned file with a date stamp
- [ ] If the lesson is something Archimedes should act on, also append an entry to `archimedes-mailbox/outbox.md` with status `unread`
- [ ] "Anything else I should know before I run the rest of the checklist?"

**After this point, Claude runs autonomously. No more questions.**

---

## 2. Roadmap Sync [Light] [Full]

**First autonomous step — update the source of truth before validating anything else.**

- [ ] All completed work is marked `done` in the phases
- [ ] No tasks are marked complete that aren't actually done
- [ ] Open questions list is current (resolved questions removed or answered)
- [ ] Decision Log includes all decisions from this session
- [ ] Version number and date are updated

## 3. Context Preservation Check [Light] [Full]

**Most critical after compaction.** Verify these facts are written in files, not lost in conversation. Validates against the freshly-updated roadmap.

- [ ] All decisions made this session are captured in the roadmap's Decision Log
- [ ] Any new conventions or patterns are documented (not just discussed)
- [ ] Project vision and end product description (in roadmap)
- [ ] Key constraints and preferences are recorded
- [ ] A new Claude session with zero context could read this folder and be productive immediately

{Add project-specific items here — things that are critical to preserve for YOUR project. Examples:}
{- [ ] API keys are in .env, not hardcoded}
{- [ ] Database schema decisions are documented}
{- [ ] User-facing copy decisions are captured}

## 4. Archimedes Mailbox Check [Light] [Full]

- [ ] Check `archimedes-mailbox/inbox.md` for entries with status `unread`
- [ ] For each unread entry: read it, take the required action, update status to `actioned`
- [ ] Check `archimedes-mailbox/outbox.md` — verify any pending entries have been written

### 4.5 Audit Feedback Check [Light] [Full]

- [ ] Check `for-claude/` for any `capture-*-audit-findings-*` files not yet addressed
- [ ] If new findings exist: read them, address Critical items immediately, add Important items to roadmap, batch Minor items for cleanup
- [ ] For items tagged `[KURT ACTION]`: surface to Kurt before proceeding

**Light mode stops here.** Everything below is Full mode only.

---

## 5. File Inventory [Full]

Scan the full folder tree and confirm every file is accounted for.

{Replace this with your actual project folder structure once established:}

```
{project-parent-folder}/
├── CLAUDE.md                              ← Router (local only)
├── {project}-overview-for-kurt.md         ← Kurt's orientation file
├── for-claude/                            ← Workshop (flat, type-prefixed)
│   ├── design-1-roadmap-for-kurt-and-claude.md
│   ├── guide-1-cleanup-checklist-for-claude.md  ← This file
│   ├── capture-1-lessons-learned-for-archimedes.md
│   └── (other builder files)
├── output/                                ← Deliverable (public git repo)
│   └── (deliverable files)
└── archimedes-mailbox/                    ← Cross-project coordination
    ├── inbox.md
    └── outbox.md
```

- [ ] All files present and in the correct folders
- [ ] No orphaned files at root level
- [ ] No duplicate files across folders

## 6. Naming Conventions [Full]

**`for-claude/` pattern:** `[type]-[seq]-[topic]-for-[audience].md`

- Type prefixes: `design-`, `guide-`, `ref-`, `module-`, `capture-`, `research-`
- Audience tags: `for-kurt`, `for-claude`, `for-kurt-and-claude`, `for-archimedes`
- **No version numbers.** Git tracks versions for all files.

**Root pattern:** `[project]-[topic]-for-[audience].md`

**Exemptions:** `CLAUDE.md`, `README.md`, `SKILL.md`, `inbox.md`, `outbox.md`

**Full spec:** Archimedes `output/templates/naming-conventions.md`

- [ ] All `for-claude/` files follow the type-prefix pattern
- [ ] Sequence numbers are assigned and in logical order within each type
- [ ] Every file has an audience tag
- [ ] No version numbers in any filenames
- [ ] No spaces in filenames

## 7. Writing Style by Audience [Full]

- `for-claude` — Technical. Concise. Structured. Every word earns its place.
- `for-kurt` — Conversational. Plain English.
- `for-kurt-and-claude` — Structured content Claude can act on, readable by Kurt.
- `for-archimedes` — Same as `for-claude`. Signals this file feeds back into Archimedes.

Checks:
- [ ] Each file's writing style matches its audience tag
- [ ] `for-claude` files have no unnecessary prose
- [ ] `for-kurt` files are readable without technical context

## 8. Stale Content Check [Full]

- [ ] No references to old filenames that don't exist on disk
- [ ] No references to old folder names
- [ ] Internal file references point to current filenames
- [ ] No outdated terminology or working names that have been replaced
- [ ] **Propagation sync:** For any source-of-truth file with a "Propagation Targets" table, verify downstream copies match the source. Fix any drift.
- [ ] No descriptions that contradict current decisions
- [ ] Headers and titles inside files match the file's current name and purpose
- [ ] All `Last updated` timestamps use Kurt's format: `DDD DD MMM YYYY HHMM`

## 9. Archimedes Mailbox — Outbound Check [Full]

Before pushing, check if anything from this session should be reported to Archimedes.

- [ ] Did this session discover a new pattern, anti-pattern, or tool insight not already captured in Section 1?
- [ ] Did this session hit a convention conflict or a gap in Archimedes guidance?
- [ ] Did Kurt state a preference or correction that should propagate to other projects?
- [ ] For each "yes": append an entry to `archimedes-mailbox/outbox.md` with status `unread`

This is the **end-of-session write trigger** — it catches anything that Section 1 (Kurt input) missed because it wasn't recognized as Archimedes-relevant at the time.

## 10. Pre-Push Security Check [Full]

**BEFORE EVERY `git push` TO PUBLIC REPOS.** Private repos skip this unless they contain sensitive project data.

Read first: `output/guides/security-conventions-guide.md` for full PII patterns and edge cases.

Quick checks:
- [ ] Search for all PII terms from your project's PII search terms list (family names, home nicknames, personal emails, addresses, vehicles)
- [ ] Search for phone numbers and financial figures
- [ ] `.env`, `.gitignore`, `CLAUDE.md` are excluded from repo
- [ ] No `for-claude/` files in `output/` folder (if two-repo project)
- [ ] No root overview with PII committed to public repo
- [ ] No references to private repo structure or sensitive filenames

If matches found: remove, generalize, or move to private files. Then commit and proceed to Section 11.

## 11. GitHub Repo Health [Full]

- [ ] Does this project have a GitHub repo? If not, and there are 3+ files, create one.
- [ ] **Before every commit:** Update README.md "Last updated" timestamp to current time. The timestamp reflects the last time anything in the repo changed, not just the README.
- [ ] Are there uncommitted changes? If so, commit and push.
- [ ] Does the remote match the local state? (no unpushed commits)
- [ ] **One push block.** All repos, all commands, one code fence. Chain with `&&` and `cd ../sibling`. Never split across multiple blocks — Kurt should copy-paste once.

{If this project has two repos (deliverable + builder), check BOTH:}
- [ ] Deliverable repo (`{project}/`) — clean and pushed
- [ ] Builder repo (`{project}-builder/`) — clean and pushed

## 12. Folder Structure Check [Full]

- [ ] Deliverable folder contains only deliverable files (no builder artifacts)
- [ ] Builder folder contains only builder files (no deliverables)
- [ ] CLAUDE.md is at the parent level, not inside either subfolder
- [ ] Each subfolder with a git repo has its own `.gitignore`

---

## How to Run This Checklist

**Light mode** (after compaction or mid-session):
> "Run the light cleanup checklist."

Claude should: Ask Kurt Sections 0–1. After Kurt responds, run Sections 2–4 autonomously. Report pass/fail. Fix what it can.

**Full mode** (pre-push or end-of-session):
> "Run the cleanup checklist." or "Run the full cleanup checklist."

Claude should:
1. Ask Kurt Sections 0–1. After Kurt responds, run autonomously.
2. Scan the full folder tree
3. Read each file
4. Report pass/fail for each item
5. Fix what it can, flag what needs Kurt's input
6. Section 10 (pre-push security check) must complete before any `git push` to public repos
7. Section 9 (if Archimedes project, outbound mailbox check) is the last chance to send traffic before the session ends
