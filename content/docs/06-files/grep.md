---
title: "grep"
description: "Searching text by pattern — the single most-used text-processing tool in daily Linux work, and the foundation the next two topics build on."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/comparing-files"]
relatedTopics: ["sed-and-awk", "regular-expressions-basics"]
relatedCommands: ["grep"]
careerRelevance: ["devops", "sre", "cybersecurity", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#grep-basics"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/sed-and-awk"
prevTopic: "06-files/comparing-files"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["grep command linux", "grep -i -r -v", "grep pattern search", "grep count matches"]
canonicalUrl: "/docs/files/grep"
---

# grep

🟢 Must Know · Relevant for: DevOps · SRE · Cybersecurity · Linux Administrator

> **TL;DR:** `grep pattern file` prints every line matching a pattern. `-i` for case-insensitive,
> `-r` to search recursively through a directory, `-v` to invert (show non-matching lines), `-c`
> to count matches. This is the single most-used command in this entire documentation from here
> forward.

## What is it?

`grep` searches text for lines matching a pattern and prints the matching lines. The name comes
from "global regular expression print," a hint at its deep connection to regular expressions,
covered in [Regular Expressions Basics](regular-expressions-basics.md).

## Why does it exist?

Finding specific information inside logs, config files, and codebases by pattern — not just
exact filename, but content — is one of the most common tasks in Linux work. `grep` does this one
job extremely well, exactly the small-tools philosophy from Module 01, and composes naturally
with pipes (Module 04).

## Where is it used?

Everywhere, constantly: searching logs for an error pattern, searching a codebase for where a
function is used, filtering the output of another command down to just the relevant lines. It's
difficult to overstate how frequently `grep` shows up in real Linux work at every experience
level.

## How it works

> 📊 Diagram: a file's lines shown as a list, with `grep` acting as a filter that only lets
> matching lines through, and non-matching lines dropped — visually reinforcing "print only the
> lines that match."

| Flag | Effect |
|---|---|
| `-i` | Case-insensitive matching |
| `-r` | Recursively search all files in a directory |
| `-v` | Invert — show lines that do NOT match |
| `-c` | Count matching lines instead of printing them |
| `-n` | Show line numbers alongside matches |
| `-l` | Show only filenames that contain a match, not the matches themselves |
| `-E` | Extended regex support (more pattern syntax available — see Module 06's regex topic) |

`grep` can read from a file, multiple files, or stdin — meaning it composes directly with pipes:
`command | grep pattern` filters another command's output by pattern.

## Real-world example

An SRE investigating an incident runs `grep -i "timeout" /var/log/nginx/error.log` to pull every
timeout-related line out of a log with thousands of entries, instantly narrowing an
overwhelming file down to exactly what's relevant. The same skill, applied with `-r` across an
entire log directory, is often the very first command run at the start of an investigation.

## Commands

- [`grep`](../../commands/grep.md) — full syntax and examples

## Production example

```
$ grep -i "error" app.log
$ grep -r "TODO" src/
$ ps aux | grep nginx
$ grep -c "404" access.log
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `-i` when case doesn't matter for the search | Miss matches because of an unnecessary case-sensitivity assumption |
| Use `-r` to search an entire directory tree at once | Manually `grep` file by file when `-r` does it in one command |
| Use `grep -c` when you just need a count | Pipe `grep` output into `wc -l` when `-c` does the same thing directly |

## Common mistakes

- Forgetting `-i` and missing matches due to case differences that didn't actually matter for the
  search.
- Not knowing `-r` exists and manually looping over files instead of searching a directory
  recursively in one command.
- Confusing basic and extended regex support — some pattern syntax (like `|` for alternation)
  needs `-E` (or `grep -P` for Perl-compatible regex) to work as expected; full detail in
  [Regular Expressions Basics](regular-expressions-basics.md).

## Best practices

- Reach for `grep` before writing a custom script whenever the task is "find lines matching X" —
  it's almost always faster and more reliable than a hand-rolled alternative.
- Combine with pipes (`command | grep pattern`) to filter other commands' output — one of the
  most common patterns in daily terminal work.
- Use `-c` or `-l` when you need a count or filenames rather than the actual matching lines.

## Exercises

1. Search a log file for a specific word, case-insensitively.
2. Search an entire directory recursively for a pattern using `-r`.
3. Pipe the output of another command into `grep` to filter it.

## Quiz

**Q: What does `grep -v pattern file` show?**
<details><summary>Show answer</summary>
Lines that do NOT match the pattern — the inverse of normal grep behavior.
</details>

**Q: How would you search an entire directory tree for a pattern, not just one file?**
<details><summary>Show answer</summary>
`grep -r pattern directory/`
</details>

## Interview questions

- How would you find every line containing "error" across every log file in a directory? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `grep pattern file` prints matching lines — one of the most-used commands in all of Linux work.
- `-i`, `-r`, `-v`, `-c` cover the overwhelming majority of daily use cases.
- `grep` composes naturally with pipes to filter any other command's output.
- Full regex power (beyond simple text matching) is covered in
  [Regular Expressions Basics](regular-expressions-basics.md).

## Related topics

- [sed and awk](sed-and-awk.md)
- [Regular Expressions Basics](regular-expressions-basics.md)
