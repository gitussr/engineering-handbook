# Phase 2 Content Templates (v2 — post Module 01 audit)

Status: **Canonical.** Established as the site-wide template after the Module 01 pilot audit
([11-module-01-audit.md](11-module-01-audit.md)). Every rule here exists because Module 01 either
got it right and it's worth locking in, or got it wrong and this is the corrected version.

Two kinds of section in every template below:

- **Required** — appears on every page of that type, no exceptions. If genuinely inapplicable,
  say so in one line rather than omitting the heading (keeps page shape predictable).
- **Conditional** — appears only when the page has real content for it. Do not add a conditional
  section just to look complete — that recreates the filler problem this standard exists to
  avoid. See [WRITING_RULES.md](../WRITING_RULES.md) "Conditional components" for the test.

---

## A. Topic / concept page template (required sections marked •, conditional marked ○)

```markdown
---
title: ""
description: ""
module: ""
moduleTitle: ""
stage: ""                    # beginner | intermediate | professional | production-engineer
difficulty: ""                # must-know | good-to-know | expert
foundational: false            # true only for the handful of pages genuinely relevant to every
                                # career track (e.g. "What is Linux") — see careerRelevance rule below
type: "concept"
prerequisites: []
relatedTopics: []
relatedCommands: []
careerRelevance: []           # 3-5 genuinely-relevant tags, NOT a reflexive full list — subset of:
                               # devops, cloud, cybersecurity, backend, sre, platform,
                               # linux-administrator, software-engineering, ai-ml-infrastructure,
                               # wordpress-web-hosting. Ignored/rendered as "All career paths" if
                               # foundational: true.
relatedLabs: []                # legitimately empty for pre-CLI modules (e.g. Module 01) — see WRITING_RULES.md
relatedInterviewQuestions: []
relatedCheatsheet: ""
furtherReading: []             # [{label, url}] — official docs / man pages, external
nextTopic: ""
prevTopic: ""
estimatedReadingTime: 0
updatedAt: ""
keywords: []
canonicalUrl: ""
---

# {Title}

🟢/🟡/🔴 {Difficulty label} · Relevant for: {careerRelevance badges, or "All career paths" if foundational}

> **TL;DR:** {one to two sentences — the answer a reader gets if they read nothing else on this page.} •

## What is it? •
{1-3 sentences. No preamble.}

## Why does it exist? •
{The problem it solves. Not history — the engineering reason.}

## Where is it used? •
{Real contexts: which layer of the stack, which kind of job, which kind of incident.}

## How it works •
{Minimal theory. If this concept is already fully explained on another page (don't re-explain
kernel/OS/distro layering on three different pages — own it on one page, link from the rest),
write one sentence + a link instead of repeating it. Add a diagram callout if genuinely helpful:
> 📊 Diagram: {short description of the recommended visual}}

## Real-world example •
{A short, concrete, SPECIFIC scenario — an incident, a comparison, an onboarding moment, a
decision point. Not a template sentence with one noun swapped ("An engineer at a company running
X..." repeated across pages is exactly the weak-example pattern the Module 01 audit flagged).}

## Syntax ○
{Only if the topic has a direct syntax form. Omit entirely for pure-concept pages — do not stub it.}

## Commands •
{Links to canonical /commands/{cmd} pages this concept introduces. If none apply on this specific
page, say "No command example on this page — see [sibling page] for one" instead of claiming the
whole module is command-free (a claim that breaks the moment a sibling page has commands).}

## Production example •
{A realistic terminal session or workflow — commands + output + what an engineer concludes.}

## Do / Don't ○
{2x2-ish quick-scan table, only when the topic has genuinely binary right/wrong actions worth a
table (most topics do). Table is the scan path; Common Mistakes/Best Practices below are the
depth path — don't let them duplicate each other's wording.}

| Do | Don't |
|---|---|
| {short imperative} | {short imperative} |

## Common mistakes •
{Bulleted. Each mistake states the wrong action AND the consequence.}

## Best practices •
{Bulleted. Production-grade, not textbook-grade.}

## Exercises •
{2-4 short hands-on tasks a learner can do immediately.}

## Quiz •
{3-5 questions, answers collapsed/revealed on click.}

## Interview questions •
{2-4 questions pulled from this topic, each linking to the full entry in the relevant
/interview-questions/{tier} page rather than duplicating the answer.}

## Key Takeaways •
{3-5 bullet recap — skimmable in 10 seconds. (Renamed from "Summary" for terminology consistency
— see TERMINOLOGY.md. This is the bottom-of-page recap; the TL;DR at the top is the pre-read
digest. They serve different readers and should not be copy-pasted from each other.)}

## Further Reading ○
{External, authoritative references only — man pages, official distro docs, upstream project
docs. Omit if there's genuinely nothing beyond what Related Topics already covers internally.}

## Related topics •
{Auto-rendered from relatedTopics/relatedCommands/careerRelevance/relatedLabs/
relatedInterviewQuestions/relatedCheatsheet frontmatter — not hand-written prose links.}
```

