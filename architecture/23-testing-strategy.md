# Testing Strategy

Scope: what gets tested, at what layer, and why — for a platform where most of the surface area
is content (Markdown), not application logic, which makes this pyramid shaped differently from a
typical product's.

## The pyramid, reordered for a content-heavy platform

A conventional testing pyramid (many unit tests, fewer integration, few E2E) undersells the
biggest actual risk on this platform: **content defects at scale** (broken links, missing
prerequisites, orphaned pages) — exactly what the Module 01 audit
([11-module-01-audit.md](11-module-01-audit.md)) found by hand on a single module. That doesn't
scale to 218+ pages across unlimited future packages by manual review alone, so content validation
sits alongside, not below, code-level testing.

| Layer | What it covers | Tooling class | Runs |
|---|---|---|---|
| Content validation | Frontmatter schema, internal link integrity, prerequisite ordering, orphan detection | Custom script against the content collection | Every PR |
| Unit tests | Adapters against their port contract ([13-plugin-architecture.md](13-plugin-architecture.md)), utility functions | Standard TS test runner | Every PR |
| Component tests | Astro component rendering in isolation (props → expected markup) | Astro's component testing tooling | Every PR |
| Accessibility tests | Automated WCAG rule checks against sample routes | axe-core | Every PR |
| Performance tests | Lighthouse budget gate ([21-performance-budget.md](21-performance-budget.md)) | Lighthouse CI | Every PR |
| E2E / smoke tests | Critical user paths end-to-end | Browser automation against a built preview | Every PR (fast, narrow set) |
| Visual regression | Theme token changes, layout shifts | Screenshot diffing | On theme/component-affecting PRs only |
| Manual accessibility pass | Real assistive-tech experience | Screen reader | Per package launch / structural nav change ([20-accessibility-strategy.md](20-accessibility-strategy.md)) |

## Content validation, in detail

- **Frontmatter schema validation.** Every required field from
  [07-seo-jsonld.md](07-seo-jsonld.md)'s frontmatter contract is present and correctly typed
  (`difficulty` is one of the three valid enum values, not a free-text string, etc.) — validated
  against a schema, not by convention.
- **Link integrity.** Every `prerequisites`, `relatedTopics`, `relatedCommands`, `nextTopic`,
  `prevTopic` reference resolves to a page that actually exists in the build — a dangling
  reference fails the build rather than shipping a 404 behind a "Related Topics" link.
- **Prerequisite ordering.** No topic lists a prerequisite that appears later in its own module's
  roadmap order ([01-roadmap.md](01-roadmap.md)) — this is the automated version of the
  "forward-reference" check already described as a manual rule in
  [07-seo-jsonld.md](07-seo-jsonld.md).
- **Orphan detection.** Every page is reachable from at least one `prevTopic`/`nextTopic` chain,
  module index, or hub grid — a page with no inbound link is a content defect (unreachable via
  navigation) even if its URL technically resolves.

## Critical E2E paths (kept deliberately narrow)

Only paths where a regression would break the platform's core promise, not exhaustive UI coverage:

1. Search: type a query in the global search bar → results appear → click a result → correct page
   loads.
2. Navigation: expand a sidebar module → click a topic → breadcrumb and Prev/Next reflect the
   correct position.
3. Dark mode: toggle theme → persists across a page reload (localStorage) → no FOUC.
4. Quiz interaction: click a `<details>` question → answer reveals → collapses again.

Kept narrow deliberately — broad E2E suites are slow and brittle; the content-validation and
component-test layers above catch most regressions faster and more precisely, so E2E is reserved
for cross-cutting behaviors no lower layer can observe.

## Testing plugin adapters ([13-plugin-architecture.md](13-plugin-architecture.md))

Every adapter is tested against its port's contract using a **contract test suite** shared across
all adapters implementing that port — e.g., one `SearchPort` contract test suite runs against
both the Pagefind adapter and any future replacement, guaranteeing a swapped adapter can't
silently violate behavior the UI depends on (empty query handling, facet filtering, result
ordering).

## What is explicitly not tested

- Visual pixel-perfection across every one of 218+ pages — visual regression testing runs against
  representative templates (topic page, command page, hub page), not every generated page, since
  they share the same component tree and testing every instance would be redundant with testing
  the template once.
- Third-party service uptime (Pagefind's hosted infra, if any; a future auth provider's uptime) —
  out of this platform's test scope; graceful-degradation behavior (e.g., search UI shows an error
  state) is tested, the provider's own reliability is not.

## Coverage philosophy

No blanket line-coverage percentage target is mandated. Coverage is judged by: does every port
have a contract test, does every content-validation rule have a passing and a failing fixture, do
the critical E2E paths pass — a high line-coverage number on trivial code is not treated as
evidence of quality, per the project's general "no filler" discipline
([WRITING_RULES.md](../WRITING_RULES.md) applies the same principle to content).
