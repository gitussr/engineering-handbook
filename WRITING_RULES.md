# Writing Rules

Canonical since the Module 01 pilot audit ([architecture/11-module-01-audit.md](architecture/11-module-01-audit.md)).
These are the rules that make every page in this documentation behave the same way. If a rule
here conflicts with something in an older architecture file, this document wins for content
generation — the architecture files describe structure, this one describes voice and judgment.

## 1. Voice

This is a reference manual, not a tutorial and not a blog. Every page assumes the reader wants an
answer immediately.

**Avoid:**
- Long introductions ("In this article, we will explore...")
- Motivational content ("Congratulations on taking the first step...")
- Historical stories, unless the history is technically load-bearing (e.g. why SysV init vs
  systemd matters is technical, not trivia — that's fine; "Linus Torvalds was a student in
  Helsinki in 1991" is not, unless the page is specifically about Linux's origin)
- Filler paragraphs that restate the heading in sentence form before answering it
- Repeating information already explained on another page — link to it instead (see Rule 5)

**Prefer:** short paragraphs, bullet lists, tables, comparison tables, decision matrices,
checklists, quick-reference blocks, and explicit Do/Don't pairs over prose whenever the content
is naturally enumerable.

## 2. The 15-step topic page structure is fixed

What is it? → Why does it exist? → Where is it used? → How it works → Real-world example →
Syntax (conditional) → Commands → Production example → Do/Don't (conditional) → Common mistakes
→ Best practices → Exercises → Quiz → Interview questions → Key Takeaways → Further Reading
(conditional) → Related topics.

Do not reorder. Do not skip a required section. See
[architecture/10-content-template.md](architecture/10-content-template.md) for the literal
skeleton.

## 3. Every concept answers six questions somewhere in its 15 sections

Why does it exist, when do professionals use it, what problem does it solve, what happens if
misused, what's the production best practice, what are the common mistakes. These map directly
onto existing sections (Why does it exist / Where is it used / Common mistakes / Best practices)
— don't add new headings for them, make sure the existing sections actually answer them.

## 4. Conditional components are a menu, not a checklist

TL;DR, Do/Don't, Syntax, Further Reading, Compatibility Notes, Version Differences, Decision
Matrix, FAQ (hub pages only) — each appears **only when the page has real content for it**.

Test before adding one: *if I deleted this heading, would the page lose real information, or
just lose a heading with a stub under it?* If it's just a stub, don't add it. This resolves the
tension between "every page should have X" and "avoid filler" — see
[architecture/11-module-01-audit.md](architecture/11-module-01-audit.md) finding #5 for the
concrete example (Compatibility Notes and Decision Matrix were correctly *not* added to most
Module 01 pages).

## 5. One concept, one owning page

If two pages would otherwise explain the same thing (e.g. kernel/OS/distro layering), pick the
page that's the natural home for it, give it the full explanation and the diagram, and have every
other page link to it with one sentence of context instead of re-explaining. Found and fixed in
Module 01 — see audit finding #1. Before writing a "How it works" section, check
`relatedTopics` for a page that might already own this explanation.

## 6. Commands appear before their dedicated module, deliberately, when illustrative

Concept modules may show short, read-only commands (e.g. `uname -a`, `cat /etc/os-release`) as
forward illustrations of what a concept looks like in a terminal — this is not the same as
teaching command syntax, which stays in the command's dedicated `/commands/{cmd}` page and its
owning module. If a page in a module genuinely has no command example, say so relative to a
sibling page that does ("No command example on this page — see [X] for one"), never as a
module-wide claim ("no commands in this module") unless it's actually true for every page in that
module.

## 7. Career relevance tags are selective, not reflexive

3–5 tags per page, chosen because the content would genuinely change how someone in that track
evaluates the page — not "this is Linux, so it's relevant to everyone." The exception: a small
number of genuinely foundational pages (what Linux is, where it's used) get `foundational: true`
and render as "All career paths" instead of an arbitrarily-assembled full tag list. See
[architecture/06-career-paths.md](architecture/06-career-paths.md) for the 10-tag taxonomy.

## 8. Diagrams are specified, never embedded

Every diagram is a `> 📊 Diagram: {description}` callout, specific enough for an illustrator to
act on without more context. One callout per concept, on the page that owns the concept — not
repeated on pages that merely reference it. Categories to reach for: Architecture, Flowcharts,
Process flow, Filesystem, Networking, Boot process, Permissions, Memory, Storage, Containers,
Systemd, Cloud architecture.

## 9. Every page is a knowledge-graph node

Prerequisites, Related Concepts, Related Commands, Career Relevance, Practice Labs, Interview
Questions, Cheat Sheets, Next/Previous Topic — all come from frontmatter arrays, rendered
automatically, never hand-typed as prose links in the body. See
[architecture/07-seo-jsonld.md](architecture/07-seo-jsonld.md).

**Legitimate empty arrays exist.** `relatedLabs: []` on every Module 01 page is correct, not a
gap — there's no hands-on lab possible before CLI fundamentals exist. Don't force a link that
doesn't belong just to make an array non-empty.

## 10. Future scalability — adding topics that don't exist in the 34-module roadmap yet

New topic areas (Virtualization deep-dive, LXC, Podman, eBPF, Terraform, Ansible, CI/CD,
Observability, Cloud Native, Advanced Networking, Kernel Internals, etc.) must fit into the
existing architecture without a restructure. The pattern:

- **If it deepens an existing module**, add it as a new sub-topic page inside that module's
  folder (e.g. Podman → a new page in `23-docker-basics/` or `22-containers/`, cross-linked as an
  alternative container runtime; eBPF → a new 🔴 Expert page in `21-performance/` or
  `19-security/`; Kernel Internals → 🔴 Expert pages added to `05-file-system/` or a new
  sub-section, not a new top-level module).
- **If it's a genuinely new standalone skill area** (Terraform, Ansible, CI/CD, Observability),
  it follows the same pattern as Git: a **sibling section outside the 34-module core roadmap**,
  structured like [architecture/09-developer-essentials.md](architecture/09-developer-essentials.md)
  — e.g. an "Infrastructure Automation" or "Cloud Native Tooling" section, not a renumbered
  Module 35+. This keeps the core Linux roadmap numbering permanently stable.
- **Never renumber existing modules** to insert something new — URLs don't contain module numbers
  (see [architecture/04-url-structure.md](architecture/04-url-structure.md)) specifically so this
  is always possible without breaking links.
- Every new page still uses the exact templates in
  [architecture/10-content-template.md](architecture/10-content-template.md) and every rule in
  this document — there is no separate process for "new" topic areas.
