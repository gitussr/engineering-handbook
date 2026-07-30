# Performance Budget

Scope: turning the numeric targets in `prompt.txt` §3 into a per-asset-type budget that CI can
actually enforce ([17-github-actions-workflow.md](17-github-actions-workflow.md)), rather than a
single global number nobody can attribute a regression to.

## Top-level targets (restated from `prompt.txt` §3, binding)

| Metric | Target |
|---|---|
| Lighthouse Performance | ≥ 98 |
| Lighthouse Accessibility | 100 |
| Lighthouse SEO | 100 |
| Lighthouse Best Practices | 100 |
| CLS | < 0.05 |
| LCP | < 1.5s |
| INP | Excellent (Google's "good" threshold, sub-200ms) |
| JS shipped, first load | ≤ 150KB compressed |

## Why Astro's architecture is the actual budget enabler

Astro ships zero JavaScript by default per component ("islands architecture") — a page is HTML/CSS
until a component explicitly opts into client-side interactivity (`client:load`,
`client:visible`, etc.). This is the single biggest reason the 150KB budget is achievable at all
for a content-heavy site: **most of the site (prose, code blocks rendered by Shiki at build time,
navigation) ships 0KB of JS**, leaving the entire budget for the handful of genuinely interactive
surfaces.

## Per-surface JS budget (sums to the 150KB ceiling)

| Surface | Budget | Hydration strategy |
|---|---|---|
| Theme toggle + no-FOUC script | ~0.2KB | Inline, blocking (see [12-theme-specification.md](12-theme-specification.md)) |
| Search bar + instant-results dropdown (Pagefind) | ~40KB | `client:idle` — loads after first paint, not blocking LCP |
| Mermaid diagram rendering | ~60KB | `client:visible` — only loads when a diagram scrolls into view, and only on pages that have one |
| Copy Code button | ~1KB | `client:idle`, shared single bundle across every code block on a page |
| Quiz `<details>` interactivity | 0KB | Native HTML, no JS required (per [20-accessibility-strategy.md](20-accessibility-strategy.md)) |
| Analytics (once a provider is registered, [13-plugin-architecture.md](13-plugin-architecture.md)) | ≤ 10KB | `client:idle`, self-hosted script only, never a third-party CDN tag |
| Reserve / future features | ~35KB | Held back deliberately — see below |

Mermaid's `client:visible` strategy matters specifically: a topic page with no diagram never pays
the Mermaid cost at all, and a page with one only pays it once the reader scrolls near it, keeping
LCP unaffected by a diagram library that would otherwise dominate the entire JS budget on its own.

## Why a 35KB reserve is held back on purpose

`prompt.txt` §6 lists a long tail of future interactive features (Code Playground, Flashcards, AI
Tutor, semantic search). None of these exist yet, but the budget is written assuming they will —
holding 35KB in reserve today means a future feature has to fit a real constraint from day one of
its own design, rather than the budget being retroactively renegotiated upward every time
something new ships (the exact kind of budget creep that turns a "150KB budget" into a number
nobody actually enforces).

## Image budget

- **SVG preferred** (`prompt.txt` §3) for all diagrams, icons, and illustrations — vector assets
  scale losslessly across the responsive breakpoints required by "Fully responsive, Mobile-first"
  and never blur on high-DPI displays, unlike a raster fallback would.
- **PNG only where unavoidable** (e.g. an actual terminal screenshot) — served via Astro's image
  optimization pipeline (responsive `srcset`, modern formats like WebP/AVIF with PNG fallback),
  never an unoptimized source file shipped as-is.
- No image on any page is permitted to be the LCP element without an explicit `fetchpriority="high"`
  and correct `width`/`height` attributes — missing dimensions are the most common CLS regression
  source and are treated as a build-time lint failure, not a runtime surprise.

## Font budget

- Self-hosted, per `prompt.txt` §3 ("No external font requests") — variable fonts (one file per
  family covering the full weight range) instead of separate static files per weight, to minimize
  request count and total transfer size.
- Subsetted to the Latin character set required by the documentation's language scope today;
  broadened only when [Localization](29-future-roadmap.md) actually ships a non-Latin-script
  language, not preemptively.
- `font-display: swap` to avoid blocking text render on font load — a deliberate small
  first-paint font flash is preferred over delaying LCP.

## Enforcement

- Lighthouse CI gate ([17-github-actions-workflow.md](17-github-actions-workflow.md)) runs against
  a fixed sample of route types (a topic page, a command page, a hub page, the homepage) on every
  PR — not every one of the 218+ pages, which would make CI prohibitively slow, but enough route
  diversity to catch a component-level regression before it ships everywhere that component is
  used.
- A bundle-size check fails the build if any per-surface budget above is exceeded, attributing the
  regression to the specific surface that grew rather than a single opaque "total JS" number.

## Non-goals

- No CDN/edge-caching configuration — that's a deployment concern
  ([18-deployment-strategy.md](18-deployment-strategy.md)), not a budget concern; this document
  governs what ships from the build, not how it's delivered over the network.
