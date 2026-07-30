---
title: "cut, sort, uniq, wc"
description: "Four small tools that combine into powerful text-processing pipelines — extract a field, sort it, deduplicate it, count it."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/sed-and-awk"]
relatedTopics: ["regular-expressions-basics"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#top-ip-addresses-log"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/regular-expressions-basics"
prevTopic: "06-files/sed-and-awk"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["cut command linux", "sort command linux", "uniq command linux", "wc command linux", "sort uniq -c"]
canonicalUrl: "/docs/files/cut-sort-uniq-wc"
---

# cut, sort, uniq, wc

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `cut` extracts a field/column, `sort` orders lines, `uniq` removes or counts adjacent
> duplicates, `wc` counts lines/words/bytes. `sort | uniq -c | sort -rn` — "count and rank" — is
> one of the most useful pipelines in this documentation.

## What is it?

Four small, focused tools, each doing one job, designed to be chained together with pipes
(Module 04): `cut` (extract fields), `sort` (order lines), `uniq` (deduplicate adjacent lines),
and `wc` (count lines/words/characters).

## Why does it exist?

Answering "what are the top 5 most frequent values in this column" doesn't need a custom script —
it's exactly what chaining these four small tools together was built for, the small-tools
philosophy in its most practical daily form.

## Where is it used?

- Extracting a specific field from structured log lines (`cut`)
- Sorting output numerically or alphabetically (`sort`)
- Finding duplicate or unique entries (`uniq` — note: only catches *adjacent* duplicates, so it's
  almost always paired with `sort` first)
- Counting matches, lines, or words (`wc`)

## How it works

> 📊 Diagram: a pipeline flowing left to right — raw log lines → `cut` (extract just the IP
> column) → `sort` (put identical IPs next to each other) → `uniq -c` (count each IP's
> occurrences) → `sort -rn` (rank by count, highest first) — the canonical "top N" pipeline used
> as this page's running example.

| Tool | Purpose | Key flags |
|---|---|---|
| `cut -dDELIM -fN` | Extract field N, splitting on DELIM | `-d` delimiter, `-f` field number |
| `sort` | Order lines | `-n` numeric, `-r` reverse, `-u` unique while sorting |
| `uniq` | Remove adjacent duplicate lines | `-c` count occurrences (needs sorted input first) |
| `wc` | Count | `-l` lines, `-w` words, `-c` bytes |

**Critical detail**: `uniq` only removes/counts *adjacent* duplicate lines — it has no concept of
"duplicate anywhere in the file." This is why `sort` almost always comes before `uniq` in a
pipeline: sorting guarantees identical lines end up next to each other first.

## Real-world example

An SRE wants the top 5 IP addresses hitting a web server in an access log:

```
$ cut -d' ' -f1 access.log | sort | uniq -c | sort -rn | head -5
```

Extract the IP column, sort so duplicates are adjacent, count each one, sort by count descending,
show the top 5 — five small tools, one line, no custom script, and this exact pipeline shape
(extract → sort → count → rank) reappears constantly across totally different problems.

## Commands

No new canonical command pages on this page — `cut`, `sort`, `uniq`, `wc` are taught in full here
as a documented gap, consistent with this module's approach.

## Production example

```
$ wc -l access.log
15234 access.log
$ cut -d' ' -f1 access.log | sort | uniq -c | sort -rn | head -5
   842 203.0.113.42
   511 198.51.100.7
   ...
```

## Do / Don't

| Do | Don't |
|---|---|
| Sort before `uniq` when you need to catch all duplicates, not just adjacent ones | Run `uniq` on unsorted input and expect it to catch every duplicate |
| Use `wc -l` for a fast line count instead of eyeballing output | Manually count lines in a large file |
| Chain `cut`/`sort`/`uniq -c`/`sort -rn` for "top N" questions | Write a custom script for a task this pipeline already solves |

## Common mistakes

- Running `uniq` without sorting first and being confused when obvious duplicates aren't removed
  — `uniq` only catches adjacent ones.
- Forgetting `-n` on `sort` when sorting numbers, causing "10" to sort before "2" (lexical, not
  numeric, order).
- Not knowing `uniq -c` exists and using a more complicated approach to count occurrences.

## Best practices

- Internalize the `cut | sort | uniq -c | sort -rn` pipeline shape — it answers "what are the top
  N values" for an enormous range of different problems.
- Always sort before `uniq` unless you specifically know input is already sorted.
- Use `wc -l` liberally as a fast sanity check on how much data you're actually dealing with
  before processing it further.

## Exercises

1. Use `wc -l` to count the lines in any text file.
2. Build the full "top N" pipeline (`cut | sort | uniq -c | sort -rn | head`) against a log file
   or any structured text with a repeated field.
3. Explain in one sentence why `sort` almost always precedes `uniq` in a pipeline.

## Quiz

**Q: Why does `sort` almost always come before `uniq` in a pipeline?**
<details><summary>Show answer</summary>
`uniq` only removes/counts adjacent duplicate lines — sorting first guarantees identical lines
end up next to each other, so `uniq` actually catches all of them.
</details>

**Q: What does `sort -n` do differently from plain `sort`?**
<details><summary>Show answer</summary>
Sorts numerically instead of lexically — without `-n`, "10" would sort before "2" because sort
compares character by character by default.
</details>

## Interview questions

- How would you find the top 5 most frequent IP addresses in a web server access log? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `cut` extracts fields, `sort` orders, `uniq` deduplicates adjacent lines, `wc` counts.
- `uniq` requires sorted input to catch all duplicates, not just adjacent ones.
- The `cut | sort | uniq -c | sort -rn` pipeline is one of the most broadly useful patterns in
  daily Linux work.

## Related topics

- [Regular Expressions Basics](regular-expressions-basics.md)
