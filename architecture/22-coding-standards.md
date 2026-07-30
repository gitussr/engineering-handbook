# Coding Standards

Scope: standards for the two languages `prompt.txt` §2 permits (TypeScript for the platform,
Python for build tools only) and for Astro component conventions. This document governs code that
does not exist yet — it is binding on whoever writes the first line of application code, which,
per `prompt.txt` §8, is not this deliverable.

## TypeScript

- **Strict mode, no exceptions.** `strict: true` in `tsconfig.json` from the first commit — a
  documentation platform meant to last ten years cannot afford to retrofit strictness onto a
  large, already-loose codebase later.
- **No `any`.** Ports and adapters ([13-plugin-architecture.md](13-plugin-architecture.md)) are
  the place type safety matters most, since they're the seams the whole "replaceable feature"
  promise depends on — an untyped adapter can silently violate its port's contract.
- **Explicit return types on every exported function.** Inferred return types are fine for local,
  unexported helpers; anything crossing a module boundary states its type explicitly so a
  breaking change to a return shape is a visible diff, not a silent inference change.
- **No business logic in Astro components** (`prompt.txt` §4). A component receives already-shaped
  data via props or a port call and renders it — data fetching, entitlement checks, and search
  querying live in the application layer (per the Clean Architecture separation in
  [00-README.md](00-README.md) and `prompt.txt` §4), never inline in `.astro` component bodies.
- **Composition over inheritance.** No class hierarchies for content types or adapters — a shared
  shape is a TypeScript `interface` (structural typing), and shared behavior is a plain function
  or a composed object, not a base class extended by every adapter.

## Astro component conventions

- One component, one responsibility — a component that renders a code block never also decides
  whether the surrounding page is gated ([15-premium-feature-architecture.md](15-premium-feature-architecture.md));
  that decision is made by the page/layout, passed down as a prop.
- Hydration directives (`client:load`, `client:idle`, `client:visible`) are chosen deliberately per
  the budget table in [21-performance-budget.md](21-performance-budget.md) — never `client:load`
  by default "to be safe," since that silently defeats the islands-architecture budget strategy.
- Props are typed via an exported `Props` interface at the top of every component file — no
  untyped `Astro.props` destructuring.

## CSS / Tailwind

- **Utility-first, tokens only.** Arbitrary values (`w-[437px]`, `text-[#3a3a3a]`) are disallowed
  in review — if a value isn't a token, it's either missing from
  [12-theme-specification.md](12-theme-specification.md) (fix the token file) or genuinely
  one-off (rare; justify in the PR description).
- No global CSS resets or overrides outside the theme file — component-scoped styles only, so
  removing a component never has a hidden global side effect.

## Naming and file conventions

- Files: `kebab-case.ts` / `kebab-case.astro`. Types/interfaces: `PascalCase`. Functions/variables:
  `camelCase`. Constants: `SCREAMING_SNAKE_CASE` only for true compile-time constants, not for
  config values that could plausibly become runtime-configurable later.
- Import order: external packages → internal ports/interfaces → internal adapters/components →
  relative imports — enforced by lint rule, not convention alone, since import-order drift is a
  common source of circular-dependency risk between adapters and the core they implement.
- One export per adapter file, matching the port it implements (`pagefind-search-adapter.ts`
  exports `PagefindSearchAdapter implements SearchPort`) — makes the plugin registry
  ([13-plugin-architecture.md](13-plugin-architecture.md)) trivially traceable from config to
  implementation.

## Python (build tools only, per `prompt.txt` §2)

- Used exclusively for build-time scripts (content validation, data-file generation from
  frontmatter, salary-data ingestion per [06-career-paths.md](06-career-paths.md)) — never for
  anything that runs in the browser or serves a request.
- Type hints on every function signature; `ruff` for linting/formatting (single tool, replaces a
  separate linter + formatter + import-sorter stack) — chosen for the same "reduce moving parts"
  principle as the rest of the mandated stack.
- No Python dependency is added to satisfy something TypeScript could already do — Python's role
  is scoped narrowly enough that dependency creep here would blur the "why two languages" rationale.

## Linting and formatting enforcement

- ESLint + Prettier (TypeScript/Astro) and `ruff` (Python) run in CI stage 1
  ([17-github-actions-workflow.md](17-github-actions-workflow.md)) — a PR with lint failures
  cannot proceed to build.
- Formatting is never bikeshed in review — Prettier/`ruff format` are the single source of truth
  for style, so review time goes to logic and architecture fit, not spacing.

## Commit conventions

- Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`) — enables an automated
  changelog per content package and per platform release, relevant once
  [30-development-milestones.md](30-development-milestones.md)'s later phases need a release
  history distinguishable from routine content edits.

## Non-goals

- No specific IDE/editor mandated — standards are enforced by CI tooling, not by requiring a
  particular editor setup.
- No monorepo tooling choice (Nx, Turborepo, etc.) specified — deferred until the platform
  actually has multiple independently-buildable packages large enough to need one
  ([26-scalability-strategy.md](26-scalability-strategy.md)).
