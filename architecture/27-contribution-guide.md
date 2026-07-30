# Contribution Guide (Architecture Governance)

Scope: how the *architecture itself* changes over time — distinct from the root
[CONTRIBUTING.md](../CONTRIBUTING.md), which governs content contributions (new topic pages,
command pages, cheat sheets). This document governs changes to files in `architecture/` and to
the core (ports, theme tokens, frontmatter schema) that every content package depends on.

## Why architecture changes need a different process than content changes

A typo fix in a `chmod` topic page and a change to the frontmatter schema in
[07-seo-jsonld.md](07-seo-jsonld.md) are not the same kind of change. The second one is binding on
every one of 218+ existing pages and every future package
([28-extension-guide.md](28-extension-guide.md)) — it needs a higher bar than one reviewer's
approval on a single PR.

## Change categories and required process

| Change type | Example | Process |
|---|---|---|
| New architecture doc (net new file, additive) | Adding this very document | Standard PR review — no existing behavior changes |
| Clarification / typo in an existing architecture doc | Fixing a broken cross-link | Standard PR review |
| Non-breaking extension to a shared contract | Adding an optional frontmatter field | Standard PR review, plus updating the schema validation in [23-testing-strategy.md](23-testing-strategy.md) |
| Breaking change to a shared contract | Renaming a required frontmatter field, changing a port's method signature | Requires an ADR (see below) and a migration note for every existing consumer |
| Reversal of a previously "Approved" decision (per [00-README.md](00-README.md)'s approval log) | Re-opening the URL nesting scheme in [04-url-structure.md](04-url-structure.md) | Requires an ADR explicitly superseding the prior approval, not a silent edit |

## Architecture Decision Records (ADR)

`prompt.txt` §7 names ADRs as their own deliverable (item 03); no dedicated `03-*.md` file exists
under that number in this repo (see the numbering note in [00-README.md](00-README.md)), so the
practice is specified here instead of left unowned. An ADR is a short, immutable record — once
merged, it is superseded by a later ADR, never edited in place:

```markdown
# ADR-{number}: {short title}

Status: Proposed | Accepted | Superseded by ADR-{n}
Date: {YYYY-MM-DD}

## Context
{What forced this decision — a constraint, a scaling limit hit, a new requirement.}

## Decision
{What was decided, stated as a single clear sentence.}

## Consequences
{What becomes easier, what becomes harder, what is now locked in.}
```

ADRs are created for exactly the "breaking change" and "reversal" rows in the table above — not
for every architecture doc edit, which would turn a lightweight record into bureaucratic overhead
disproportionate to a clarifying edit.

## Review requirements

- Every architecture PR requires at least one review approval, same floor as content
  ([17-github-actions-workflow.md](17-github-actions-workflow.md) branch protection).
- A breaking-change PR (per the table above) additionally requires the ADR to be merged first, or
  in the same PR, and requires the PR description to enumerate every existing document/component
  the change affects — reviewers check that list against the actual diff, not just the diff in
  isolation.

## Keeping documents from drifting out of sync

- Cross-references between architecture docs use relative Markdown links
  (`[04-url-structure.md](04-url-structure.md)`), never restated content — if two documents
  need to agree on a fact (e.g., the JS budget number), it is stated once, in the document that
  owns it, and linked from everywhere else. A PR that duplicates a fact instead of linking to its
  owning document is a review-blocking issue, not a style nit — this is the same "one concept, one
  owning page" discipline the Module 01 audit established for content
  ([11-module-01-audit.md](11-module-01-audit.md)), applied to architecture docs themselves.
- [00-README.md](00-README.md)'s file index table is updated in the same PR that adds or renames
  an architecture document — never left to a follow-up.

## Who this guide is for

Anyone proposing a change to `architecture/*`, `src/core/*` (ports, once code exists), the theme
token file, or the frontmatter schema — not content authors, who follow the root
[CONTRIBUTING.md](../CONTRIBUTING.md), [STYLE_GUIDE.md](../STYLE_GUIDE.md),
[TERMINOLOGY.md](../TERMINOLOGY.md), and [WRITING_RULES.md](../WRITING_RULES.md) instead.
