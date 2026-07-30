# Accessibility Strategy

Scope: how WCAG 2.2 AA (`prompt.txt` §3) and Lighthouse Accessibility = 100 are actually achieved
and kept true over time, not just stated as a target.

## Why this needs a strategy document, not just a Lighthouse number

Lighthouse's accessibility score checks a subset of automatable rules (~30–40% of WCAG success
criteria, industry-wide estimate). Hitting 100 on Lighthouse and being WCAG 2.2 AA compliant are
different claims — this document covers both the automatable gate (CI-enforced) and the
non-automatable practices (design and content discipline) that Lighthouse can't check.

## Layered enforcement

| Layer | Mechanism | Catches |
|---|---|---|
| Design tokens | [12-theme-specification.md](12-theme-specification.md) contrast validation | Color contrast failures, before a single component is built |
| Automated CI gate | axe-core against sample routes ([17-github-actions-workflow.md](17-github-actions-workflow.md)) | Missing alt text, invalid ARIA, missing form labels, contrast regressions |
| Component-level requirements (below) | Manual review checklist | Keyboard traps, focus order, screen-reader semantics automated tools miss |
| Periodic manual pass | Screen reader spot-check (NVDA/VoiceOver) on representative page types (topic, command, hub, search) | Real assistive-tech experience gaps no automated tool catches |

## Component-level requirements

These apply to every reusable UI surface referenced across the existing architecture docs — the
Component Library deliverable (not yet written, per the numbering note in
[00-README.md](00-README.md)) inherits these as binding requirements when it's authored, rather
than accessibility being retrofitted onto components after the fact.

- **Skip link.** First focusable element on every page, jumps to main content — required because
  the persistent sidebar ([03-navigation-sidebar.md](03-navigation-sidebar.md)) means keyboard
  users otherwise tab through the entire nav tree on every single page load.
- **Landmark regions.** `<nav>`, `<main>`, `<aside>` (sidebar), `<footer>` — one of each per page,
  correctly labeled with `aria-label` where more than one of the same landmark type exists (e.g.
  top nav vs. in-page table of contents, both arguably "navigation").
- **Focus states.** Every interactive element has a visible focus ring using the theme's focus
  token — never `outline: none` without a replacement, and never a focus style that relies on
  color alone (must also change shape/thickness) to pass 1.4.11 Non-text Contrast.
- **Keyboard shortcut (`/` to focus search, per [05-search-taxonomy.md](05-search-taxonomy.md)).**
  Must not fire while focus is already inside a text input/textarea (a common shortcut-conflict
  bug) and must be documented in a discoverable keyboard-shortcuts help surface, not just this doc.
- **Quiz `<details>`/`<summary>` pattern** ([10-content-template.md](10-content-template.md)
  Section E). Native `<details>` is used specifically because it is keyboard- and
  screen-reader-accessible by default — this is why the template mandates the native element
  instead of a custom collapsible built from `<div>`s.
- **Copy Code button.** Icon-only buttons carry an `aria-label` ("Copy code to clipboard"), and a
  success state is announced via `aria-live="polite"`, not just a visual checkmark swap.
- **Difficulty dots and career badges** (🟢/🟡/🔴, [01-roadmap.md](01-roadmap.md)). Emoji alone is
  not sufficient — each dot's accessible name is the full label ("Must Know difficulty"), not the
  emoji glyph, since emoji rendering and screen-reader pronunciation vary by platform.
- **Mermaid diagrams.** Every diagram callout ([10-content-template.md](10-content-template.md)
  Section D) ships an adjacent text-equivalent description, not just alt text on a rendered image
  — diagrams are frequently complex enough that alt text alone under-serves screen-reader users.
- **Dark mode toggle.** Operable via keyboard, announces its current state
  (`aria-pressed` or equivalent), and respects `prefers-reduced-motion` for any transition
  animation.

## Content-level accessibility (distinct from component-level)

- **Heading hierarchy.** The fixed page template ([10-content-template.md](10-content-template.md))
  guarantees one `H1` and a strict heading order — content authors never skip levels for visual
  sizing reasons (that's a typography-scale problem, solved by
  [12-theme-specification.md](12-theme-specification.md) tokens, not by misusing heading levels).
- **Link text.** "Related Topics" auto-rendered links (per 07-seo-jsonld.md) use the target
  page's actual title as link text, never generic "click here" — this is enforced structurally by
  the auto-generation itself, not left to per-page author discipline.
- **Terminal output / code blocks.** Rendered with sufficient contrast in both themes (validated
  by the same token-contrast check as body text) and never conveyed by color alone (e.g. a
  "removed line" diff marker uses a `-` prefix, not only red text).

## Reduced motion & vestibular safety

Any animation (theme transition, Mermaid diagram render, future interactive MDX content) respects
`prefers-reduced-motion: reduce` by disabling non-essential transitions — a binding constraint on
every future animated component, not an opt-in.

## Testing cadence

- Automated axe-core gate: every PR (see [23-testing-strategy.md](23-testing-strategy.md)).
- Manual screen-reader spot-check: before each new content-package launch
  ([28-extension-guide.md](28-extension-guide.md)) and before any Component Library / navigation
  structural change — not on every content PR, where it would be prohibitively slow for 218+
  pages and provides no new signal on pages using already-audited components.

## Non-goals

- No claim of WCAG AAA compliance — AA is the stated target (`prompt.txt` §3); AAA-only criteria
  (e.g. sign-language interpretation for video) are out of scope.
- No accessibility overlay/widget (the "accessibility toolbar" plugins common on marketing sites)
  — these are widely documented to conflict with native assistive technology and are explicitly
  rejected in favor of the built-in-correctly approach above.
