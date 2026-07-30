# Premium Feature Architecture

Scope: **architecture only, per `prompt.txt` §2 — no payment system is implemented.** This file
specifies how premium content gating will work so that, per the acceptance criteria in
`prompt.txt` §9, **"premium features can be added without refactoring the content model."**

## The constraint this satisfies

Every content page today ([10-content-template.md](10-content-template.md)) already ships a full,
locked frontmatter schema. If premium gating were bolted on later by adding a new content type, a
separate "premium docs" folder, or a different page template for gated content, that would be
exactly the refactor the acceptance criteria forbids. So gating is designed in now as a single
optional frontmatter field, even though nothing enforces it yet.

## The gating field (added to the existing frontmatter schema, default off)

```yaml
# Addition to the schema in 10-content-template.md — one field, optional, defaults to "free"
tier: "free"          # "free" | "premium"
```

That is the entire content-model change. No new file location, no new page template, no new
content type. A page's `tier` field is inert today (the `EntitlementPort` adapter is `"none"`, per
[13-plugin-architecture.md](13-plugin-architecture.md)) — it does not gate anything until an
entitlement adapter is registered. Authoring content with `tier: premium` today is safe and
forward-compatible: it simply renders like any other page until gating activates.

## EntitlementPort contract

```typescript
// Conceptual — the interface, not an implementation.
interface EntitlementPort {
  hasAccess(userId: string | null, contentId: string): Promise<boolean>;
  getEntitlements(userId: string): Promise<Entitlement[]>;
}

interface Entitlement {
  userId: string;
  plan: string;              // "free" | "individual" | "team" | ...
  contentPackages: string[]; // e.g. ["linux", "docker"] — per-package entitlement, not all-or-nothing
  expiresAt: string | null;  // null = perpetual / not subscription-based
}
```

Per-package entitlements (`contentPackages`) rather than a single global "premium" flag — this
supports selling access to one handbook package at a time as the platform grows, without a data
model change when package #2 ships.

## Rendering behavior when gating is active (design, not built)

- The build still statically renders every page in full — premium content is **not** omitted
  from the static output, because SEO indexing and search ([19-seo-strategy.md](19-seo-strategy.md))
  need the content discoverable. Instead, a client-side check via `EntitlementPort.hasAccess()`
  determines whether the full body renders or a paywall component replaces it below a visible
  preview (first N paragraphs, typically the TL;DR + "What is it?" + "Why does it exist?"
  sections from the template).
- This means: **search still finds premium pages, previews are crawlable, and the paywall is a
  client-side rendering decision, not a content-serving decision** — consistent with a fully
  static host having no way to withhold HTML per-request.
- The paywall component is itself a plugin surface — it reads `EntitlementPort` and
  `AuthPort.getSession()` and renders one of: full content / preview + upsell / sign-in prompt.
  It never contains payment-provider-specific logic.

## Candidate payment/entitlement providers (none selected, none integrated)

| Provider family | Fit |
|---|---|
| Hosted checkout + webhook-driven entitlement sync (e.g., Stripe-style) | Standard SaaS pattern; needs a server function to receive webhooks — same hosting dependency as auth |
| Marketplace-style reseller (e.g., Lemon Squeezy-style) | Lower integration lift, handles tax/compliance, same server-function dependency |

Selection is deferred to the same point [18-deployment-strategy.md](18-deployment-strategy.md)'s
migration path is exercised — entitlement sync, like auth, needs a runtime endpoint a pure static
host doesn't have.

## What ships today vs. what's designed for later

| Ships now | Designed, not built |
|---|---|
| `tier` frontmatter field (inert) | `EntitlementPort` adapter |
| This architecture document | Paywall component |
| — | Checkout/billing integration |
| — | Per-package pricing/plan definitions |

## Non-goals

- No pricing decisions, no plan names, no currency handling — commercial/business decisions are
  out of scope for an architecture document.
- No DRM or content-obfuscation scheme for premium Markdown source — the threat model here is
  "casual paywall bypass via view-source" is an accepted risk common to this class of product,
  not one this architecture attempts to solve with client-side obfuscation (which would conflict
  with the static-rendering/SEO requirement above anyway).
