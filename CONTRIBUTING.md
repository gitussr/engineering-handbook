# Contributing

How new pages and modules get added to this documentation, and how to check one before it ships.
Read [WRITING_RULES.md](WRITING_RULES.md) (voice, structure) and
[STYLE_GUIDE.md](STYLE_GUIDE.md) (formatting) first — this document is the workflow layered on
top of both.

## Adding a topic page to an existing module

1. Find the module folder under `content/docs/{NN-module-slug}/`.
2. Copy the topic template from
   [architecture/10-content-template.md](architecture/10-content-template.md) Section A.
3. Fill every required frontmatter field. Check against `TERMINOLOGY.md` for career tag slugs and
   difficulty values.
4. Check `relatedTopics`/`relatedCommands` against sibling pages in the module — if another page
   already owns the explanation you're about to write, link to it instead of duplicating it
   (WRITING_RULES.md Rule 5).
5. Update the module's `index.md` topic table and the `nextTopic`/`prevTopic` chain on both the
   new page and its neighbors.
6. Run the pre-publish checklist below before considering the page done.
7. Update `data/roadmap.json`, `data/search-index.json` generation inputs are automatic from
   frontmatter — no manual data-file edit needed (see
   [architecture/02-folder-structure.md](architecture/02-folder-structure.md) "generated, not
   hand-authored" rule).

## Adding a command page

Same process, using [architecture/10-content-template.md](architecture/10-content-template.md)
Section B, under `content/commands/{command}.md`. One canonical page per command — check
`content/commands/` first; if the command already has a page, link to it from your new topic page
instead of creating a duplicate.

## Adding an entirely new topic area not in the 34-module roadmap

(Terraform, Ansible, Podman, eBPF, CI/CD, Observability, Cloud Native, Advanced Networking,
Kernel Internals, and similar.) Follow
[WRITING_RULES.md](WRITING_RULES.md) Rule 10 — deepen an existing module if it fits one, or add a
new sibling section (like Developer Essentials) if it's a standalone skill area. Never renumber
existing modules and never add a 35th core module.

## Pre-publish checklist (self-audit, modeled on the Module 01 review)

Before marking a page or module done, check it the way
[architecture/11-module-01-audit.md](architecture/11-module-01-audit.md) checked Module 01:

- [ ] **Unnecessary explanations** — does every paragraph answer a question a working engineer
      would actually ask, or is some of it padding?
- [ ] **Missing concepts** — does this page use any term it doesn't define and that no earlier
      page in the reading path defined either?
- [ ] **Duplicated information** — does another page already own this explanation? Link, don't
      repeat.
- [ ] **Weak examples** — is the "Real-world example" scenario-specific, or is it a generic
      template sentence with one noun swapped in?
- [ ] **Confusion points** — would a beginner reading this for the first time get stuck anywhere
      without a defined term or missing step?
- [ ] **Under-detailed spots** — would a working professional reading this for reference feel
      short-changed anywhere?
- [ ] **Linking opportunities** — are `relatedTopics`, `relatedCommands`, `relatedLabs`,
      `relatedInterviewQuestions`, `relatedCheatsheet` all filled in where a real connection
      exists (and left empty only where it's genuinely premature, e.g. pre-CLI modules)?
- [ ] **Terminology** — does it match [TERMINOLOGY.md](TERMINOLOGY.md) exactly?
- [ ] **Length** — is it in the same band as sibling pages in the module (Module 01's pages run
      roughly 90–160 lines; use that as the reference band unless the topic genuinely needs more)?
- [ ] **Conditional components** — for every conditional section included (Do/Don't, Syntax,
      Further Reading, Compatibility Notes, Decision Matrix), would deleting it lose real
      information? For every one omitted, is that a deliberate choice, not an oversight?
- [ ] **Career tags** — is `careerRelevance` a genuinely selective 3–5 tags (or `foundational:
      true` for the rare universal page), not a reflexive full list?

## Module-level review

When a full module (all its topic pages) is done, do one pass across the whole module the way the
Module 01 audit did:

- Check for duplication *between* sibling pages in the module, not just within one page.
- Check the "no commands yet" / "commands appear here" claims are consistent across every page in
  the module (WRITING_RULES.md Rule 6) — this was the actual defect found in Module 01.
- Confirm the module's `index.md` topic table, difficulty labels, and `nextTopic`/`prevTopic`
  chain are all correct and complete.

## Process for the remaining modules (02–34)

Module 01 is the canonical template — confirmed in
[architecture/11-module-01-audit.md](architecture/11-module-01-audit.md). Each subsequent module
is generated against this same template and checklist, module by module, in roadmap order. A
module is not started until the previous one has passed the pre-publish and module-level checks
above.
