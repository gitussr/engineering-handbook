# Risk Assessment

Scope: risks to the platform's ten-year horizon (`prompt.txt` §0), assessed across technical,
content, platform, and process dimensions. Each risk names a mitigation already designed
elsewhere in this architecture, or flags where none exists yet.

## Risk register

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Framework lock-in — Astro-specific patterns leak into content or data | Medium | High | Content stays plain Markdown + frontmatter, framework-agnostic by design ([00-README.md](00-README.md), [02-folder-structure.md](02-folder-structure.md)); only the build layer is Astro-specific |
| R2 | Pagefind indexing doesn't scale gracefully past many packages/thousands of pages | Medium | Medium | Per-package index sharding ([26-scalability-strategy.md](26-scalability-strategy.md)); `SearchPort` abstraction allows swapping providers without UI change ([13-plugin-architecture.md](13-plugin-architecture.md)) |
| R3 | GitHub Pages build-time or bandwidth limits become binding as content grows | Low–Medium | Medium | Portability designed in from day one ([18-deployment-strategy.md](18-deployment-strategy.md)); migration doesn't require content changes |
| R4 | GitHub Pages deprecated, changes pricing, or changes limits unfavorably | Low | High | Same mitigation as R3 — this is precisely the scenario portability protects against |
| R5 | JS budget creep — each new interactive feature nudges the 150KB ceiling until it's meaningless | Medium | Medium | Per-surface budget with an explicit reserve, enforced by CI gate, not convention ([21-performance-budget.md](21-performance-budget.md)) |
| R6 | Content accuracy drift — Linux/tooling changes over time, pages go stale | High (inherent to any technical documentation) | Medium | `updatedAt`/`lastReviewed` frontmatter fields make staleness visible ([07-seo-jsonld.md](07-seo-jsonld.md)); no automated fix — this is an ongoing editorial cost, not a solved problem |
| R7 | Single-author/small-team content bottleneck limits how many packages can realistically launch | High | Medium | Community Contributions is a named future feature (`prompt.txt` §6); until then, [29-future-roadmap.md](29-future-roadmap.md) paces package additions to actual authoring capacity, not aspirational schedule |
| R8 | Premium/auth features get partially built ahead of a real hosting migration, creating dead code | Medium | Medium | Auth/entitlements scoped strictly to "architecture only" until migration is actually triggered ([14](14-authentication-architecture.md), [15](15-premium-feature-architecture.md), [18](18-deployment-strategy.md)) |
| R9 | Supply-chain compromise via a dependency or a pinned GitHub Action | Low | High | Lockfile-pinned dependencies, SHA-pinned Actions, minimal token scopes ([25-security-considerations.md](25-security-considerations.md)) |
| R10 | Career/salary data becomes stale or is perceived as fabricated | Medium | High (trust/reputation) | Salary data sourced, dated, independently updatable per record, never hand-written into page bodies ([06-career-paths.md](06-career-paths.md)) — mitigated by design, but still requires a periodic refresh cadence not yet scheduled |
| R11 | Accessibility regresses silently as component count grows | Medium | High | Layered enforcement — token-level, CI-level, manual-pass-level ([20-accessibility-strategy.md](20-accessibility-strategy.md)) |
| R12 | New content package doesn't actually satisfy "no core changes" in practice, and the plugin/package-registry abstraction leaks | Medium | High | This is the platform's central architectural bet — first tested for real the moment package #2 ships ([28-extension-guide.md](28-extension-guide.md), [30-development-milestones.md](30-development-milestones.md)) |
| R13 | Interview-question / company-wise content is perceived as leaked/confidential material | Low | High (legal/reputation) | Mandatory disclaimer on every company-wise page, categorized by company type not named company ([08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)) |
| R14 | Mermaid/Shiki/Pagefind (mandated third-party tools, `prompt.txt` §2) become unmaintained | Low | Medium | All three sit behind the plugin/rendering boundary conceptually, though `prompt.txt` mandates them specifically (not swappable without a spec change) — accepted risk, not mitigated further, since the brief fixes this choice |

## Risk R12 deserves special attention

Every other risk in this register has a designed mitigation already sitting in another document.
R12 is different: it's a claim about the *whole architecture's* validity that cannot be verified
by more design work — only by actually adding a second content package and observing whether it
required touching `src/core`, `src/plugins`, or any file outside `content/packages/{new-package}/`
and its registry entry. [30-development-milestones.md](30-development-milestones.md) schedules
this as an explicit, named milestone (not an implicit side effect of "eventually adding more
content") precisely because it is the architecture's own falsification test.

## Risks explicitly not tracked here

- Market/demand risk (will anyone use this platform) — outside an engineering architecture
  document's scope.
- Individual contributor/team risk (illness, turnover) — a project-management concern, not an
  architectural one.
