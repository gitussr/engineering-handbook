# Developer Essentials (approved addition, outside the core roadmap)

**Decision:** Git is a developer tool that runs on Linux, macOS, and Windows — not a Linux OS
topic. It does not become Module 35 and is not folded into Module 18 (Shell Scripting). It ships
as its own top-level section, structurally equal to the Linux Roadmap but conceptually separate,
so the 34-module Linux path stays purely about the operating system.

## Site structure (updated)

```
Home
├── Linux Roadmap        (Modules 01–34 — unchanged, pure Linux OS content)
└── Developer Essentials (new, sibling section)
    ├── Git Basics
    ├── GitHub Basics
    ├── SSH Keys for GitHub
    ├── Git Workflow
    └── Git Cheat Sheet
```

## Topics and labels

| Topic | Slug | Label | What it covers |
|---|---|---|---|
| Git Basics | `git-basics` | 🟢 Must Know | What Git is, why it exists, init/add/commit/status/log, local workflow |
| GitHub Basics | `github-basics` | 🟢 Must Know | Remotes, push/pull/clone, GitHub UI basics, forking vs cloning |
| SSH Keys for GitHub | `ssh-keys-for-github` | 🟢 Must Know | Generating a key pair and registering it with GitHub — explicitly cross-linked to Module 13 SSH (the underlying mechanism is taught there; this page is the applied, GitHub-specific walkthrough) |
| Git Workflow | `git-workflow` | 🟡 Good to Know | Branching, merging, pull requests, resolving conflicts, a typical team workflow |
| Git Cheat Sheet | `git-cheat-sheet` | 🟢 Must Know | Single-page command reference, cross-listed on `/cheatsheets` |

## Folder structure addition

```
content/
└── developer-essentials/
    ├── index.md
    ├── git-basics.md
    ├── github-basics.md
    ├── ssh-keys-for-github.md
    ├── git-workflow.md
    └── git-cheat-sheet.md
```

## URL structure addition

| Content type | Pattern | Example |
|---|---|---|
| Developer Essentials topic | `/developer-essentials/{slug}` | `/developer-essentials/git-basics` |
| Developer Essentials hub | `/developer-essentials` | `/developer-essentials` |

Git Cheat Sheet is the one page with two URLs by design: canonical at
`/developer-essentials/git-cheat-sheet`, cross-listed (rel=canonical points back) on the
`/cheatsheets` hub grid so the "10 cheat sheets" landing-page promise still holds — see
[08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md).

## Navigation addition

Top nav gains one item, between `Docs` and `Careers`:

```
[Logo]  Docs   Developer Essentials   Careers   Cheat Sheets   Labs   Interview Prep   [Search]
```

Sidebar gains a standalone group, rendered below the four Linux Roadmap stage groups, not nested
inside any of them:

```
DEVELOPER ESSENTIALS
 ▸ Git Basics
 ▸ GitHub Basics
 ▸ SSH Keys for GitHub
 ▸ Git Workflow
 ▸ Git Cheat Sheet
```

## Cross-linking rules specific to this section

- `SSH Keys for GitHub` links to Module 13 SSH's key-generation page as a prerequisite (the
  mechanism), and back-links from that Module 13 page point here as the "applied to GitHub"
  example — this is the one place the Linux roadmap and Developer Essentials intentionally touch.
- Nothing else in the 34-module roadmap references Developer Essentials pages as a prerequisite —
  the Linux path must remain completable without ever visiting this section, per the approval
  rationale ("keep the Linux learning path pure").
- Developer Essentials pages carry `careerRelevance` tags same as any topic page (see
  [07-seo-jsonld.md](07-seo-jsonld.md)) — Git is relevant to nearly every track, so expect broad
  tagging here (DevOps, Backend, SRE, Platform, Software Engineering, WordPress/Web Hosting all
  apply).

## Roadmap page treatment

The `/roadmap` page (visual 4-stage progression) shows Developer Essentials as a small side
panel/callout, not a fifth stage — it's not part of the Beginner→Production Engineer progression,
it's a parallel track a learner can dip into any time.
