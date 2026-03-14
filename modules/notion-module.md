# Notion Integration Module

**Audience:** `for-claude`
**Purpose:** Product Effectiveness (PE), Product Efficiency (PI)
**Load when:** Project uses Notion databases, pages, or API
**Last updated:** Thu 12 Mar 2026 0300

---

## When to Load This Module

Load this module if the project:
- Uses Notion as a workspace (pages, databases)
- Reads or writes Notion content via API or MCP tools
- Uses Notion as an approval surface or UI layer
- Stores reference data in Notion

Do NOT load for projects that only mention Notion in passing.

---

## Core Patterns

### 1. Workspace Map as Canonical Reference

Create a local file (`notion-workspace-map.md` or similar) that indexes:
- Every Notion page the project uses, with its page ID
- Person-to-ID lookup table (who maps to which Notion user ID)
- Page hierarchy (which pages are children of which)
- Naming conventions for Notion pages

This is the single source for Notion page IDs. Never search Notion blindly — consult the workspace map first. Update it when the roster or page structure changes.

**Real example from Cato:** `cato-builder-notion-mirror/notion-workspace-map.md` contains page IDs, person lookup, and structural relationships. Every Claude session that needs a Notion page ID checks here first.

### 2. Local Mirror Folder

Keep local markdown copies of key Notion pages:
```
[project]-builder-notion-mirror/
├── notion-workspace-map.md      ← canonical index
├── [page-name].md               ← exported content
├── [page-name].md
└── archive/                     ← old versions
```

**Why:** Notion content can change outside the project. The local mirror provides a stable reference that Claude can read without API calls. Drift detection happens during cleanup (compare mirror to live version).

### 3. Page IDs in Environment Variables

Store frequently-used page IDs as env vars:
```
NOTION_APPROVALS_PAGE_ID=320b34c38f2d8067a8abd984d59b3f2e
NOTION_WORKSPACE_ROOT_ID=...
```

One source for each ID. If it appears in code, it should reference the env var, not a hardcoded string.

---

## Preserving Notion Special Elements

When reading from and writing to Notion pages, these elements require special handling. The Notion API and MCP tools don't always preserve them correctly.

### @date mentions
- **Format:** `<mention-date start="YYYY-MM-DD"/>` (not "March 9, 2026")
- **With end date:** `<mention-date start="YYYY-MM-DD" end="YYYY-MM-DD"/>`
- When editing a page, preserve existing @date mentions exactly. Don't convert to plain text.

### @user mentions
- **Format:** `<mention-user url="user://USER_ID"/>` (not "@Kurt")
- Look up user IDs in the workspace map
- When editing, preserve existing mentions. Don't replace with plain names.

### Page links
- **Format:** `<mention-page url="page://PAGE_ID"/>` or inline link
- Preserve the page ID reference. Don't convert to plain text.

### Colors and formatting
- Notion supports colored text and backgrounds
- When editing, preserve existing color formatting
- Don't strip colors unless explicitly asked

### Toggle blocks, callouts, /toc
- These block types have specific syntax
- When editing nearby content, don't accidentally delete or modify these blocks
- If unsure about a block type, leave it alone

### Empty blocks (spacers)
- Notion pages often use empty blocks for visual spacing
- Don't remove them — they're intentional

---

## Notion as Approval Surface

A pattern used in both Cato and Marvin: use Notion pages as the human review interface.

### Pre-approved checkboxes pattern

Items are written to Notion with checkboxes **already checked** (pre-approved):
```
- [x] CR-001: Color Wodify class to Flamingo @ 9:00 AM Mon Mar 9 (in 2 days)
- [x] CR-002: Delete declined event "Team Lunch" @ 12:00 PM Tue Mar 10 (in 3 days)
```

**User action:** Kurt reviews the list. Leaves checked items as-is (approved). Unchecks items to reject them. This is faster than approving each item individually.

**Code reads back:** Checked = approved, proceed. Unchecked = rejected, skip.

**Why this works:** Reduces decision fatigue. Most items are routine. Kurt only needs to act on exceptions.

### Structured format for approval items

Agree on a format before building:
```
- [x] [Action]: [description] @ [TIME] [DAY] [DATE] ([relative days])
```

Deterministic format makes parsing reliable. Don't use freeform text for items that code needs to read back.

---

## Notion API Patterns

### Internal integration token
- Created at `notion.so/my-integrations`
- Must be explicitly shared to each page/database the integration accesses
- Store as `NOTION_TOKEN` in env vars
- Tokens are scoped to the workspace — one per project is fine

### Read before write
- Always read the current page state before writing
- Notion pages can be edited by other users between your reads and writes
- Check for conflicts (content that changed since last read)

### Database queries
- Notion API has limited query capabilities for databases
- Pre-structure pages to make fetching deterministic
- Use page properties (not page content) for filterable data
- For complex queries, consider fetching all records and filtering locally

### Error handling
- Log every Notion operation (read and write)
- Notion API can return 429 (rate limit), 500 (server error), or timeout
- Retry with backoff on transient errors
- Surface errors to the user (don't swallow them)

### Scan summaries
After a batch operation, write a summary to the Notion page:
```
Scan: 2026-03-12 · CR-001 (3 hits), CR-002 (1 hit) · 47 events already correct
```

This gives the user confidence that the system ran and shows what it did.

---

## Anti-Patterns

1. **Searching Notion blindly:** Use the workspace map. Don't call the API to search for pages by title when you have a page ID table.

2. **Destroying special elements:** Editing a Notion page and accidentally stripping @mentions, colors, or toggle blocks. Always preserve what you don't explicitly intend to change.

3. **Silent write failures:** Code moves on after a failed Notion write. Page is stale. User doesn't know. Log everything, surface errors.

4. **Hardcoded page IDs in source code:** Use env vars. Page IDs change when pages are moved or recreated.

5. **Over-fetching:** Reading entire Notion databases when you only need one page. Wastes API quota and context budget.

---

## Open Questions

> **⚠️ This module is based on patterns from Cato and Marvin.** Additional patterns may emerge from new projects.

1. **Notion database schemas as code:** Should the module include guidance on designing Notion database schemas? Cato uses a complex schema with relational properties.
2. **MCP vs. direct API:** When should Claude use Notion MCP tools vs. direct HTTP API calls? MCP is simpler but may have limitations.
3. **Sync strategy:** How often should the local mirror be refreshed? Every session? On demand? On a schedule?
