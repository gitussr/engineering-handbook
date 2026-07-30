# Engineering Knowledge Platform — Architecture

Status: **Phase 0/1 APPROVED (2026-07-25)** — Phase 2 (Linux content generation) is underway.
Phase 1 platform architecture (files 12–30) drafted 2026-07-30, pending review against
`prompt.txt`'s acceptance criteria — see [30-development-milestones.md](30-development-milestones.md).

This folder contains the complete architecture for the platform: the Linux-specific information
architecture from Phase 0 (per the brief in `linux-documentation.txt` and the approval decisions
in `approval.txt`), plus the platform-level architecture required by [`../prompt.txt`](../prompt.txt)
to support unlimited future documentation packages. Chapters are generated module by module
against this structure so terminology, navigation, and cross-linking stay consistent site-wide.

## Numbering note

`prompt.txt` §7 specifies a 31-item deliverable list (00 Vision → 30 Development Milestones). The
existing files 00–11 below predate that list (they're Phase 0's Linux-specific IA, not literally
"00 Vision / 01 PRD / 02 SAS / 03 ADR / 04 Tech Stack Evaluation") and are kept as-is rather than
renumbered, since they're approved, in active use by Phase 2 content generation, and internally
cross-linked by number throughout every later document. Files 12–30 pick up `prompt.txt`'s own
naming from that point forward. A standalone Vision/PRD/SAS/ADR/Tech-Stack-Evaluation set (items
00–04 on `prompt.txt`'s list) and a dedicated Component Library / Design System pair (items 10–11)
remain unwritten gaps — flagged here rather than silently assumed complete.

## Files in this folder

| File | Purpose |
|---|---|
| [01-roadmap.md](01-roadmap.md) | All 34 Linux modules, every sub-topic, difficulty label, learning order |
| [02-folder-structure.md](02-folder-structure.md) | Physical repo/content folder layout |
| [03-navigation-sidebar.md](03-navigation-sidebar.md) | Sidebar grouping, landing page sections, breadcrumbs |
| [04-url-structure.md](04-url-structure.md) | URL patterns for every content type |
| [05-search-taxonomy.md](05-search-taxonomy.md) | Search index schema, facets, ranking |
| [06-career-paths.md](06-career-paths.md) | Career section structure — 12 roles, what each maps to in the roadmap |
| [07-seo-jsonld.md](07-seo-jsonld.md) | SEO metadata fields, JSON-LD schema types, internal linking rules |
| [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md) | Inventory of all cheat sheets, labs, and the interview question bank |
| [09-developer-essentials.md](09-developer-essentials.md) | Git/GitHub section — sibling to the core roadmap, not part of it |
| [10-content-template.md](10-content-template.md) | Phase 2 page templates: topic pages, command pages, career pages |
| [11-module-01-audit.md](11-module-01-audit.md) | Module 01 pilot quality audit — the record establishing the canonical content template |
| [12-theme-specification.md](12-theme-specification.md) | Design tokens, dark/light mode, package-agnostic theming |
| [13-plugin-architecture.md](13-plugin-architecture.md) | Ports-and-adapters plugin system — how every major feature stays replaceable |
| [14-authentication-architecture.md](14-authentication-architecture.md) | Provider-abstracted auth contract — architecture only, no implementation |
| [15-premium-feature-architecture.md](15-premium-feature-architecture.md) | Content-gating/entitlement architecture — architecture only, no payments |
| [16-future-database-design.md](16-future-database-design.md) | Forward-looking schema for progress, bookmarks, notes, comments, certificates |
| [17-github-actions-workflow.md](17-github-actions-workflow.md) | CI/CD pipeline: lint, build, content validation, a11y/perf gates, deploy |
| [18-deployment-strategy.md](18-deployment-strategy.md) | GitHub Pages deployment + portability to any modern host |
| [19-seo-strategy.md](19-seo-strategy.md) | Site-wide, multi-package technical SEO strategy |
| [20-accessibility-strategy.md](20-accessibility-strategy.md) | WCAG 2.2 AA strategy — layered enforcement, component/content requirements |
| [21-performance-budget.md](21-performance-budget.md) | Per-surface JS/image/font budgets enforcing the Lighthouse/CWV targets |
| [22-coding-standards.md](22-coding-standards.md) | TypeScript/Astro/Python coding standards |
| [23-testing-strategy.md](23-testing-strategy.md) | Content validation, unit/component/a11y/perf/E2E testing layers |
| [24-risk-assessment.md](24-risk-assessment.md) | Risk register across technical, content, platform, and process dimensions |
| [25-security-considerations.md](25-security-considerations.md) | Static-site security posture, supply chain, future dynamic-feature security |
| [26-scalability-strategy.md](26-scalability-strategy.md) | Scaling content volume, build time, search index, and contributor base |
| [27-contribution-guide.md](27-contribution-guide.md) | Architecture governance — ADR process, review requirements for core changes |
| [28-extension-guide.md](28-extension-guide.md) | Step-by-step: adding a new content package without touching core |
| [29-future-roadmap.md](29-future-roadmap.md) | Package and feature sequencing beyond Linux |
| [30-development-milestones.md](30-development-milestones.md) | Phased milestones with explicit exit criteria, current state → validated platform |

## Difficulty label system (used everywhere)

| Label | Meaning |
|---|---|
| 🟢 Must Know | Required for almost every Linux-related job |
| 🟡 Good to Know | Frequently used professionally, can be learned later |
| 🔴 Expert | Advanced — sysadmins, kernel engineers, specialized infra teams |

Every module, every sub-topic, and every generated page carries one of these labels, shown
prominently in the roadmap and in the page header.

## Design constraints carried into every later phase

- No history/philosophy framing. Every page opens with What is this / When do I use it / Why
  professionals use it / Example / Real-world scenario / Common mistakes.
- Concepts are taught before commands (e.g., "Processes" the concept, then `ps`, `top`, `htop`, `kill`).
- Framework-agnostic content: plain Markdown + frontmatter, so it can be wired into any static
  site generator (Astro, Docusaurus, Next.js + MDX, etc.) without rewriting content.
- Dark/light mode, minimal W3Schools-style UI — a build/design concern for Phase 2+, not content.

## Approved decisions (2026-07-25)

1. 34-module roadmap order — approved as drafted, no reordering.
2. URL scheme `/docs/{module}/{topic}` (nested) — approved as drafted.
3. 12 career role pages — approved as drafted, plus a separate 10-tag `careerRelevance`
   taxonomy for per-page badges (see [06-career-paths.md](06-career-paths.md)).
4. Cheat sheet list and 4 lab tiers — approved, with Git Cheat Sheet moved into Developer
   Essentials and cross-listed on the cheat sheet hub (see
   [09-developer-essentials.md](09-developer-essentials.md)).
5. Git does **not** become Module 35 or fold into Shell Scripting — it ships as a standalone
   **Developer Essentials** section, sibling to the core roadmap. See
   [09-developer-essentials.md](09-developer-essentials.md).
6. Company-wise interview questions are approved, categorized by **company type** (Startup,
   Product, Service, FAANG-style, Cloud, DevOps Roles, Linux Administrator Roles, Cybersecurity
   Roles), not by named company, and every page in that group carries a mandatory disclaimer.
   See [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md).
7. Salary data is never fabricated — rendered from sourced, dated, independently-updatable data
   records per role. See the "Salary section design" in
   [06-career-paths.md](06-career-paths.md).

## Phase 2 content standards (locked in from the approval)

- **Voice:** answer only what engineers actually ask while working — no history, no long
  intros, no filler, no motivational framing.
- **Every topic page follows this exact 15-step order:** What is it? → Why does it exist? →
  Where is it used? → How it works → Real-world example → Syntax → Commands → Production
  example → Common mistakes → Best practices → Exercises → Quiz → Interview questions →
  Summary → Related topics.
- **Every concept additionally answers:** why it exists, when professionals use it, what
  problem it solves, what happens if misused, the production best practice, and common mistakes.
- **Every command page includes:** Purpose, Syntax, Arguments, Options, Examples, Example
  Output, Exit Codes (when applicable), Common Errors, Security Notes, Performance Notes,
  Production Tips, Related Commands, Related Concepts.
- **Every page carries two label rows:** a difficulty dot (🟢/🟡/🔴) and a `careerRelevance`
  badge row (see [06-career-paths.md](06-career-paths.md)).
- **Every page behaves as a knowledge-graph node:** auto-linking Prerequisites, Related
  Concepts, Related Commands, Career Relevance, Practice Labs, Interview Questions, Cheat
  Sheets, Next Topic, Previous Topic — per [07-seo-jsonld.md](07-seo-jsonld.md).
- **Diagrams:** every major concept gets a recommended visual (diagram/flowchart/architecture
  diagram/terminal screenshot) called out explicitly in the page, even before an illustrator
  produces the actual asset.
- Full field-by-field templates: [10-content-template.md](10-content-template.md).
