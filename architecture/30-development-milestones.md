# Development Milestones

Scope: concrete, checkable phase gates from the current state to a validated multi-package
platform. Each milestone has explicit exit criteria — a milestone is "done" when its criteria are
verifiably true, not when work has generally progressed.

## Phase 0 — Information Architecture (done)

**Status: complete.** `architecture/00-11` — roadmap, folder structure, navigation, URLs, search
taxonomy, career paths, SEO/JSON-LD, content inventory, developer essentials, content templates,
and the Module 01 quality audit. Approved 2026-07-25 ([00-README.md](00-README.md)).

## Phase 1 — Platform Architecture (this batch, 12–30)

**Exit criteria:**
- [x] Theme, plugin, auth, premium, future-DB, CI/CD, deployment, SEO, accessibility, performance,
      coding standards, testing, risk, security, scalability, contribution, extension, roadmap,
      and milestone documents exist and cross-reference consistently.
- [ ] Reviewed against `prompt.txt`'s acceptance criteria (§9) line by line before being treated
      as approved — not yet marked approved; this batch is a draft pending that review, consistent
      with how Phase 0 required explicit approval before Phase 2 began.

## Phase 2 — Linux Content Generation (underway, per 00-README.md)

**Exit criteria:**
- [x] Module 01 piloted and audited ([11-module-01-audit.md](11-module-01-audit.md)); template
      locked (`CONTRIBUTING.md`, `STYLE_GUIDE.md`, `TERMINOLOGY.md`, `WRITING_RULES.md`).
- [ ] Modules 02–34 generated against the locked template.
- [ ] Cheat sheets (10), labs (4 tiers), projects (3 tiers), interview question banks, and 12
      career pages complete.
- [ ] Developer Essentials section complete.

## Phase 3 — Framework Scaffold

Not started. First point at which application code is written — everything before this phase is
architecture and content, per `prompt.txt` §8.

**Exit criteria:**
- [ ] Astro + Starlight project scaffolded; Tailwind v4 theme tokens
      ([12-theme-specification.md](12-theme-specification.md)) implemented as the actual `@theme`
      block.
- [ ] Content collections wired to `content/docs`, matching
      [02-folder-structure.md](02-folder-structure.md) exactly (no schema surprises versus what
      Phase 2 content already assumes).
- [ ] Shiki, Mermaid (with `client:visible` hydration), Lucide integrated per their budget
      allocations in [21-performance-budget.md](21-performance-budget.md).
- [ ] Pagefind wired behind a `SearchPort` adapter, not called directly from UI
      ([13-plugin-architecture.md](13-plugin-architecture.md)).
- [ ] CI pipeline ([17-github-actions-workflow.md](17-github-actions-workflow.md)) running for
      real, all five PR-blocking gates active.

## Phase 4 — Linux Package Launch

**Exit criteria:**
- [ ] Full Linux content package live on GitHub Pages.
- [ ] Lighthouse Performance ≥98, Accessibility/SEO/Best Practices = 100, measured against the
      real deployed site, not a local build — the `prompt.txt` §3 numbers are a launch gate, not
      an aspiration.
- [ ] CLS < 0.05, LCP < 1.5s, INP excellent, measured via field data (real users) once traffic
      exists, not lab data alone.
- [ ] JS budget ≤150KB confirmed on the deployed build.
- [ ] Manual accessibility (screen reader) pass completed
      ([20-accessibility-strategy.md](20-accessibility-strategy.md)).

## Phase 5 — Extensibility Validation (package #2)

**The load-bearing milestone** — this is where [24-risk-assessment.md](24-risk-assessment.md) R12
is either confirmed or falsified.

**Exit criteria:**
- [ ] Second content package (per [29-future-roadmap.md](29-future-roadmap.md)'s sequencing)
      shipped end-to-end following [28-extension-guide.md](28-extension-guide.md)'s steps exactly.
- [ ] Diff audit confirms the package's first PR touched only
      `content/packages/{slug}/` and `data/packages/{slug}.json` — any core file touched is
      logged as an architecture defect and triaged via [27-contribution-guide.md](27-contribution-guide.md),
      not silently accepted.
- [ ] Navigation, sitemap, search index, and homepage category grid all reflect the new package
      with zero manual wiring, confirmed by inspection.

## Phase 6 — Dynamic Feature Activation

Not started; gated on Phase 5 succeeding and a real feature (from
[29-future-roadmap.md](29-future-roadmap.md)'s dependency table) being greenlit.

**Exit criteria (whichever feature is greenlit first):**
- [ ] Hosting migration executed per [18-deployment-strategy.md](18-deployment-strategy.md), with
      zero content changes required — this specific criterion validates the portability acceptance
      criterion in `prompt.txt` §9 directly.
- [ ] First real adapter implemented against `AuthPort` and/or `EntitlementPort`
      ([14](14-authentication-architecture.md), [15](15-premium-feature-architecture.md)), proving
      those ports were specified correctly without having been exercised until now.

## How this list is used

Each phase's checkboxes are the literal definition of "done" for that phase — a phase is not
advanced past based on elapsed time or effort, only on its exit criteria being verifiably true.
This file is updated (checkboxes ticked, not rewritten) as each criterion is met, per
[27-contribution-guide.md](27-contribution-guide.md)'s "additive, standard PR review" change
category.
