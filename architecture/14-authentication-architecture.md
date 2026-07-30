# Authentication Architecture

Scope: **architecture only, per `prompt.txt` §2 — no authentication is implemented.** This file
specifies the `AuthPort` contract from [13-plugin-architecture.md](13-plugin-architecture.md) in
enough depth that a future implementer can build an adapter without redesigning the shape of
auth, but it ships zero login code, zero session code, zero provider SDKs.

## Why architecture now, implementation later

The platform is 100% static (GitHub Pages, no backend, per `prompt.txt` §2). Real authentication
needs a place to verify a session server-side — a static host cannot do that. Specifying the
contract now, without implementing it, means: (1) the content model, premium-feature gating
([15-premium-feature-architecture.md](15-premium-feature-architecture.md)), and future database
design ([16-future-database-design.md](16-future-database-design.md)) can all be built against a
stable `AuthPort` shape today, and (2) the day the platform migrates to a host with server
functions ([18-deployment-strategy.md](18-deployment-strategy.md)), auth activates by adding one
adapter — no retrofitting of content, premium gating, or page structure.

## AuthPort contract

```typescript
// Conceptual — the interface, not an implementation.
interface Session {
  userId: string;
  email: string;
  displayName: string;
  provider: string;          // "github" | "google" | "email-magic-link" | ...
  issuedAt: string;          // ISO 8601
  expiresAt: string;         // ISO 8601
}

interface AuthPort {
  getSession(): Promise<Session | null>;
  signIn(provider: AuthProviderId): Promise<void>;
  signOut(): Promise<void>;
  onSessionChange(callback: (session: Session | null) => void): Unsubscribe;
}
```

Every page and component that needs to know "is someone logged in" calls `getSession()` through
the port — never a provider SDK directly. This is the same ports-and-adapters discipline as every
other plugin in [13-plugin-architecture.md](13-plugin-architecture.md), applied to the one
subsystem the brief explicitly calls out as security-sensitive.

## Candidate providers (adapter targets, none built)

| Provider family | Fit | Notes |
|---|---|---|
| OAuth — GitHub | Strong fit, dev-heavy audience | Zero-friction for the platform's actual user base (engineers) |
| OAuth — Google | Broad fit | Lowest-friction for non-technical/career-path learners |
| Email magic link | Fallback | No password storage, lowest security surface for a small team to own |
| Managed identity platform (e.g., a hosted auth service) | Fastest to implement later | Trades a small recurring cost for owning zero session/token security code |

No provider is selected. Selection happens at the point auth is actually implemented, evaluated
against the hosting decision in [18-deployment-strategy.md](18-deployment-strategy.md) (a managed
identity platform typically requires the same server-function capability that unlocks real auth
in the first place).

## Session & token handling (architecture-level rules, binding on any future adapter)

- **No adapter stores raw passwords.** Password-based auth is out of scope permanently — every
  candidate provider above is either OAuth (delegated) or passwordless.
- **Session tokens are opaque to the frontend.** Components read `Session` (the shape above) from
  the port; they never parse, decode, or inspect a raw JWT/cookie — that logic lives entirely
  inside the adapter, so switching providers can't leak provider-specific token shapes into UI
  code.
- **No session data is ever committed to the static build.** Auth is inherently a runtime concern;
  it cannot leak into the pre-rendered HTML the way content does — this is a structural
  consequence of the platform being static-first, not something that needs separate enforcement.

## Where auth intersects the rest of the architecture

| Subsystem | Auth touchpoint |
|---|---|
| Premium features ([15](15-premium-feature-architecture.md)) | `EntitlementPort.hasAccess()` takes the `userId` from `Session` |
| Future DB ([16](16-future-database-design.md)) | `users` table keyed on the same `userId` shape as `Session` |
| Progress tracking / bookmarks / notes (`prompt.txt` §6) | All future per-user data keys off `Session.userId` — no feature invents its own identity concept |
| Navigation | A signed-in state adds account/profile nav items — additive, not a structural nav change (see [03-navigation-sidebar.md](03-navigation-sidebar.md)) |

## Explicit non-goals of this document

- No implementation, no SDK choice, no environment variables, no secrets.
- No admin/authorization roles beyond the reader/premium distinction covered in
  [15-premium-feature-architecture.md](15-premium-feature-architecture.md) — a content-moderation
  or contributor-permissions model is out of scope until Community Contributions
  (`prompt.txt` §6) is prioritized in [29-future-roadmap.md](29-future-roadmap.md).
