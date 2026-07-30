# Plugin Architecture

Scope: how "every major feature must be replaceable" (`prompt.txt` §4) is actually enforced, not
just stated. This file defines the plugin contract; it does not implement any plugin — per
`prompt.txt` §8, no application code ships from this deliverable.

## The problem this solves

Search is Pagefind today. Auth doesn't exist yet but will need a provider. Payments don't exist
yet but will need a provider. Comments, analytics, an AI tutor, a code playground — every one of
these is either already named as a mandatory technology or listed as a future feature
(`prompt.txt` §6). If any of them is wired directly into pages/components, replacing it later
means touching every page that used it. The plugin architecture exists so that replacing Pagefind
with a different search provider, or adding a payment provider, is a **single adapter swap**, not
a site-wide refactor.

## Core pattern: ports and adapters (hexagonal architecture)

Every replaceable subsystem is defined as a **port** (a TypeScript interface owned by the
application core) with one or more **adapters** (concrete implementations behind that interface).
Pages and components depend only on the port, never on a specific adapter — this is the
Dependency Inversion principle from `prompt.txt` §4 made concrete.

```typescript
// Conceptual interface shape — illustrates the contract, not a shippable implementation.

interface SearchPort {
  query(input: string, facets?: SearchFacets): Promise<SearchResult[]>;
  index(): Promise<void>; // build-time indexing hook
}

interface AuthPort {
  getSession(): Promise<Session | null>;
  signIn(provider: string): Promise<void>;
  signOut(): Promise<void>;
}

interface EntitlementPort {
  hasAccess(userId: string, contentId: string): Promise<boolean>;
}

interface AnalyticsPort {
  track(event: AnalyticsEvent): void;
}

interface CommentsPort {
  list(contentId: string): Promise<Comment[]>;
  submit(contentId: string, comment: NewComment): Promise<Comment>;
}
```

Each port lives in a stable, versioned location (`src/core/ports/*.ts`, conceptually) that
downstream adapters and page code both import from — the port is the contract both sides agree
never to break without a version bump.

## Plugin registry (the swap mechanism)

A single build-time config selects which adapter satisfies each port. No page or component ever
imports an adapter directly.

```typescript
// astro.config / plugin-registry.ts (conceptual)
export const plugins = {
  search: "pagefind",       // swappable: "algolia" | "typesense" | "pagefind"
  auth: "none",              // swappable: "none" | "github-oauth" | "google-oauth" | "clerk"
  entitlements: "none",      // swappable: "none" | "stripe-gated" | "lemon-squeezy-gated"
  analytics: "none",         // swappable: "none" | "plausible" | "umami"
  comments: "none",          // swappable: "none" | "giscus" | "custom-api"
};
```

Swapping `search: "pagefind"` → `search: "typesense"` requires: (1) a new adapter satisfying
`SearchPort`, (2) one config line change. Zero page-level or component-level changes, because
every search UI element (search bar, results dropdown, `/search` page) calls `SearchPort`, never
Pagefind's own API surface.

## What is and isn't a plugin

| Replaceable (plugin) | Not a plugin (core) |
|---|---|
| Search provider | Content collections / frontmatter schema |
| Auth provider | Routing strategy (see [04-url-structure.md](04-url-structure.md)) |
| Payment/entitlement provider | Theme tokens ([12-theme-specification.md](12-theme-specification.md)) |
| Comments provider | Navigation structure |
| Analytics provider | The 15-field documentation frontmatter (`prompt.txt` §5) |
| AI tutor backend (future) | Difficulty/career-relevance taxonomy |

The rule: **anything that could plausibly be swapped for a competing product without changing
what the reader experiences conceptually is a plugin. Anything that defines what the platform
fundamentally is stays in core.**

## Plugin lifecycle hooks

Plugins that need to run at build time (search indexing) vs. runtime (auth session check) are
distinguished explicitly — a plugin declares which hooks it implements, and the build/runtime
orchestrator only calls the hooks that exist:

| Hook | When | Example use |
|---|---|---|
| `onBuild` | Static build time | Search indexing, sitemap generation |
| `onRequest` | Per-request (if ever deployed to a non-static host) | Auth session resolution |
| `onClientInit` | Client-side hydration | Analytics init, comments widget mount |

On GitHub Pages (pure static, per `prompt.txt` §2), only `onBuild` and `onClientInit` are
reachable — `onRequest`-dependent plugins (most auth flows) require the migration path described
in [18-deployment-strategy.md](18-deployment-strategy.md) before they can activate. This is
exactly why `prompt.txt` §2 scopes Authentication/Payments as "architecture only" for now: the
port and adapter contract can be fully specified today; the adapter that needs a live server
cannot be exercised until the platform deploys somewhere that has one.

## Package-level plugins vs. platform-level plugins

Distinct from the ports above, each content package ([28-extension-guide.md](28-extension-guide.md))
may register **package-scoped plugins** — e.g., a future "Code Playground" plugin might only
activate for the Python or JavaScript package, not Linux. Package-scoped plugins are declared in
that package's own registry entry and are additive; they never require a change to the platform
core or to other packages' behavior.

## Testing implication

Because every subsystem is an interface, every adapter is unit-testable in isolation against the
port's contract (a fake `SearchPort` implementation can replace Pagefind in tests without spinning
up the real indexer) — see [23-testing-strategy.md](23-testing-strategy.md).
