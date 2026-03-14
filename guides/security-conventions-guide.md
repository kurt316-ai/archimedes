# Security Conventions Guide

**Purpose:** Pre-push PII and secrets checks for public repos. Prevents accidental exposure of family data, credentials, and private infrastructure.
**When to Load:** Before any `git push` to a public repo. Referenced by cleanup checklist Section 13.
**Last updated:** Sat 14 Mar 2026
**Scope:** Pre-push PII checks. Full security audit and per-tool security considerations are separate deliverables.

---

## Rule: Never Push PII to Public Repos

**Public repos** (`output/`, published GitHub pages): zero PII. Private repos may contain PII if the project requires it.

PII includes:
- Family member names
- Home nicknames and addresses
- Personal email addresses and domain names
- Phone numbers
- Financial details (account numbers, credit cards, rates, prices)
- Medical/health information
- Precise location coordinates (beyond public property info)
- Internal salary/compensation data

---

## Pre-Push Security Checklist

**Run this before every `git push` to a public repo.** Private repos skip this unless they explicitly contain sensitive data.

### Root-Level Files

- [ ] Root `*-overview-for-kurt.md` — **PRIVATE DATA.** Never push to public repos.
  - **Correct:** Private repo or local-only file
  - **Wrong:** Committed to `output/` or public GitHub
  - **Mitigation:** Keep root overview private. Create a public-safe overview slice in `output/` if needed.

- [ ] CLAUDE.md — **LOCAL ONLY.** Contains credentials and personal preferences.
  - **Correct:** Not in any git repo
  - **Wrong:** Committed anywhere
  - **Mitigation:** Always add CLAUDE.md to `.gitignore`

- [ ] .env files — **LOCAL ONLY.** Contains API keys, secrets, database credentials.
  - **Correct:** Not in any git repo
  - **Wrong:** Committed anywhere (even in private repos)
  - **Mitigation:** Always add `.env`, `.env.local`, `.env.*.local` to `.gitignore`

### `for-claude/` Folder

- [ ] No `for-claude/` files in `output/` (the deliverable folder)
  - **Correct:** `for-claude/` is flat, workshop-only, has its own git repo
  - **Wrong:** Any `for-claude/` file committed to `output/` or public GitHub
  - **Mitigation:** This is a folder structure check — the two repos are separate

- [ ] Lessons-learned files — **PRIVATE REPO ONLY.**
  - **Correct:** Private builder repo (`{project}-builder`), not public deliverable
  - **Wrong:** Pushed to public GitHub
  - **Mitigation:** Lessons-learned files never leave the private builder repo

- [ ] Personal preferences and research docs — **PRIVATE REPO ONLY.**
  - Examples: preferences files, universal preferences research, PII search terms
  - **Correct:** Private builder repo
  - **Wrong:** Public `output/` or public GitHub
  - **Mitigation:** These are internal docs; their public equivalents (if any) go in `output/templates/`

### `output/` Folder (The Deliverable)

