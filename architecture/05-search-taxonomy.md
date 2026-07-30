# Search Taxonomy

## What gets indexed

Per the brief: Commands, Concepts, Career pages, Interview questions, Labs, Cheat sheets — i.e.
every content type in [02-folder-structure.md](02-folder-structure.md), indexed into one unified
search rather than separate per-section search boxes.

## Index record schema

Each indexed document (built from Markdown frontmatter at build time into
`data/search-index.json`):

```json
{
  "id": "docs/permissions/chmod-symbolic-and-octal",
  "type": "command | concept | career | interview | lab | cheatsheet | project",
  "title": "chmod (Symbolic and Octal)",
  "description": "One-line summary shown under the search result title",
  "module": "07-permissions",
  "moduleTitle": "Permissions",
  "stage": "beginner | intermediate | professional | production-engineer | reference",
  "difficulty": "must-know | good-to-know | expert",
  "url": "/docs/permissions/chmod-symbolic-and-octal",
  "keywords": ["chmod", "permissions", "rwx", "octal", "755", "644"],
  "relatedCommands": ["chown", "umask", "setfacl"],
  "body": "<plain-text extract for full-text match, stripped of markdown/code fences>",
  "updatedAt": "2026-07-25"
}
```

## Facets (filters shown alongside search results)

- **Type**: Command / Concept / Career / Interview Question / Lab / Cheat Sheet / Project
- **Stage**: Beginner / Intermediate / Professional / Production Engineer
- **Difficulty**: 🟢 Must Know / 🟡 Good to Know / 🔴 Expert
- **Module**: any of the 34 (only shown when Type = Concept or Command)

## Ranking rules

1. Exact title match (e.g. query `chmod` matches the `chmod` command page title exactly) ranks
   above partial/keyword matches.
2. `keywords` match ranks above `body` full-text match.
3. Among equal-relevance matches, 🟢 Must Know content ranks above 🟡 and 🔴 — a beginner typing
   "permissions" should see the core `chmod` page before the ACL deep dive.
4. Command pages (`type: command`) and concept pages are interleaved by relevance, not
   type-segregated — but each result card visibly tags its type so a learner searching "kill"
   can tell the `kill` command page apart from the "Processes" concept page at a glance.

## Search UX behavior

- Global search bar (present in top nav on every page, plus the large hero search on `/`) opens
  an instant-results dropdown after 2+ characters (client-side fuzzy match against
  `search-index.json`), with "View all results for '{query}'" linking to `/search?q={query}`
  for the full filterable results page.
- Keyboard shortcut `/` focuses the search bar from anywhere (documented in the UI itself, not
  just here).
- Empty/no-result state suggests: check the Command Reference (`/commands`), or browse the
  Roadmap (`/roadmap`) — never a dead end.

## Source of truth

`data/search-index.json` is generated, never hand-edited — see the "generated, not hand-authored"
rule in [02-folder-structure.md](02-folder-structure.md). Every content Markdown file's
frontmatter must include `title`, `description`, `keywords`, `difficulty`, and `module` for this
to work; these become required frontmatter fields, enforced by the Phase 2 content template
(defined when chapter generation starts).
