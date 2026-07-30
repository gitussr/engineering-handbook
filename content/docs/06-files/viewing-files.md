---
title: "Viewing Files"
description: "cat, less, more, head, and tail — picking the right tool depending on file size and what you actually need to see."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/creating-copying-moving-removing-files"]
relatedTopics: ["editing-files"]
relatedCommands: ["cat"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#cat-vs-less"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/editing-files"
prevTopic: "06-files/creating-copying-moving-removing-files"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["cat vs less command", "tail -f log", "head command linux", "less command navigation"]
canonicalUrl: "/docs/files/viewing-files"
---

# Viewing Files

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `cat` dumps a whole file at once — fine for small files, unusable for huge ones.
> `less` pages through a file interactively without loading it all into memory. `head`/`tail`
> show just the start/end. `tail -f` streams new lines live — the default way to watch a log.

## What is it?

Five commands for reading file content without editing it, each suited to a different situation:
dump everything (`cat`), page through interactively (`less`, `more`), or show just one end of the
file (`head`, `tail`).

## Why does it exist?

A 2GB log file can't be usefully dumped to a terminal with `cat` — it would scroll past instantly
and strain the terminal itself. Different viewing tools exist because "view a file" means
something different depending on the file's size and what you're actually trying to find.

## Where is it used?

- `cat` — small files, or piping a file's content into another command
- `less` — large files, interactive reading, searching within a file
- `more` — a simpler, older predecessor to `less`, still occasionally the only pager available on
  a minimal system
- `head` — checking a file's start (headers, first few log lines)
- `tail` — checking a file's end, and especially `tail -f` for watching a log grow live

## How it works

> 📊 Diagram: a file represented as a long strip, with four labeled brackets showing what portion
> each tool reveals — `cat` (the whole strip at once), `less` (a scrollable window, only part
> loaded), `head` (just the start), `tail` (just the end, with `-f` shown as a live-following
> arrow at the very tip).

| Command | Shows | Best for |
|---|---|---|
| `cat file` | Entire file, all at once | Small files, or feeding into a pipe |
| `less file` | Interactive, scrollable, searchable | Large files, exploring content |
| `more file` | Interactive, scrollable, forward-only | Minimal systems without `less` |
| `head file` | First 10 lines (configurable) | Quick peek at a file's start |
| `tail file` | Last 10 lines (configurable) | Quick peek at a file's end |
| `tail -f file` | Last lines, then keeps streaming new ones live | Watching an active log in real time |

`less` loads content on demand rather than all at once, which is exactly why it handles huge
files comfortably where `cat` would flood the terminal.

## Real-world example

An engineer investigating a production incident runs `tail -f /var/log/nginx/error.log` to watch
errors appear in real time while reproducing the issue, then switches to `less` on the same file
once they need to search backward for when the errors actually started — two different tools for
two different moments in the same investigation.

## Commands

- [`cat`](../../commands/cat.md) — full syntax and examples

`less`, `more`, `head`, and `tail` are taught in full on this page; their own canonical command
pages are a documented gap (see the module's Known Gaps note).

## Production example

```
$ head -20 access.log
$ tail -f /var/log/nginx/error.log
$ less /var/log/syslog
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `less` for anything larger than a screenful | Use `cat` on a multi-gigabyte file and flood your terminal |
| Use `tail -f` to watch a log live during an investigation | Repeatedly re-run `cat` on a growing log to check for new lines |
| Use `head`/`tail` for a quick peek before committing to a full view | Open a huge file in `less` when you only needed the first few lines |

## Common mistakes

- Using `cat` on a huge file out of habit, flooding the terminal instead of using `less`.
- Not knowing `tail -f` exists and manually re-running `tail` or `cat` repeatedly to check for new
  log lines.
- Forgetting `less` supports search (`/pattern`, then `n` for next match) and instead scrolling
  manually looking for something.

## Best practices

- Default to `less` over `cat` for anything you're not certain is small.
- Use `tail -f` as the standard way to watch a log during live troubleshooting.
- Learn `less`'s search (`/pattern`) — it's faster than manual scrolling for finding something
  specific in a large file.

## Exercises

1. View a large file with `less` and search within it using `/pattern`.
2. Use `tail -f` on a log file (or any file being actively written to) and watch it update live.
3. Explain in one sentence why `cat` is a poor choice for viewing a multi-gigabyte file.

## Quiz

**Q: What does `tail -f` do that plain `tail` doesn't?**
<details><summary>Show answer</summary>
It keeps running and streams new lines as they're appended to the file, instead of just showing
a snapshot of the last lines once.
</details>

**Q: Why is `less` better suited than `cat` for very large files?**
<details><summary>Show answer</summary>
`less` loads content on demand rather than dumping the entire file into the terminal at once,
making it usable regardless of file size.
</details>

## Interview questions

- How would you watch a log file update in real time from the terminal? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `cat` for small files or piping; `less` for large/interactive viewing; `head`/`tail` for one end
  of a file.
- `tail -f` is the standard way to watch a growing log live.
- `less` supports search and doesn't require loading the whole file into memory.

## Related topics

- [Editing Files: nano, vim Basics](editing-files.md)
- [Module 16: Logs](../16-logs/index.md)
