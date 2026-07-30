# GitHub Actions Workflow

Scope: CI/CD pipeline design. Per `prompt.txt` §8 this is architecture, not shippable YAML — the
job list, gates, and ordering below are the spec a future workflow file implements against.

## Pipeline stages (every push to a content or code branch)

```
1. Lint            → frontmatter schema, Markdown lint, TypeScript/ESLint, Python (ruff) if used
2. Build            → astro build (all registered content packages, per 28-extension-guide.md)
3. Content validate → internal link integrity, prerequisite-ordering check, orphan-page check
4. A11y gate        → axe-core against a sample of rendered routes — fails under 100 (prompt.txt §3)
5. Performance gate → Lighthouse CI against a sample of rendered routes — fails under the
                       thresholds in 21-performance-budget.md
6. Search index     → Pagefind indexing (build-time plugin hook, per 13-plugin-architecture.md)
7. Deploy            → GitHub Pages (main branch only) — see 18-deployment-strategy.md
```

Stages 1–5 run on every PR; stage 7 runs only on merge to the default branch. A PR that fails any
of stages 1–5 cannot merge — these are hard gates, not advisory checks, because they are the only
mechanical enforcement of the Lighthouse/a11y/JS-budget numbers in `prompt.txt` §3. Without CI
enforcement those numbers are aspirational text, not a guarantee.

## Why content validation is its own stage, separate from build

`astro build` succeeding only proves the HTML compiles — it does not prove the knowledge graph
promised in [00-README.md](00-README.md) is intact (no page links to a prerequisite that doesn't
exist, no topic is orphaned with no `prevTopic`/`nextTopic`, no `relatedCommands` entry points at
a command page that was never created). This stage exists because those are exactly the defects
the Module 01 audit ([11-module-01-audit.md](11-module-01-audit.md)) found by hand — CI is what
keeps that from being a manual audit forever as the page count grows past 218.

## Illustrative workflow shape (conceptual YAML, not a file to run)

```yaml
name: ci
on:
  pull_request:
  push:
    branches: [main]

jobs:
  lint:
    steps:
      - run: npm run lint:frontmatter
      - run: npm run lint:markdown
      - run: npm run lint:ts

  build:
    needs: lint
    steps:
      - run: npm run build

  content-validate:
    needs: build
    steps:
      - run: npm run validate:links
      - run: npm run validate:prerequisites
      - run: npm run validate:orphans

  a11y-gate:
    needs: build
    steps:
      - run: npm run test:a11y   # axe-core, fails the job below threshold

  perf-gate:
    needs: build
    steps:
      - run: npm run test:lighthouse   # fails below 21-performance-budget.md thresholds

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: [content-validate, a11y-gate, perf-gate]
    steps:
      - run: npm run deploy:gh-pages
```

## Caching and build time (scalability concern, ties to 26-scalability-strategy.md)

- Dependency install cache (lockfile-keyed) and Astro's content-collection cache are both
  restorable across runs — as page count grows toward and past the ~218-page current estimate
  ([01-roadmap.md](01-roadmap.md)) times N future packages, uncached full rebuilds become the
  pipeline's dominant cost.
- Pagefind indexing (stage 6) only re-runs when content changed, not on every dependency bump —
  indexing 1 page's worth of edits should not force reindexing every package.

## Security posture for the workflow itself

- Third-party Actions are pinned to a commit SHA, never a floating tag (`@v4` can change
  underneath the pipeline; a pinned SHA cannot) — see [25-security-considerations.md](25-security-considerations.md).
- Secrets (once auth/payment adapters exist, per [14](14-authentication-architecture.md) and
  [15](15-premium-feature-architecture.md)) are scoped to the deploy job only, never exposed to
  PR-triggered jobs from forks.
- `GITHUB_TOKEN` permissions are minimally scoped per job (`contents: read` for lint/build,
  `pages: write` only for the deploy job).

## Branch protection (policy, enforced in repo settings, not in the workflow file)

- `main` requires stages 1–5 passing before merge.
- `main` requires at least one review approval — a documentation platform intended to last ten
  years should never have a single-reviewer bottleneck be the *only* gate; CI is the mechanical
  gate, review is the judgment gate, per [27-contribution-guide.md](27-contribution-guide.md).
