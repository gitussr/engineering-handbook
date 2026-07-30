# Security Considerations

Scope: security architecture for the platform as it exists today (fully static, no backend) and
as it will exist once auth/entitlements activate. Referenced from
[24-risk-assessment.md](24-risk-assessment.md) R9 and R11's security-adjacent items.

## Today's attack surface (static site, no backend)

A fully static site with no server, no database, and no user accounts has a deliberately small
attack surface. The security work today is almost entirely about the **build and deploy
pipeline**, not runtime application security, because there is no running application yet.

| Area | Posture |
|---|---|
| Server-side vulnerabilities (SQLi, SSRF, auth bypass, etc.) | Not applicable — no server exists |
| XSS via user-submitted content | Not applicable — no user-submitted content is rendered yet (comments, notes are future features, see below) |
| Dependency supply chain | Primary real risk today — see below |
| CI/CD pipeline compromise | Primary real risk today — see below |

## Supply chain security

- **Lockfile-pinned dependencies**, automated update PRs (Dependabot or equivalent) reviewed like
  any other PR — never auto-merged without CI passing
  ([17-github-actions-workflow.md](17-github-actions-workflow.md)).
- **GitHub Actions pinned to a commit SHA**, never a floating major-version tag — a compromised
  upstream Action publishing a new commit under an existing tag cannot silently change what runs
  in this pipeline.
- **Minimal `GITHUB_TOKEN` scope per job** — lint/build jobs get `contents: read` only; only the
  deploy job gets `pages: write`; no job gets more than it needs.
- No runtime dependency is added to satisfy something achievable at build time — every dependency
  added to the client bundle is scrutinized against the JS budget
  ([21-performance-budget.md](21-performance-budget.md)) *and* against supply-chain exposure,
  since client-side dependencies are the ones an end user's browser actually executes.

## Content Security Policy

- A strict CSP is set even though the site is static — `default-src 'self'`, no inline
  `<script>` except the theme no-FOUC script (which is hashed/nonce'd, not blanket-allowed via
  `unsafe-inline`), no third-party script origins beyond what a registered plugin explicitly
  requires (per [13-plugin-architecture.md](13-plugin-architecture.md)).
- No third-party CDN script tags — every mandated library (Shiki, Mermaid, Pagefind) is bundled
  through the build, not loaded from a public CDN at runtime, per `prompt.txt` §3's "No
  unnecessary runtime JavaScript" and the CSP posture above.
- Fonts are self-hosted (`prompt.txt` §3), which also means no third-party font-origin CSP
  exception is ever needed.

## Future dynamic-feature security (auth, entitlements, comments, notes)

These do not exist yet, but the architecture is designed so their security model is decided now,
not improvised when first implemented:

- **Auth** ([14-authentication-architecture.md](14-authentication-architecture.md)): no
  password storage ever (OAuth/magic-link only), opaque session tokens never parsed by frontend
  code, provider-delegated identity verification.
- **Entitlements** ([15-premium-feature-architecture.md](15-premium-feature-architecture.md)):
  gating is a client-side rendering decision on a fully static, pre-rendered page — the real
  security boundary for anything genuinely sensitive (not just "nicer if paid") would need to move
  server-side at the same point auth does; premium *documentation content* is treated as a
  soft paywall by design (see that document's non-goals), not a hard security boundary.
- **User-submitted content** (comments, notes — [16-future-database-design.md](16-future-database-design.md)):
  when implemented, is never rendered as raw HTML — Markdown-in, sanitized-HTML-out, with the
  sanitizer allowlist excluding `<script>`, event handler attributes, and `javascript:` URLs. This
  is a binding requirement on whichever adapter eventually implements `CommentsPort`.
- **Moderation status** (`comments.moderationStatus` in the future DB design) exists specifically
  so abusive/spam content has a server-side gate before public rendering, once comments ship.

## Secrets management

- No secret is ever committed to the repository — enforced by a pre-commit/CI secret-scanning
  check, not just policy.
- Once payment/auth adapters exist, their credentials live in the CI platform's encrypted secrets
  store, scoped to the deploy job only (per the pipeline design in
  [17-github-actions-workflow.md](17-github-actions-workflow.md)), never exposed to
  fork-triggered PR builds.

## Reporting

A `SECURITY.md` (not yet authored — a candidate addition alongside
[27-contribution-guide.md](27-contribution-guide.md)) will define a responsible-disclosure
contact once the platform has any user-facing dynamic surface worth disclosing issues about;
premature for a pure static content site with no user data today.

## Non-goals

- No penetration-testing engagement scoped here — appropriate once dynamic features
  (auth/payments) are live, not for a static documentation site.
- No compliance framework (SOC 2, etc.) targeted — out of scope until the platform handles
  payment or personal data at a scale that warrants it.