**Not included on topic pages, by design:** FAQ (redundant with Quiz + Interview Questions on a
page that already has both — reserved for hub pages with no quiz, see Section C);
Compatibility Notes / Version Differences / Decision Matrix (only added where a page has genuine
version-specific behavior or a genuine multi-option decision — most concept pages don't; forcing
the heading everywhere is filler). See [WRITING_RULES.md](../WRITING_RULES.md).

Page-header fields (Title, Description, Prerequisites, Estimated Reading Time, Difficulty,
Related Topics, Next/Previous Topic, Copy Code button, Terminal Output styling) render from
frontmatter + a shared page-header component — never re-typed manually per page.

---

## B. Command page template

Every command gets exactly one canonical page at `/commands/{command}` (see
[02-folder-structure.md](02-folder-structure.md)).

```markdown
---
title: "{command} — {one-line purpose}"
description: ""
relatedConcepts: []           # concept topic(s) that introduce this command
relatedCommands: []
careerRelevance: []
difficulty: ""
compatibility: []              # ○ only if behavior differs meaningfully across distros/versions —
                                # e.g. [{context: "GNU coreutils (Linux)", note: "..."}, {context: "BSD/macOS", note: "..."}]
updatedAt: ""
keywords: []
canonicalUrl: "/commands/{command}"
---

# {command}

🟢/🟡/🔴 {Difficulty label} · Relevant for: {careerRelevance badges}

> **TL;DR:** {one sentence — what this command does.} •

## Purpose •
## Syntax •
## Arguments •
{Table: argument, meaning, required/optional}
## Options •
{Table: flag, long form, meaning}
## Examples •
{3-6 examples, simplest first, increasingly realistic, each with a one-line "what this does"}
## Expected Output •
{Actual terminal output for at least one example, terminal-styled, Copy Code button.}
## Exit Status •
{Table of exit codes and meaning. Renamed from "Exit Codes" for terminology consistency. If the
command has no documented semantics beyond 0/non-zero, say so in one line — keep the heading.}
## Common Errors •
{Table: error message/symptom, cause, fix.}
## Security Considerations •
## Performance Considerations •
## Compatibility Notes ○
{Only if behavior genuinely differs across distros/versions — e.g. GNU `find` vs BSD `find` flag
differences. Most commands in this documentation's scope (GNU coreutils on target distros) won't
need this — don't stub it.}
## Production Usage •
{What experienced engineers actually do differently from the textbook usage.}
## Related Commands •
## Related Concepts •
```

---

## C. Hub / index page template (career hub, cheat sheet hub, interview question banks)

Hub pages are the one place **FAQ** is appropriate — they have no per-page Quiz to make it
redundant. Structure:

```markdown
# {Hub Title}
{One-line purpose of this hub.}
## {Grid/list of child pages}
## FAQ ○
{Only the questions that don't belong on any single child page — cross-cutting questions about
the hub as a whole, e.g. "Which career path should I pick?" on the Careers hub.}
## Related topics
```

---

## D. Diagram callout convention

```
> 📊 Diagram: {one-sentence description of the recommended diagram/flowchart/architecture
> diagram/terminal screenshot, specific enough for an illustrator to act on without more context}
```

One diagram callout per concept, on the page that owns that concept — not repeated on every page
that merely references it (see the Module 01 audit finding on duplicated kernel/OS/distro
diagrams).

---

## E. Quiz / Exercise formatting convention

- Exercises: numbered list, each a single imperative sentence actionable without re-reading the
  whole page.
- Quiz: `<details>`/`<summary>` per question, collapsed by default:

```markdown
**Q: What does `chmod 640 file` do?**
<details><summary>Show answer</summary>
Owner gets read+write, group gets read-only, others get no access.
</details>
```

---

## F. Conditional-component decision rule (prevents template bloat)

Before adding any ○ (conditional) section, answer: *if I deleted this heading, would the page
lose real information, or just lose a heading with a stub under it?* If the honest answer is "just
a stub," don't add it. This is the single rule that resolves the tension between "every page
should have X" (the brief) and "avoid filler" (the approval) — components are a menu, not a
checklist to fill mechanically. Full rationale in
[WRITING_RULES.md](../WRITING_RULES.md).

## G. What this template guarantees

Because every topic and command page is built from these skeletons, the knowledge-graph linking
(Prerequisites, Related Concepts, Related Commands, Career Relevance, Practice Labs, Interview
Questions, Cheat Sheets, Next/Previous Topic) is mechanical — it comes from frontmatter, not from
remembering to hand-add links on 218+ pages.
