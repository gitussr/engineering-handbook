# Extension Guide — Adding a New Content Package

Scope: the concrete, step-by-step mechanism for "the platform must never require architectural
changes when new handbooks are added" (`prompt.txt` §1) and "new documentation packages can be
added without modifying application code" (`prompt.txt` §9). This is the document that either
proves or disproves the platform's central bet — see
[24-risk-assessment.md](24-risk-assessment.md) R12.

## What a "content package" is

A content package is a self-contained handbook (Linux today; SQL, Python, JavaScript, Docker,
Kubernetes, DevOps, Cloud, Cybersecurity, Networking, Operating Systems, Database Design, System
Design, AI Engineering, MLOps, or any future discipline per `prompt.txt` §1) with its own module
roadmap, its own content folder, and its own entry in the package registry — structurally
identical in shape to Linux, never a special case in the core.

## Steps to add a package (net new, zero core changes)

1. **Author the roadmap** for the new package, following the same pattern as
   [01-roadmap.md](01-roadmap.md): staged modules, sub-topics, difficulty labels
   (🟢/🟡/🔴). This is content planning, not code.
2. **Create the content folder**, mirroring [02-folder-structure.md](02-folder-structure.md)'s
   shape:
   ```
   content/
   └── packages/
       └── {package-slug}/          # e.g. "python", "docker"
           ├── docs/
           │   ├── 01-{module}/
           │   └── ...
           ├── commands/             # if applicable — e.g. Python has no "commands" in the
           │                         # Linux sense, but Docker/SQL might reuse this shape
           ├── cheatsheets/
           ├── interview-questions/
           ├── labs/
           └── projects/
   ```
   Every page uses the same frontmatter contract as any existing page
   ([07-seo-jsonld.md](07-seo-jsonld.md)) — `module`, `difficulty`, `careerRelevance`, etc. — with
   `module` values namespaced to the new package's own roadmap numbering.
3. **Register the package** — one entry, one file, no core code touched:
   ```json
   // data/packages/{package-slug}.json
   {
     "id": "python",
     "label": "Python Handbook",
     "accent": "oklch(65% 0.15 260)",   // validated per 12-theme-specification.md
     "moduleCount": 0,                    // populated at build time
     "status": "in-progress"              // "planned" | "in-progress" | "published"
   }
   ```
4. **Nothing else changes.** Navigation ([03-navigation-sidebar.md](03-navigation-sidebar.md)),
   the sitemap ([19-seo-strategy.md](19-seo-strategy.md)), the search index
   ([05-search-taxonomy.md](05-search-taxonomy.md)), and the homepage's "Documentation Categories"
   section all read from the package registry and each package's content collection at build
   time — none of them hardcode "Linux" as a special case, so a second registry entry is a second
   entry in every one of those surfaces automatically.

## What must never be touched to add a package

| Never modified for a new package | Why |
|---|---|
| `src/core/ports/*` (once code exists) | Ports are package-agnostic by construction ([13-plugin-architecture.md](13-plugin-architecture.md)) |
| The frontmatter schema itself | A package can use existing optional fields or propose a genuinely new optional field via the ADR process ([27-contribution-guide.md](27-contribution-guide.md)), but never redefine an existing required field's meaning |
| The URL pattern | `/docs/{module-slug}/{topic-slug}` already has room for any package's module/topic slugs ([04-url-structure.md](04-url-structure.md)) — no package-specific URL segment is needed or added |
| The theme token file, beyond one accent value | Per [12-theme-specification.md](12-theme-specification.md) |
| Any existing package's content | Cross-package links are forward-only and additive ([19-seo-strategy.md](19-seo-strategy.md) — see note) — an existing package is never edited just because a new one launched, except to *add* an optional forward link once the new package is mature enough to be worth linking to |

## Package-specific variation that IS allowed

- **Content shape differences.** Not every package needs every content type Linux has — a
  package with no CLI commands (e.g., a pure SQL handbook) simply omits the `commands/` folder;
  the registry's `moduleCount` and content-collection tooling handle an absent optional folder
  without special-casing it.
- **Package-scoped plugins** ([13-plugin-architecture.md](13-plugin-architecture.md)) — e.g., a
  future Code Playground plugin activating only for packages with runnable code (Python,
  JavaScript), registered per-package, not globally.
- **Career mapping extensions** — new packages extend the existing 12-role module mapping
  ([06-career-paths.md](06-career-paths.md)) with new primary/secondary module references; the
  10-tag `careerRelevance` taxonomy is stable and reused as-is, not redefined per package.

## Validating a package addition didn't leak into core

Per [23-testing-strategy.md](23-testing-strategy.md)'s content-validation stage, a new package's
first PR is checked with: does the diff touch anything outside `content/packages/{slug}/` and
`data/packages/{slug}.json`? If yes, that diff is the signal the abstraction has a gap — flagged
per [24-risk-assessment.md](24-risk-assessment.md) R12, not silently merged as a one-off exception.

## Launch checklist (per package)

- [ ] Roadmap authored and reviewed (module/topic list, difficulty labels)
- [ ] Content collection populated, passing all content-validation checks
- [ ] Registry entry added, `accent` color passes contrast validation
- [ ] Sitemap, search index, navigation, and homepage category grid confirmed to include the
      package with zero manual wiring
- [ ] Accessibility manual pass completed for the package's page types
      ([20-accessibility-strategy.md](20-accessibility-strategy.md))
- [ ] Package status flipped from `"in-progress"` to `"published"` in the registry
