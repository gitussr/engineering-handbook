# URL Structure

## Pattern by content type

| Content type | URL pattern | Example |
|---|---|---|
| Docs topic page | `/docs/{module-slug}/{topic-slug}` | `/docs/permissions/chmod-symbolic-and-octal` |
| Docs module landing | `/docs/{module-slug}` | `/docs/permissions` |
| Developer Essentials topic | `/developer-essentials/{slug}` | `/developer-essentials/git-basics` |
| Developer Essentials hub | `/developer-essentials` | `/developer-essentials` |
| Roadmap | `/roadmap` | `/roadmap` |
| Command reference (single) | `/commands/{command}` | `/commands/chmod` |
| Command reference (index) | `/commands` | `/commands` |
| Career page | `/careers/{role-slug}` | `/careers/devops-engineer` |
| Career hub | `/careers` | `/careers` |
| Cheat sheet | `/cheatsheets/{sheet-slug}` | `/cheatsheets/ssh` |
| Cheat sheet hub | `/cheatsheets` | `/cheatsheets` |
| Interview question bank | `/interview-questions/{level}` | `/interview-questions/senior` |
| Interview company-type set | `/interview-questions/company-wise/{category-slug}` | `/interview-questions/company-wise/faang-style` |
| Practice project | `/projects/{tier}/{project-slug}` | `/projects/intermediate/build-a-log-rotation-script` |
| Production lab | `/labs/{tier}/{lab-slug}` | `/labs/production/debug-a-failing-nginx-deployment` |
| Search results | `/search?q={query}` | `/search?q=chmod` |

## Why `/docs/{module-slug}/{topic-slug}` and not `/docs/{topic-slug}` flat

- Module numbers (`01-`, `07-`) are dropped from the URL — `07-permissions` → `permissions`.
  Numbers are a roadmap/ordering concern, not a URL concern; inserting a new topic between two
  modules later shouldn't force a URL renumbering.
- Two-level nesting keeps topic slugs short and collision-free (`processes/kill-signals` vs.
  a flat site needing `processes-kill-signals`) while still being only one level deeper than
  flat — good for both SEO breadcrumbs and skimmability.
- Commands are pulled OUT of the module nesting into `/commands/{command}` (flat, one canonical
  page per command) because the same command (`ps`, `grep`, `kill`) is referenced from multiple
  modules — nesting it under one module would make cross-module links either wrong or duplicated.
  Docs topic pages embed/transclude the relevant command reference rather than re-hosting it.

## Slug rules

- All lowercase, hyphen-separated, no stop-word stripping needed but keep slugs under ~60 chars.
- Module slug = the module name from the roadmap with the number stripped (`10 Processes` →
  `processes`).
- Topic slug = short version of the topic title, not the full sentence (`chmod (symbolic and
  octal)` → `chmod-symbolic-and-octal`).
- No trailing slashes; canonical URLs are enforced at build time (redirect trailing-slash
  variants to non-trailing-slash canonical).

## Redirects / stability policy

- Once a topic page ships, its URL is permanent. If a topic is renamed, the old slug 301-redirects
  to the new one — content URLs are treated as a public API once published (learners bookmark
  and share deep links into specific commands/topics).
- Module reordering (e.g. inserting a new Module between 17 and 18) never changes existing URLs,
  since module numbers aren't in the URL — only `data/roadmap.json` ordering changes.
