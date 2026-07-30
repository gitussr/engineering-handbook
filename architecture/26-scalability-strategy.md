# Scalability Strategy

Scope: scaling along the axis `prompt.txt` actually cares about — "unlimited future documentation
packages" (§1) — plus the build-performance and search-index consequences of that growth. This is
distinct from traffic scaling, which a static, CDN-served site gets close to for free.

## The four axes that actually grow

| Axis | Grows from | Grows to | Primary lever |
|---|---|---|---|
| Content volume | ~218 pages, 1 package (Linux) | Unbounded pages, unbounded packages | Per-package content collections, package registry ([28-extension-guide.md](28-extension-guide.md)) |
| Build time | Single-package build | N-package build | Incremental/cached builds, per-package build isolation |
| Search index size | Single Pagefind index | Per-package sharded indexes | Index sharding (below) |
| Contributor base | Small team | Potentially community-driven (`prompt.txt` §6) | Contribution workflow ([27-contribution-guide.md](27-contribution-guide.md)) |

Traffic/read-scaling is explicitly *not* a primary concern: a fully static site served via GitHub
Pages or any CDN-backed host ([18-deployment-strategy.md](18-deployment-strategy.md)) scales reads
by adding edge cache capacity, not by any decision this architecture needs to make.

## Content volume scaling

- Every package is its own top-level content collection ([28-extension-guide.md](28-extension-guide.md)),
  never a subfolder requiring the existing Linux collection's schema to be renegotiated. Adding
  package #2 changes zero lines in package #1's content, frontmatter schema, or folder layout.
- The one shared thing across all packages — the frontmatter contract from
  [07-seo-jsonld.md](07-seo-jsonld.md) — is versioned; a new package can extend it with
  package-specific optional fields but cannot remove or repurpose a required field, since the
  knowledge-graph linking depends on every package honoring the same core contract.

## Build time scaling

- **Per-package build isolation.** Astro's content-layer caching keys off content that actually
  changed — editing one Linux page should not force reprocessing the SQL package's content.
- **Incremental CI.** [17-github-actions-workflow.md](17-github-actions-workflow.md)'s
  content-validation and Pagefind-indexing stages scope to changed packages where the tooling
  supports it, falling back to a full rebuild only when shared core (theme tokens, ports, the
  frontmatter schema itself) changes — a shared-core change legitimately does need to revalidate
  everything, since every package depends on it.
- **Parallelizable by package.** Because packages don't depend on each other's content (per the
  cross-package linking rule in [19-seo-strategy.md](19-seo-strategy.md) — simpler package links
  forward, never the reverse), package builds are independent and can run in parallel CI jobs
  once the page count makes serial builds a bottleneck.

## Search index scaling

- **Per-package Pagefind indexes**, unified at query time behind the single `SearchPort`
  ([13-plugin-architecture.md](13-plugin-architecture.md)) — the search UI never knows or cares
  how many index shards exist; it queries one port and gets ranked, cross-package results.
- If a single search provider genuinely cannot handle the eventual combined index size or query
  volume, the adapter swaps (per the plugin architecture's entire reason for existing) — the UI,
  the query contract, and every page's `keywords`/`body` frontmatter fields are unaffected by that
  swap.

## Contributor scaling

- Today: small-team, PR-reviewed content (see [27-contribution-guide.md](27-contribution-guide.md)).
- At community scale (`prompt.txt` §6, Community Contributions): the same per-page, one-concept
  file structure ([02-folder-structure.md](02-folder-structure.md)) that keeps content
  merge-conflict-resistant today is what makes many simultaneous external contributors viable
  later — a mega-page model would make concurrent community edits far more conflict-prone.
- Module/package ownership (a designated reviewer per package, once packages multiply) is the
  anticipated review-scaling mechanism — not yet needed at one package, explicitly flagged here so
  it isn't invented ad hoc under pressure once package count makes single-reviewer review
  impractical.

## What does NOT need a scaling strategy yet

- Database read/write scaling — no database exists ([16-future-database-design.md](16-future-database-design.md)
  is forward design only).
- Multi-region deployment — a static, CDN-served site is already effectively multi-region via the
  CDN's own edge network; no additional architecture is needed on this platform's side.
- Horizontal compute scaling for a "server" — there is no server today
  ([18-deployment-strategy.md](18-deployment-strategy.md)).

## The real scaling test

Per [24-risk-assessment.md](24-risk-assessment.md) R12, none of the above is proven until package
#2 actually ships and the build/search/content pipeline is observed handling two packages instead
of one — this strategy is the plan; [30-development-milestones.md](30-development-milestones.md)
is where it gets checked against reality.
