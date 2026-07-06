# Global Instructions (Claude Code behavior — not MyAppBuddy-specific)

These rules apply across all projects/repos regardless of business context
(MyAppBuddy, TSM, or otherwise). Kept in this repo alongside the MyAppBuddy
portfolio `CLAUDE.md` for sync convenience across machines, but scoped
independently — see that file's own heading for why.

## Confluence user stories — always check against TSMPlus source
Whenever any Claude Code session references, writes, reviews, or edits a Confluence user story for TSMPlus, regardless of which project or repo is currently open:

1. Locate the TSMPlus source code at `e:\firebird\firebird-dapr`. If not already in that directory, navigate there for the check.
2. Run `git pull` first — never reason about the codebase from a potentially stale checkout.
3. Search the actual codebase for what the story assumes: existing services, schema, field names, dependent stories (e.g. RBAC/field-visibility rules like SEC-FV-001).
4. Explicitly call out:
   - Schema/migration implications
   - Which services/files are affected
   - Assumptions the story makes that aren't yet true in code (e.g. "this story assumes SEC-FV-001 is implemented — confirm before committing to this UAC")
   - Naming or architecture mismatches between the story's language and the actual code
5. Do not treat a Confluence story as complete or accurate based on its text alone — if the source-code check can't be completed (e.g. relevant file not found), flag that as a gap rather than skipping it silently.

## /init discipline — prune before presenting
Whenever /init is run (new project or refresh of an existing CLAUDE.md):
1. Generate the draft as normal.
2. Before presenting it for approval, review every line and ask: "Would removing this cause a mistake Claude would otherwise make?"
   - If Claude could already infer it from reading the code (standard language/framework conventions, obvious file structure, self-documenting patterns) — cut it.
   - Keep: non-obvious build/test commands, project-specific conventions that deviate from defaults, architectural decisions not visible from file structure alone, and repo-specific gotchas.
3. Present the pruned version, not the raw draft, and briefly note what was cut and why.
4. Do the same self-review whenever CLAUDE.md is edited later (e.g. via # quick-adds) — if a file grows past ~60-80 lines, flag it and suggest what could move to a skill or path-scoped rule instead of staying in CLAUDE.md.

## Confluence access (TSMPlus / TDDP space)
- Cloud ID: `1134fb9e-8a07-4e11-b96f-64ed92b2cf3d`
- Space: TDDP (spaceId `1844903941`)
- Story Template page: `1845723141`
- New stories: created as Live Docs (not drafts) via `POST /wiki/rest/api/content/{templatePageId}/copy`, parented under the relevant EPIC page.
- Template structure (10 sections): (1) User Story, (2) Scope & Boundaries, (3) User Acceptance Criteria (Given/When/Then, IDs like `StoryID-S#-#`), (4) Non-Functional Requirements, (5) UX Snippets, (6) Architectural Notes, (7) Technical Notes, (8) Development Tickets, (9) Azure DevOps Delivery, (10) Evidence & Attachments. UAT sections (11+) added separately once ready for testing.
