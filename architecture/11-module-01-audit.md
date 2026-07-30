# Module 01 Quality Audit — Pilot Review

Reviewed as: Senior Linux Engineer, Senior Technical Writer, Documentation Information Architect,
UX Writer, Curriculum Designer. Scope: `content/docs/01-linux-basics/` (index + 7 topic pages),
against the new documentation standard (reference-manual tone, information density, new
components, knowledge-graph linking, future scalability).

Verdict: the structural skeleton (frontmatter, 15-step order, difficulty/career badges) holds up.
Six real defects found, below, all fixed in this pass. This audit is the record of what was wrong
and why the fix is correct — it is not a rubber stamp.

---

## 1. Duplicated information

**Finding:** `what-is-linux.md` and `kernel-vs-os-vs-distro.md` both explain the kernel/OS
layering, each with its own diagram callout (a layered-stack diagram in the first, a
concentric-boxes diagram in the second) describing the same three-layer idea from slightly
different angles. A reader goes through the same mental model twice within two consecutive pages.

**Why it happened:** `what-is-linux.md` was written to be self-contained ("What is it?" answered
fully in isolation) rather than written as node 1 of a sequence that assumes node 2 exists.

**Fix applied:** `what-is-linux.md`'s "How it works" section is trimmed to one sentence
(kernel manages hardware, everything else sits on top) with an explicit pointer to
`kernel-vs-os-vs-distro.md` for the full breakdown. That page keeps the one canonical diagram and
the full layer-by-layer explanation. One concept, one owning page — every other page links to it
instead of re-explaining it.

## 2. Inconsistent commands policy within the same module

**Finding:** `gui-vs-cli.md` and `linux-in-industry.md` both state some version of "no commands
introduced yet — commands start in Module 03." But `what-is-linux.md`, `linux-distributions.md`,
and `kernel-vs-os-vs-distro.md` already show `uname -a`, `cat /etc/os-release`, and `ls` as
illustrative output in their Production Example sections. The claim is false the moment a reader
has already seen three commands earlier in the same module.

**Why it happened:** Each page was written independently against the template rather than checked
against sibling pages for module-wide consistency.

**Fix applied:** Standardized the actual policy (documented in `WRITING_RULES.md`): Module 01 may
use short, read-only identification commands (`uname`, `cat /etc/os-release`, `ls`) as forward
illustrations, because showing what a concept looks like in a terminal is not the same as teaching
command syntax — that teaching happens in Module 03 (Terminal) and the dedicated `/commands/`
pages. Pages with no natural command example now say "No command example on this page — see
[X](link) for one" instead of falsely claiming the whole module is command-free.

## 3. Over-broad career relevance tagging

**Finding:** `what-is-linux.md` and `linux-in-industry.md` tag all 10 career tracks as relevant.
Tagging everything is equivalent to tagging nothing — the badge stops being a filtering signal the
moment "relevant for" becomes "relevant for everyone."

**Why it happened:** Both pages are genuinely foundational, so it felt technically true to tag
every track. But the component's job is to help a reader in one track quickly judge "does this
page matter to me," not to state a universal truth.

**Fix applied:** Introduced a `foundational: true` frontmatter flag for the small number of pages
that are legitimately universal (this page and `linux-in-industry.md` qualify — a page explaining
what Linux is or where it runs). `careerRelevance` on those two is now the badge text "All career
paths" rendered as a single badge, not 10 separate ones. Every other page keeps a tightened,
genuinely-selective subset (3–5 tracks, not the reflexive full list) — checked against whether the
page's content would actually change a hiring manager's or engineer's evaluation of that track.

## 4. Weak, interchangeable "Real-world example" sections

**Finding:** Several pages used near-identical phrasing — "An engineer at a company running X..."
— differing only in the noun substituted in. `what-is-linux.md`, `linux-distributions.md`, and
`linux-philosophy-and-fhs.md` all read as variations of the same sentence template. This is the
kind of filler the new standard explicitly calls out.

**Fix applied:** Rewrote each example to be scenario-specific with a distinct shape (an incident,
a comparison, an onboarding moment, a decision point) rather than a fill-in-the-blank sentence.
See the diffs in each page.

## 5. Missing components required by the new standard

**Finding:** No page had a TL;DR/Quick Summary a reader could scan in 5 seconds before committing
to the full 15-step read. "Summary" existed only at the very bottom, meaning a returning reader
skimming for a refresher had to scroll past the entire page to get it. No page had a Do/Don't
table (prose-only Common Mistakes / Best Practices are slower to scan than a table). No page had
Further Reading pointing to authoritative external references (man pages, official distro docs).

