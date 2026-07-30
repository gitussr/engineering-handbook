# Deployment Strategy

Scope: initial GitHub Pages deployment and the migration path required by the acceptance
criterion in `prompt.txt` §9 — "the platform can migrate from GitHub Pages to any modern hosting
provider without content changes."

## Initial deployment: GitHub Pages

- Static output from `astro build` is published via the GitHub Pages Actions deployment flow
  (stage 7 of [17-github-actions-workflow.md](17-github-actions-workflow.md)), not the legacy
  "publish a branch" approach — this keeps deploy as a pipeline artifact, not a manually-pushed
  branch that can drift from `main`.
- Custom domain support (a CNAME record + repo setting) is assumed from day one so the eventual
  host migration doesn't also force a domain/URL change — a domain change would break every
  external link and search ranking earned pre-migration.
- GitHub Pages serves over HTTPS by default and requires no additional TLS configuration at this
  stage.

## Why portability is a first-class requirement, not an afterthought

GitHub Pages cannot run server functions. The moment auth
([14-authentication-architecture.md](14-authentication-architecture.md)), premium entitlement
sync ([15-premium-feature-architecture.md](15-premium-feature-architecture.md)), or any
`onRequest` plugin hook ([13-plugin-architecture.md](13-plugin-architecture.md)) needs to
activate, GitHub Pages stops being sufficient — not because the architecture was wrong, but
because those features were always designed to need a runtime the initial host doesn't have. The
migration path below exists so that day is a hosting change, not a rebuild.

## What makes the migration safe (the actual portability mechanism)

1. **Content never references the host.** Every internal link is root-relative
   ([04-url-structure.md](04-url-structure.md)); nothing in `content/` hardcodes
   `https://<user>.github.io/<repo>/`.
2. **The build output is the product, not the framework's dev server.** Astro's static output
   (plus, later, its adapter-based server output for a host that supports functions) is a
   directory of HTML/CSS/JS — any static host or CDN can serve it unmodified.
3. **Plugin adapters are the only host-coupled code** (per
   [13-plugin-architecture.md](13-plugin-architecture.md)) — swapping host is, at most, swapping
   the adapters that need a runtime (auth, entitlements) plus an Astro adapter package for the new
   target. Content, theme, navigation, and search are untouched.

## Candidate migration targets (none selected — evaluated only when migration is triggered)

| Target class | What it unlocks |
|---|---|
| Edge-function-capable static host | Server-rendered auth/entitlement checks become possible; still serves static content from a CDN edge, so no perf regression |
| Self-hosted / container-based | Full control, higher operational ownership cost — evaluated only if the managed options above prove insufficient |

The decision to migrate, and to which target, is made when a feature genuinely requires it —
speculatively migrating early would violate "prioritize... long-term evolution over short-term
convenience" in exactly the wrong direction (premature infrastructure complexity the static site
doesn't yet need).

## Preview deployments (PR review)

GitHub Pages has no native per-PR preview. Until migration, PR review relies on: (1) the CI gates
in [17-github-actions-workflow.md](17-github-actions-workflow.md) passing, and (2) a downloadable
build artifact reviewers can run locally (`astro preview`). A hosting target with native PR
previews is one of the concrete benefits evaluated at migration time, not a gap patched on GitHub
Pages with a workaround that adds host-coupling.

## Rollback

- Every deploy is a distinct, addressable build (tied to a commit SHA) — rollback is "redeploy the
  previous successful build artifact," never a manual file revert on the live host.
- Because content is the single source of truth in Git ([00-README.md](00-README.md)), a rollback
  never risks losing content — it only reverts *which build* is live, never the underlying
  Markdown history.

## Non-goals of this document

- No CDN vendor selection, no pricing comparison, no DNS runbook — those are executed at
  migration time against whichever target is chosen.
- No blue-green/canary deployment design — unnecessary for a fully static, read-only site; revisit
  only if a future dynamic feature introduces state that makes an instant full cutover risky.
