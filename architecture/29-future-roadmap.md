# Future Roadmap

Scope: sequencing for the two growth dimensions `prompt.txt` names explicitly — future content
packages (§1) and future platform features (§6) — paced against actual capacity
([24-risk-assessment.md](24-risk-assessment.md) R7), not aspirational dates. No calendar dates are
committed here; sequencing is relative ("after X proves out Y"), and concrete phase gates live in
[30-development-milestones.md](30-development-milestones.md).

## Content package sequencing

| Order | Package | Rationale |
|---|---|---|
| 1 (current) | Linux | Already in Phase 2 content generation ([00-README.md](00-README.md)) |
| 2 | SQL or Python | First package after Linux exists specifically to exercise
  [28-extension-guide.md](28-extension-guide.md)'s "zero core changes" claim
  ([24-risk-assessment.md](24-risk-assessment.md) R12) — chosen for broad career relevance and
  content-shape difference from Linux (less command-reference-heavy), which stress-tests the
  package abstraction harder than a second CLI-heavy package would |
| 3+ | JavaScript, TypeScript, React, Next.js, PHP, Laravel, Docker, Kubernetes, DevOps, Cloud Computing, Cybersecurity, Networking, Operating Systems, Database Design, System Design, AI Engineering, MLOps | Ordered opportunistically by career-path demand and content-author availability once packages 1–2 validate the extension mechanism; no fixed order committed |

Package 2 is deliberately the load-bearing milestone, not package 10 — if the abstraction leaks,
it should be discovered on the *second* package, when the fix is still cheap, not the fifth.

## Feature sequencing (from `prompt.txt` §6's Future Features list)

Features are grouped by what unlocks them, since several depend on the same prerequisite:

| Group | Features | Unlocked by |
|---|---|---|
| Available today, no dependency | Progress Tracking, Bookmarks, Personal Notes, Reading History, Flashcards | A hosting migration that adds a database ([16-future-database-design.md](16-future-database-design.md), [18-deployment-strategy.md](18-deployment-strategy.md)) — but no auth requirement if scoped to anonymous/local-storage-only versions first |
| Requires Authentication | Full (cross-device) Progress Tracking/Bookmarks/Notes, Comments, Community Contributions, Certificates | [14-authentication-architecture.md](14-authentication-architecture.md) adapter selection + hosting migration |
| Requires Authentication + Payments | Subscriptions, Premium Content | [14](14-authentication-architecture.md) + [15-premium-feature-architecture.md](15-premium-feature-architecture.md) adapter selection |
| Requires a content-package-aware backend | Course Builder, Collections, Learning Paths | Builds on the package registry ([28-extension-guide.md](28-extension-guide.md)) once ≥2–3 packages exist — a "Learning Path" spanning packages isn't meaningful with only one |
| Requires significant new subsystem | AI Tutor, Semantic Search, Code Playground | Each is its own R&D effort — semantic search in particular would sit behind `SearchPort` ([13-plugin-architecture.md](13-plugin-architecture.md)) as a new adapter, not a UI change |
| API surface | REST API, GraphQL API, Webhook Support, RSS | RSS is achievable today (build-time generated from content, no backend needed) and should ship early; REST/GraphQL/Webhooks presume the same backend as the Authentication group |
| Cross-cutting, any time | Localization | Independent of auth/payments — gated on content-authoring capacity and the font/i18n groundwork noted in [21-performance-budget.md](21-performance-budget.md)'s font-subsetting section, not on any other feature |

## What ships before any of the above

Nothing in the feature table activates before: (1) Linux content is substantially complete, (2)
package #2 has validated the extension mechanism, and (3) the CI/testing/performance/accessibility
gates in [17](17-github-actions-workflow.md)/[20](20-accessibility-strategy.md)/[21](21-performance-budget.md)/[23](23-testing-strategy.md)
are actually running, not just specified. Building Progress Tracking against an untested pipeline
would mean debugging two unproven things at once.

## Review cadence

This roadmap is revisited whenever a package or feature milestone lands (see
[30-development-milestones.md](30-development-milestones.md)) — sequencing here reflects current
judgment about dependency order, not a locked schedule; reordering package 3+ or reprioritizing a
feature group doesn't require an ADR (per [27-contribution-guide.md](27-contribution-guide.md)),
since nothing here is a binding architectural commitment, only a plan.