**Fix applied:**
- Added a **TL;DR** block directly under the difficulty/career badge row — 1–2 sentences, no
  prose padding.
- Renamed the bottom **Summary** section to **Key Takeaways** sitewide for terminology
  consistency with the new component vocabulary (see `TERMINOLOGY.md`), keeping it bulleted.
- Added a compact **Do / Don't** table above the existing (kept) Common Mistakes / Best Practices
  prose — the table is the scan path, the prose is the depth path for a reader who wants the "why."
- Added **Further Reading** linking to `man` pages / official distro documentation, positioned
  after Key Takeaways and before Related Topics (internal knowledge-graph links stay visually
  separate from external references).

## 6. Terminology inconsistency

**Finding:** "distro" and "distribution" were used interchangeably with no stated rule; "GNU/Linux"
appeared once with no prior definition; "headless" was used in `gui-vs-cli.md` without being
defined anywhere in the module.

**Fix applied:** Documented in `TERMINOLOGY.md`: "distribution" on first use per page, "distro" for
every subsequent use on that page — applied retroactively across all 8 files. "Headless" now has a
one-clause inline definition on first use. "GNU/Linux" usage is scoped to
`kernel-vs-os-vs-distro.md` only (the one page that owns that distinction) rather than appearing
unexplained elsewhere.

---

## Reviewed and found NOT to be a problem (so they aren't "fixed" into existence)

- **Page length:** all 7 topic pages land in a consistent 90–150 line band pre-fix. This is
  correct density for a reference page at this depth — not flagged for change. The module index
  page is intentionally much shorter (it's a table of contents, not a topic page).
- **FAQ component:** deliberately NOT added to topic pages. Quiz + Interview Questions already
  cover the "question and answer" surface for a topic page; adding FAQ on top would be the exact
  duplicated-information problem this audit is supposed to catch. FAQ is reserved for hub pages
  (career hub, cheat sheet hub) that have no Quiz of their own — documented in `STYLE_GUIDE.md`.
- **Compatibility Notes / Version Differences / Decision Matrix:** not added to any Module 01
  page. None of the 7 topics have version-specific behavior or a genuine multi-option decision at
  the sub-topic level (the one real decision — which distro to install — already has its own
  Decision Matrix-shaped table in `choosing-a-distro.md`). Forcing these headings onto pages with
  nothing to put under them would itself violate "avoid filler." The rule instead of "every page
  gets every component" is: **components are conditional on having real content**, codified in
  `WRITING_RULES.md`.
- **Empty `relatedLabs`/`relatedInterviewQuestions` arrays:** legitimate for Module 01 specifically
  — it's the one module that precedes any hands-on CLI work, so there is genuinely no lab to link
  yet. Documented as an explicit, intentional exception rather than a gap to silently fix later.

---

## Missing concepts (added)

- No page previously stated **what a shell is**, even briefly, despite `gui-vs-cli.md` and
  `linux-philosophy-and-fhs.md` both using the word. A one-sentence forward-reference definition
  ("a shell is the program that reads and runs your commands — covered fully in Module 04") is now
  present on first use in `gui-vs-cli.md`.
- **Long Term Support (LTS)** was used in `linux-distributions.md` and `choosing-a-distro.md`
  without being defined anywhere. Added a one-clause definition on first use.

## Internal linking improvements

- `what-is-linux.md` → `kernel-vs-os-vs-distro.md` link strengthened from a passive "related
  topic" mention to an explicit "full breakdown here" pointer at the point the duplicated content
  used to be (see finding #1).
- Added `relatedCommands` entries (`uname`, `cat`, `ls`) to the three pages that now show them,
  so the knowledge graph reflects what the page actually contains, not just what its concept
  frontmatter originally guessed.

---

## Confirmation

With the six findings above fixed, **Module 01 is confirmed as the canonical template** for
Modules 02–34: page structure, component set, tagging discipline, terminology rules, and linking
pattern established here are what every subsequent module is built against. The governing rules
are now written down (not just implied by example) in `CONTRIBUTING.md`, `STYLE_GUIDE.md`,
`TERMINOLOGY.md`, and `WRITING_RULES.md` at the project root, so consistency doesn't depend on
re-reading Module 01 by hand for every future module.
