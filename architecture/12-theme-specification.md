# Theme Specification

Scope: visual design tokens and dark/light mode architecture. This is a **token contract**, not a
component library — a dedicated Component Library / Design System deliverable is not yet written
(see the numbering note in [00-README.md](00-README.md)); this file specifies what any future
component/design-system doc must consume, so it isn't blocked on those existing first.

## Why tokens, not hardcoded values

Every constraint in `prompt.txt` (dark mode, WCAG 2.2 AA, ≤150KB JS, unlimited future packages)
converges on one rule: **no color, spacing, or type value is ever hardcoded in a component.**
Components reference tokens; tokens are the only place a value is defined. This is what makes
"add a new content package" a token-file change, not a UI rewrite, and what makes a future
rebrand or accessibility fix a one-file diff instead of a grep-and-replace across every page.

## Token architecture (Tailwind v4, CSS-first)

Tailwind v4 defines design tokens as native CSS custom properties via `@theme`, not a JS config
object — tokens are usable directly as CSS variables by any future renderer (Astro component,
MDX interactive island, or a completely different framework if the platform ever migrates off
Astro, per the Framework Agnostic principle in [prompt.txt](../prompt.txt)).

```css
/* conceptual — actual file lives in the future Astro app, not authored yet */
@theme {
  /* Core palette — brand-neutral, package accent layered on top (see below) */
  --color-surface: oklch(99% 0 0);
  --color-surface-dark: oklch(18% 0.01 260);
  --color-ink: oklch(20% 0.02 260);
  --color-ink-dark: oklch(96% 0.01 260);
  --color-border: oklch(90% 0.005 260);
  --color-border-dark: oklch(30% 0.01 260);

  /* Semantic, not literal — components ask for "danger", never "red-500" */
  --color-success: oklch(65% 0.15 145);
  --color-warning: oklch(75% 0.15 85);
  --color-danger: oklch(60% 0.2 25);
  --color-info: oklch(65% 0.15 250);

  /* Difficulty labels — see 01-roadmap.md legend, tokenized so the emoji-adjacent
     dot color is themeable and passes contrast independent of dark/light mode */
  --color-difficulty-must-know: oklch(60% 0.15 145);
  --color-difficulty-good-to-know: oklch(75% 0.15 85);
  --color-difficulty-expert: oklch(58% 0.2 25);

  --font-sans: "Inter Var", ui-sans-serif, system-ui, sans-serif;
  --font-mono: "JetBrains Mono Var", ui-monospace, monospace;

  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --spacing-content-max-width: 72ch;
}
```

## Package accent color (the multi-package extension point)

Every content package (Linux, SQL, Python, Docker, ...) gets exactly one additional token:
`--color-package-accent`, set per package, used only for the package's nav badge, module-card
border, and breadcrumb root — never for body text or anything contrast-critical. This is the
entire mechanism by which a new package gets "its own visual identity" without a new stylesheet,
new component, or design review beyond picking one color that passes AA against both surface
tokens.

```json
// data/packages/linux.json (conceptual — package registry, see 28-extension-guide.md)
{
  "id": "linux",
  "label": "Linux Handbook",
  "accent": "oklch(60% 0.12 200)"
}
```

A package-accent value that fails contrast validation (see below) is a build failure, not a
warning — this is the one design guardrail enforced by tooling rather than review.

## Dark / light mode

- **Mechanism:** `prefers-color-scheme` media query is the default; an explicit toggle persists
  an override to `localStorage` and stamps `data-theme="dark|light"` on `<html>` — light and dark
  are peers, not a light-primary/dark-secondary implementation.
- **No FOUC:** the theme-resolution script is a single inline `<script>` in `<head>`, blocking,
  under 200 bytes minified — reads `localStorage` then falls back to the media query before first
  paint. This is the one piece of render-blocking JS the performance budget
  ([21-performance-budget.md](21-performance-budget.md)) explicitly allows, because the
  alternative (a flash of wrong-theme content) is a worse UX and CLS regression than 200 bytes.
- **Every token above ships a `-dark` pair** (or a single OKLCH value with a computed dark
  variant) — there is no component-level "if dark mode, use this other class" branching; dark
  mode is a token swap, full stop.

## Contrast validation (WCAG 2.2 AA, ties to 20-accessibility-strategy.md)

- Body text tokens (`--color-ink` on `--color-surface`, both modes) must hit ≥4.5:1.
- Large text / UI component tokens (difficulty dots, badges, focus rings) must hit ≥3:1.
- A build-time script validates every token pair in the theme file, including every registered
  package's `accent` value against both surface tokens — this is what makes "add a package"
  safe for a non-designer contributor to do without breaking accessibility silently.

## Typography scale

Fixed modular scale, not per-page overrides — content pages never set inline font sizes; the
Markdown → HTML pipeline maps heading levels straight to the scale below:

| Token | Size | Use |
|---|---|---|
| `--text-xs` | 0.75rem | metadata (reading time, updated date) |
| `--text-sm` | 0.875rem | badges, breadcrumbs, sidebar |
| `--text-base` | 1rem | body copy |
| `--text-lg` | 1.125rem | TL;DR block, lead paragraph |
| `--text-xl`–`--text-3xl` | 1.25–1.875rem | H3–H1 |

## What this spec deliberately excludes

- Component markup/structure — that's the Component Library deliverable, not yet authored (see
  gap note above); this file only guarantees the tokens it will consume already exist and are
  stable.
- Illustration/icon style beyond "Lucide, tokenized stroke color" — icon set choice is locked by
  `prompt.txt` (Lucide), sizing/color inherit typography and semantic-color tokens above, nothing
  icon-specific to specify yet.
