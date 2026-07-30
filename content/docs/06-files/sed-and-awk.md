---
title: "sed and awk"
description: "Transforming text instead of just finding it — sed for find-and-replace, awk for column-based processing."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["06-files/grep"]
relatedTopics: ["cut-sort-uniq-wc"]
relatedCommands: []
careerRelevance: ["devops", "backend", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#sed-vs-awk"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/cut-sort-uniq-wc"
prevTopic: "06-files/grep"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["sed find and replace", "awk print column", "sed -i", "awk field separator"]
canonicalUrl: "/docs/files/sed-and-awk"
---

# sed and awk

🟡 Good to Know · Relevant for: DevOps · Backend · Linux Administrator

> **TL;DR:** `grep` finds lines; `sed` and `awk` transform them. `sed 's/old/new/'` is the
> find-and-replace you'll use constantly. `awk '{print $2}'` extracts a specific column from
> structured text. Both are full languages — this page covers the 80% case, not the whole thing.

## What is it?

`sed` (stream editor) applies text transformations — most commonly find-and-replace — to input,
line by line. `awk` is a pattern-scanning and column-processing language, especially suited to
structured text (like `/etc/passwd`, log files, or command output with consistent columns).

## Why does it exist?

`grep` can find lines, but can't change them. `sed` fills that gap for simple substitutions
without opening a file in an editor. `awk` goes further, treating each line as a set of fields
(columns), letting you extract, rearrange, or compute on structured text directly from the
command line — genuinely powerful for reports and log analysis without writing a full script.

## Where is it used?

- `sed` — find-and-replace across a file or a stream, in scripts or one-off terminal use
- `awk` — extracting a specific column from `ps`, `df`, log lines, or any consistently-delimited
  text; light on-the-fly calculations (e.g. summing a column)

## How it works

> 📊 Diagram: two transformation examples — `sed 's/foo/bar/'` shown rewriting "foo" to "bar"
> within a line of text, and `awk '{print $2}'` shown extracting just the second whitespace-
> separated field from a line with several columns — making the "line rewriting vs. column
> extraction" distinction visual.

**sed** — most common form: `sed 's/OLD/NEW/'` (substitute first match per line), `sed
's/OLD/NEW/g'` (all matches per line, `g` = global). `sed -i` edits a file in place instead of
just printing the result.

```
$ sed 's/error/ERROR/g' app.log
```
Replace every occurrence of "error" with "ERROR" and print the result (original file untouched
without `-i`).

**awk** — treats each line as fields separated by whitespace by default (`$1`, `$2`, ... for
each field, `$0` for the whole line):

```
$ ps aux | awk '{print $2, $11}'
```
Print just the PID (`$2`) and command (`$11`) columns from `ps aux` output.

## Real-world example

An engineer needs to update a config value across dozens of files after a hostname change:
`sed -i 's/old-hostname/new-hostname/g' *.conf` does it in one command, in place, across every
matching file — a task that would be slow and error-prone done manually in an editor one file at
a time. Separately, `df -h | awk '{print $5, $6}'` quickly extracts just usage percentage and
mount point from `df`'s output, dropping columns that aren't currently relevant.

## Commands

No new canonical command pages on this page — `sed` and `awk` are taught in full here as a
documented gap, consistent with this module's approach to its less-frequently-isolated commands.

## Production example

```
$ sed -i 's/old-hostname/new-hostname/g' /etc/nginx/nginx.conf
$ df -h | awk '{print $5, $6}'
Use% Mounted
 43% /
 12% /data
```

## Do / Don't

| Do | Don't |
|---|---|
| Test a `sed` substitution without `-i` first, then add `-i` once confirmed | Run `sed -i` directly on an important file without testing the pattern first |
| Use `awk` for quick column extraction from structured output | Write a multi-line script for a task `awk '{print $N}'` does in one line |
| Back up a file before `sed -i` on anything important | Assume `sed -i` is trivially reversible — it isn't, without a backup |

## Common mistakes

- Running `sed -i` directly without testing the substitution pattern first (without `-i`),
  risking an unintended change applied permanently and immediately.
- Forgetting the `g` flag in `sed 's/old/new/'`, only replacing the first match per line instead
  of all of them.
- Assuming `awk`'s field numbering is zero-indexed — it starts at `$1`, with `$0` reserved for the
  entire line.

## Best practices

- Always dry-run a `sed` substitution (without `-i`) before applying it in place.
- Keep a backup (`cp file file.bak`) before any `sed -i` on something important, even after
  testing.
- Reach for `awk` specifically when the task is column/field extraction from structured text —
  it's usually faster than combining several other tools for the same result.

## Exercises

1. Use `sed` to replace a word in a test file, first without `-i` to preview, then with `-i` to
   apply.
2. Use `awk` to extract a specific column from `ps aux` or `df -h` output.
3. Explain in one sentence why testing a `sed -i` substitution first matters.

## Quiz

**Q: What does the `g` flag do in `sed 's/old/new/g'`?**
<details><summary>Show answer</summary>
Replaces all occurrences of the pattern on each line, not just the first one.
</details>

**Q: In `awk '{print $2}'`, what does `$2` refer to?**
<details><summary>Show answer</summary>
The second whitespace-separated field (column) of the current line.
</details>

## Interview questions

- How would you replace a value across multiple config files from the command line? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `sed` transforms text (most commonly find-and-replace); `awk` processes structured, column-based
  text.
- `sed -i` edits in place — always test without it first.
- `awk '{print $N}'` is the fast way to extract a specific column from structured output.
- Both are full languages beyond what's covered here — this page is the practical 80% case.

## Related topics

- [cut, sort, uniq, wc](cut-sort-uniq-wc.md)
