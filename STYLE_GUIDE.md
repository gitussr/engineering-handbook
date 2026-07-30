# Style Guide

Formatting and component reference. For voice/tone rules and structural rules, see
[WRITING_RULES.md](WRITING_RULES.md). This document is the "how to format it" companion.

## Page header

Every content page opens with:

```
# {Title}

🟢/🟡/🔴 {Difficulty label} · Relevant for: {career badges or "All career paths"}

> **TL;DR:** {1-2 sentences}
```

- The difficulty dot and label always come first, career relevance always second, separated by
  `·`.
- TL;DR is a blockquote, bolded label, one to two sentences, no more. If you can't compress the
  page to two sentences, the page is trying to do too much — split it.

## Difficulty labels

| Emoji | Frontmatter value | Meaning |
|---|---|---|
| 🟢 | `must-know` | Required for almost every Linux-related job |
| 🟡 | `good-to-know` | Frequently used professionally, can be learned later |
| 🔴 | `expert` | Advanced — sysadmins, kernel engineers, specialized infra teams |

Always write the emoji + label together ("🟢 Must Know"), never the emoji alone — accessibility
and search both need the text.

## Career relevance badges

Rendered as `Relevant for: X · Y · Z`, title-cased, using the 10-tag taxonomy in
[architecture/06-career-paths.md](architecture/06-career-paths.md) (DevOps, Cloud, Cybersecurity,
Backend, SRE, Platform, Linux Administrator, Software Engineering, AI/ML Infrastructure,
WordPress/Web Hosting). Foundational pages render `Relevant for: All career paths` instead of
listing all 10 individually — see [WRITING_RULES.md](WRITING_RULES.md) Rule 7.

## Tables over prose

Default to a table whenever content is naturally two-or-more columns of comparable items:
arguments, options, exit codes, common errors, distro comparisons, career-to-module mappings. A
bulleted list is the fallback only when items don't share comparable fields.

### Do/Don't table

```markdown
| Do | Don't |
|---|---|
| {short imperative} | {short imperative} |
```

Each row is a matched pair — the "don't" is the direct wrong version of the "do," not an
unrelated mistake. Keep each cell to one short imperative clause; if it needs a full sentence of
justification, that justification belongs in Common Mistakes or Best Practices below the table,
not crammed into the cell.

## Code and terminal output

- Command syntax blocks use a plain fenced code block with no shell prompt prefix:
  ```
  chmod [OPTIONS] MODE FILE
  ```
- Terminal sessions (commands + their actual output) use `$ ` as the prompt prefix on the command
  line only, never on output lines:
  ```
  $ uname -a
  Linux web-prod-03 6.8.0-31-generic #31-Ubuntu SMP x86_64 GNU/Linux
  ```
- Every code block gets a Copy Code button at render time (a UI/build concern, not something
  written into the Markdown) — write the block as if it will be copied verbatim, no `$` on output
  lines, no line-number artifacts, no trailing prompts.

## Quiz format

```markdown
**Q: {question}**
<details><summary>Show answer</summary>
{answer, one to three sentences}
</details>
```

3–5 questions per topic page. Answers collapsed by default. Never duplicate a Quiz question
verbatim as an Interview Question on the same page — they should probe different angles of the
same topic, not repeat each other.

## Diagram callouts

```
> 📊 Diagram: {one sentence, specific enough for an illustrator to act on}
```

One per concept, positioned inside "How it works," on the page that owns the concept (see
[WRITING_RULES.md](WRITING_RULES.md) Rule 5 and Rule 8).

## Component reference — when each one is required vs conditional

| Component | Required on | Notes |
|---|---|---|
| TL;DR | Every topic/command page | 1-2 sentences, under the header badges |
| What is it? / Why does it exist? / Where is it used? / How it works / Real-world example / Commands / Production example / Common mistakes / Best practices / Exercises / Quiz / Interview questions / Key Takeaways | Every topic page | Fixed 15-step order, see WRITING_RULES.md Rule 2 |
| Syntax | Conditional | Only if the topic has a direct syntax form |
| Do/Don't | Conditional | Only if the topic has genuinely binary right/wrong actions worth a table |
| Further Reading | Conditional | Only for genuine external/authoritative references beyond internal links |
| Compatibility Notes / Version Differences | Conditional, command pages mostly | Only if behavior differs meaningfully across distros/versions |
| Decision Matrix | Conditional | Only for pages presenting a genuine multi-option decision (e.g. "which distro") |
| FAQ | Hub pages only | Never on topic/command pages — redundant with Quiz + Interview Questions there |

## Terminology

See [TERMINOLOGY.md](TERMINOLOGY.md) for the canonical word list (distro vs. distribution, when
"GNU/Linux" is used, "headless," "LTS," etc.).

## Frontmatter

Always matches the schema in
[architecture/10-content-template.md](architecture/10-content-template.md) exactly — field
names, field order, and array types. A page with missing required frontmatter fields is not
publishable; the sidebar, search index, and knowledge-graph links all depend on it being complete.
