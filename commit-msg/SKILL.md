---
name: commit-msg
description: >
  Ultra-compressed commit messages generator. Cuts noise from commit messages while preserving intent and reasoning. Conventional Commits format. Subject <= 50 chars, body only when "why" isn't obvious. Group files into multiple commits when changes span different concerns. Use when user says "write a commit msg", "commit message", "generate commit", or invokes /commit. Auto-triggers when staging changes.
---

Write commit messages terse and exact. Conventional Commits format. No fluff. Why over what.

## Workflow

1. Run `git status` and `git diff` (staged + unstaged) to read latest changes
2. Review the conversation context for what tasks were completed
3. Decide whether changes should be a single commit or grouped into multiple commits
4. Output commit messages

## Rules

**Subject line:**
- `<type>(<scope>): <imperative and concise summary>` — `<scope>` optional
- Types: `feat`, `fix`, `refactor`, `perf`, `docs`, `test`, `chore`, `build`, `ci`, `style`, `revert`
- Imperative mood: "add", "fix", "remove" — not "added", "adds", "adding"
- <=50 chars when possible, hard cap 72
- No trailing period
- Lowercase except for proper nouns, acronyms
- Scope for specifying package/app when working in monorepos: `fix(frontend):`, `docs(backend):`, `ci:`

**Body (only if needed):**
- Skip entirely when subject is self-explanatory
- Add body only for: non-obvious *why*, breaking changes, migration notes, linked issues
- Wrap at 72 chars
- Bullets `-` not `*`
- Reference issues/PRs at end: `Closes #42`, `Refs #17`

**What NEVER goes in:**
- "This commit does X", "I", "we", "now", "currently" — the diff says what
- "As requested by..." — PR already shows co-authors
- "Generated with Claude Code" or any AI attribution
- Emoji
- Restating the file/package name when scope already says it

## Grouping Rules

Group by concern, not by file type.

**Single commit** when:
- All changes serve one purpose
- Less than ~10 files changed across a single concern

**Multiple commits** when:
- Changes span different concerns
- The session completed multiple distinct tasks
- Mixing generated/auto-formatted files with manual changes

## Examples

Diff: API routes change grouped by concerns
- ❌ "feat: change orders to checkout and add delete endpoint"
- ✅
  ```
  1.
  <message>
  feat(api)!: rename /v1/orders to /v1/checkout

  BREAKING CHANGE: clients on /v1/orders must migrate to /v1/checkout
  before 2026-06-01. Old route returns 410 after that date.
  </message>

  <files>
    - api/orders.js
    ...
  </files>

  2.
  <message>
  feat(api): add DELETE /v1/checkout/:id
  </message>

  <files>
    - api/orders.js
    ...
  </files>
  ```

Diff: new endpoint for user profile with body explaining the why
- ❌ "feat: add a new endpoint to get user profile information from the database"
- ✅
  ```
  <message>
  feat(api): add GET /users/:id/profile

  Mobile client needs profile data without the full user payload
  to reduce LTE bandwidth on cold-launch screens.

  Closes #128
  </message>

  <files>
    - api/users.js
    ...
  </files>
  ```

## Auto-Clarity

Always include body for: breaking changes, security fixes, data migrations, anything reverting a prior commit. Never compress these into subject-only — future debuggers need the context.

## Boundaries

Only generates the commit message. Does not run `git commit`, does not stage files, does not amend. Output the message as a code block ready to paste. Flag any suspicious files (large binaries, lock files that shouldn't commit).