- [ ] No personal data in any public-facing file
  - **Check:** Search each file for PII terms from your project's PII search terms list (kept in the private builder repo). This includes family names, home nicknames, personal emails, and more.
  - **Check:** Search for financial figures (exact mortgage rates, account balances, price points)
  - **Correct:** Generic examples (e.g., "a family member's name" instead of a real name)
  - **Wrong:** Specific data (e.g., a real family member's birthday)

- [ ] Template files in `output/templates/` are truly templates — no placeholder data with real PII
  - **Example – WRONG:** a template with a literal personal email in the example
  - **Example – CORRECT:** a template with `{YOUR_PERSONAL_EMAIL}` or `user@example.com` (generic example)

- [ ] No references to private repo filenames or content
  - **Check:** Output docs should never cite `for-claude/` filenames or say "see the private builder repo for..."
  - **Wrong:** "For full preferences, see `ref-3-web-and-cowork-preferences-for-claude.md` in the private repo"
  - **Correct:** Self-contained or generic: "Customize your preferences in your local `CLAUDE.md`"

### Archimedes-Specific Checks

- [ ] `archimedes-overview-for-kurt.md` — **PRIVATE ROOT FILE.** Never push to any public repo.
  - Contains Kurt's full household reference (family names, all addresses, vehicle details)
  - **Correct:** Local-only or private repo only
  - **Wrong:** In `output/` or any public GitHub

- [ ] Project registry — **PRIVATE REPO ONLY.**
  - Contains household reference (family names, home nicknames + full addresses, vehicle details)
  - The registry itself is internal Archimedes infrastructure
  - **Correct:** Private builder repo
  - **Wrong:** Public `output/` or public GitHub

- [ ] Public-safe registry slice (if needed for other projects) — **SEPARATE FILE in `output/` if published**
  - Would omit household reference or show only generic schema
  - **Correct:** "The registry is private. Create your own household reference using this template..."
  - **Wrong:** Publishing the actual registry with real family data

### `.gitignore` Standard

Every two-repo project has two `.git` directories — one at the project root (builder repo, always private) and one inside `output/` (product repo, may be public). Each needs its own `.gitignore`:

1. **Root `.gitignore`** (builder repo — `.git` at project root, always private)
   ```
   # The product repo has its own .git inside output/
   output/

   # Local-only — never tracked anywhere
   CLAUDE.md
   .DS_Store

   # Secrets (defense in depth — this repo is private, but still)
   .env
   .env.local
   .env.*.local
   *.key
   *.pem
   secrets/
   credentials/
   ```
   **Critical:** The `output/` exclusion prevents the builder repo from double-tracking product files. Without this, both repos track the same files.

2. **`output/.gitignore`** (product repo — `.git` inside `output/`, may be public)
   ```
   # Secrets — critical if this repo is public
   .env
   .env.local
   .env.*.local
   *.key
   *.pem
   secrets/
   credentials/

   # OS artifacts
   .DS_Store

   # Build artifacts (if code is ever added)
   node_modules/
   dist/
   build/
   ```

The builder repo excludes `output/` and `CLAUDE.md` (structural). The product repo excludes secrets and build artifacts (security). Both exclude `.env` as defense in depth.

---

## Checklists by Scenario

### Scenario: Single repo (project-only, no separate builder)

**Before `git push`:**
1. [ ] Search for all PII terms from your project's PII search terms list (family names, home nicknames, personal emails, addresses, vehicles)
2. [ ] Search for phone numbers (patterns: 555-, XXX-XXX-XXXX)
3. [ ] Search for financial figures: mortgage rates, account numbers, exact prices, salary data
4. [ ] Verify .env, .gitignore, CLAUDE.md excluded
5. [ ] Skim `README.md` or main docs for narrative PII leaks (e.g., "my wife helped with..." using real names)
6. [ ] If any matches found: remove, generalize, or move to private docs
7. [ ] Commit and push when clean

### Scenario: Two-repo project (Archimedes-pattern)

**Private repo (`for-claude/`, builder, archer-builder etc.):**
- [ ] No `.gitignore` required (it's all private)
- [ ] Can contain: CLAUDE.md content, preferences, lessons-learned, household reference, real family names and addresses
- [ ] One .env file at root (if needed); excluded from being tracked with `.gitignore`

**Public repo (`output/`, deliverable):**
1. [ ] No `for-claude/` files present — only `output/` contents
2. [ ] Search for family names, home nicknames, email addresses (same as single-repo scenario)
3. [ ] No root-level `archimedes-overview-for-kurt.md` — this is private-only
4. [ ] No references to private repo structure or filenames
5. [ ] Verify .env, .gitignore excludes secrets
6. [ ] Verify CLAUDE.md exists locally but is not committed
7. [ ] Commit and push when clean

---

## How to Handle Root Overview Files

### The Problem

Root overview files (`archimedes-overview-for-kurt.md`, `{project}-overview-for-kurt.md`) contain Kurt's orientation: family, homes, financial context, deep project context. This data is private and should never ship to public repos.

### The Solution

**Pattern:** Private root file + optional public-safe output slice

1. **Root file is private.**
   ```
   ~/{your-cowork-root}/{project}/
   └── {project}-overview-for-kurt.md  ← Local only, never committed
   ```

2. **If the project needs public-facing overview:**
   - Create a public-safe version in `output/`
   - Different filename (e.g., `output/README.md` or `output/getting-started.md`)
   - Remove all PII, family references, financial context
   - Example: "This system helps manage properties" instead of "Kurt owns 4 homes..."

3. **For Archimedes specifically:**
   - Root `archimedes-overview-for-kurt.md` is private (local only)
   - Public `output/` has a separate overview (or uses `README.md`)
   - The public version doesn't mention Kurt by name or reference his household

---

## Secrets Management (Quick Reference)

See the security audit (forthcoming) for complete guidance. For now:

- **Never commit .env files** — always in `.gitignore`
- **Never hardcode API keys** — use environment variables
- **Never include credentials in examples** — use `{PLACEHOLDER}` format
- **Private repos are not a substitute for secret management** — even private repos should exclude .env
- **Service account keys** — if shared across projects, store in secure location outside git (e.g., `~/.claude/secrets/`), referenced by path in CLAUDE.md

---

## Questions & Edge Cases

**Q: Can I show an example email address?**
- A: Yes, if it's generic (`example@example.com`, `hello@example.local`) or fictional. Never use real personal email addresses.

**Q: What if my project genuinely needs to publish Kurt's family context?**
- A: Talk to Kurt first. Provide both the uncensored and censored versions. Get explicit approval before pushing. Flag it in the commit message.

**Q: Can I commit to a private GitHub repo if I .gitignore the .env?**
- A: Yes. Private repos can contain PII if the project needs it. Still .gitignore secrets and service account keys. The registry, preferences, household reference can live in the private builder repo.

**Q: What about logging or console output that leaks PII?**
- A: Same rule — never log family names, addresses, or other PII to stdout or log files that might be committed or exposed. If you must log sensitive data for debugging, make sure logs are in `.gitignore`.

---

## Updates Triggered By

- **Decision #30 & #122:** PII classification and per-project sensitivity levels
- **Decision #129:** Three-layer security approach (toolkit, audit, conventions)
- **Roadmap ID-11:** Security conventions — Phase 3/5 deliverable

---

## See Also

- **Full cleanup checklist:** `output/templates/cleanup-checklist-template.md` — includes pre-push security checks (Section 12 in Full mode)
- **Security audit** (forthcoming): One-time sweep of Kurt's current setup, repos, and exposure risks
- **Per-tool security considerations** (forthcoming): Expanded toolkit doc with security sections per tool
