# SEO Strategy

Scope: site-wide, multi-package technical SEO. [07-seo-jsonld.md](07-seo-jsonld.md) already
specifies per-page frontmatter, JSON-LD schema types, and internal linking rules — this document
is the layer above that: how SEO behaves once there is more than one content package, and how the
`prompt.txt` §3 requirement (Lighthouse SEO = 100) stays true as the platform scales.

## Why multi-package SEO needs its own strategy

A single-package site can get away with ad hoc internal linking and a flat sitemap. Once SQL,
Python, Docker, and a dozen other packages exist alongside Linux
([28-extension-guide.md](28-extension-guide.md)), two new risks appear that per-page metadata
alone doesn't solve:

1. **Keyword cannibalization across packages.** "Containers" is covered in the Linux package
   (Module 22) and will be covered again, more deeply, in a future Docker/Kubernetes package —
   both pages can legitimately want to rank for "what is a container." Cannibalization is
   prevented by canonical scoping, not by avoiding the overlap: the Linux page's `description`
   and `keywords` stay scoped to "containers, the Linux-kernel-feature," and it explicitly links
   forward to the deeper package once one exists, rather than both pages competing for the same
   broad query.
2. **Sitemap and crawl-budget growth.** Search engines allocate a crawl budget per site; a
   sitemap that silently balloons to tens of thousands of URLs across many packages needs
   structure (see Sitemap below) so freshly updated or newly launched package content gets
   crawled promptly instead of waiting behind a flat, undifferentiated URL list.

## Sitemap architecture

- **One sitemap index, one sitemap file per content package**, not a single monolithic
  `sitemap.xml` — `sitemap-index.xml` references `sitemap-linux.xml`, `sitemap-python.xml`, etc.
  (plus one for cross-cutting sections: careers, cheat sheets, interview questions, developer
  essentials).
- Per-package sitemaps let a newly launched package be resubmitted to search consoles
  independently, without re-signaling the entire site as "changed" the way a single monolithic
  file would on every content update anywhere.
- Generated at build time from each package's content collection — never hand-maintained, per the
  "generated, not hand-authored" rule in [02-folder-structure.md](02-folder-structure.md).

## Site-wide structured data (in addition to per-page JSON-LD from 07-seo-jsonld.md)

```json
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Engineering Knowledge Platform",
  "url": "https://{domain}",
  "potentialAction": {
    "@type": "SearchAction",
    "target": "https://{domain}/search?q={search_term_string}",
    "query-input": "required name=search_term_string"
  }
}
```

Emitted once, homepage only (per the existing rule in 07-seo-jsonld.md's schema table) — enables
the sitelinks search box result. An `Organization`/`EducationalOrganization` block is added
site-wide once the platform has a public identity worth marking up (deferred — see
[29-future-roadmap.md](29-future-roadmap.md); premature to add before the brand is finalized).

## Cross-package internal linking (extends the rules in 07-seo-jsonld.md)

- A concept that exists at different depths in two packages (containers in Linux vs. a future
  Docker/Kubernetes package) links **forward only**, Linux → Docker, never the reverse — the
  simpler package should route a reader toward depth; the deeper package shouldn't send readers
  backward into a shallower explanation of a topic it already fully owns.
- Career pages ([06-career-paths.md](06-career-paths.md)) become the natural cross-package hub as
  packages multiply — a role like DevOps Engineer will eventually map into Linux, Docker, and
  Cloud packages simultaneously; the career page absorbs that cross-linking so no single content
  package has to know about the others.

## Canonical URL stability across packages

- Package identifiers become part of the URL only where genuinely needed for disambiguation
  (see [04-url-structure.md](04-url-structure.md) for the existing `/docs/{module}/{topic}`
  pattern) — adding a package must never require renumbering or re-slugging existing Linux URLs,
  per the acceptance criterion "new documentation packages can be added without modifying
  application code."

## Performance-linked SEO (Core Web Vitals as a ranking signal)

Lighthouse SEO = 100 (`prompt.txt` §3) is necessary but not sufficient — Core Web Vitals are a
direct Google ranking factor, independent of the Lighthouse SEO score. The CWV targets (CLS,
INP, LCP) and their enforcement mechanism are owned by
[21-performance-budget.md](21-performance-budget.md); this document only notes the dependency so
SEO strategy isn't reasoned about in isolation from performance.

## Non-goals

- No keyword research, no backlink strategy, no paid-search — all off-page/marketing SEO is
  explicitly out of scope for an architecture document; this file governs only what the platform
  itself controls structurally.
