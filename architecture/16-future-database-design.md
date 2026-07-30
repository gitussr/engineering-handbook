# Future Database Design

Scope: forward-looking data model for every stateful feature listed under Future Features in
`prompt.txt` §6 (progress tracking, bookmarks, notes, reading history, comments, certificates,
etc.). **No database exists today** (`prompt.txt` §2: "Backend: None initially"). This document
lets those features be designed for, per the acceptance criteria, without forcing a schema
migration scramble whenever the first one is actually built.

## Design principle: user data references content, content never references user data

Content (Markdown + frontmatter) remains the single source of truth
([00-README.md](00-README.md)) and stays completely ignorant of users, progress, or entitlements.
Every table below stores a `contentId` (the canonical `canonicalUrl` from
[07-seo-jsonld.md](07-seo-jsonld.md) frontmatter) as a foreign key pointing *at* content — content
never stores a pointer back. This keeps the content model portable
(`prompt.txt` §4, Framework Agnostic) regardless of what database or even what database *engine*
ends up behind these tables.

## Entity-relationship overview

```
users ──< entitlements
  │
  ├──< progress            (contentId, status, completedAt)
  ├──< bookmarks           (contentId, createdAt)
  ├──< notes               (contentId, body, createdAt, updatedAt)
  ├──< reading_history      (contentId, visitedAt, durationSeconds)
  ├──< quiz_attempts        (contentId, answers, score, attemptedAt)
  ├──< flashcard_progress   (deckId, cardId, srsState, dueAt)
  ├──< certificates         (packageId, issuedAt, verificationCode)
  └──< comments             (contentId, body, createdAt, parentCommentId)
```

## Table sketches (conceptual — no migration, no ORM, no engine chosen)

```typescript
// Illustrative shapes, not a schema file to be run against any database.

interface User {
  id: string;
  email: string;
  displayName: string;
  createdAt: string;
}

interface Entitlement {
  userId: string;
  plan: string;
  contentPackages: string[];
  expiresAt: string | null;
} // — mirrors 15-premium-feature-architecture.md's EntitlementPort shape exactly

interface ProgressRecord {
  userId: string;
  contentId: string;         // canonicalUrl, e.g. "/docs/permissions/chmod-symbolic-and-octal"
  status: "not-started" | "in-progress" | "completed";
  completedAt: string | null;
}

interface Bookmark {
  userId: string;
  contentId: string;
  createdAt: string;
}

interface Note {
  id: string;
  userId: string;
  contentId: string;
  body: string;              // Markdown, user-authored, never rendered as HTML unsanitized
  createdAt: string;
  updatedAt: string;
}

interface QuizAttempt {
  userId: string;
  contentId: string;
  answers: Record<string, string>;
  score: number;
  attemptedAt: string;
}

interface Certificate {
  userId: string;
  packageId: string;          // e.g. "linux" — one certificate per completed content package
  issuedAt: string;
  verificationCode: string;    // publicly verifiable, no auth required to check validity
}

interface Comment {
  id: string;
  userId: string;
  contentId: string;
  body: string;
  parentCommentId: string | null;  // null = top-level
  createdAt: string;
  moderationStatus: "visible" | "pending" | "removed";
}
```

## Why `contentId` (a string URL) and not a numeric content foreign key

Content has no database row today and may never get one — it lives in Markdown, versioned in Git.
Using the stable `canonicalUrl` as the join key means the future database only ever needs to
*reference* content, never *import, sync, or mirror* it. This avoids a second source of truth for
content metadata and keeps the "Documentation must remain the single source of truth" principle
(`prompt.txt` §1) true even after a database exists.

## Multi-package implication

Every per-user table above is package-agnostic by construction — `contentId` values naturally
namespace by package (`/docs/permissions/...` vs. a future `/docs/python-basics/...`), so adding a
package never requires a schema change, only more rows. `certificates.packageId` is the one
explicitly package-scoped field, because "completed the Linux Handbook" is a package-level
achievement, not a per-page one.

## Candidate database engines (none chosen)

| Class | Fit |
|---|---|
| Managed relational (Postgres-compatible) | Strong fit for the relational shape above; broadest ecosystem support |
| Serverless/edge-native relational or KV | Better fit if the hosting migration in [18-deployment-strategy.md](18-deployment-strategy.md) lands on an edge-function platform |

Selection is deferred until a feature in `prompt.txt` §6 is actually greenlit for implementation —
this document exists so that greenlighting one doesn't start with schema design from zero.

## Non-goals

- No indexing strategy, no scaling numbers, no backup/retention policy — those are implementation
  decisions made against a chosen engine, not an architecture-level concern yet.
- No migration tooling choice.
- Comments moderation workflow (who reviews `moderationStatus: "pending"`) is a process design,
  deferred to whenever Community Contributions / Comments is prioritized
  ([29-future-roadmap.md](29-future-roadmap.md)).
