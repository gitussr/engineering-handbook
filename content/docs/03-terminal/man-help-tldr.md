---
title: "man, --help, tldr"
description: "Finding help for any command without leaving the terminal or searching the web — the single most valuable habit in this entire documentation."
module: "03-terminal"
moduleTitle: "Terminal"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["03-terminal/command-structure-and-syntax"]
relatedTopics: ["keyboard-shortcuts-and-history"]
relatedCommands: ["man"]
careerRelevance: ["devops", "linux-administrator", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#man-pages"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "tldr-pages project", "url": "https://tldr.sh"}]
nextTopic: "03-terminal/keyboard-shortcuts-and-history"
prevTopic: "03-terminal/command-structure-and-syntax"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["man command", "linux help command", "tldr pages", "man pages sections"]
canonicalUrl: "/docs/terminal/man-help-tldr"
---

# man, --help, tldr

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Software Engineering

> **TL;DR:** `man command` gives the full, authoritative reference. `command --help` gives a
> quick flag summary. `tldr command` (a third-party tool) gives practical examples instead of a
> full spec. Knowing all three — and when to reach for which — is what "never needing to Google
> Linux fundamentals again" actually depends on.

## What is it?

Three different ways to get help for a command without leaving the terminal: `man` (manual
pages — the full reference), `--help` (a quick built-in flag summary most commands support), and
`tldr` (a community-maintained tool that shows practical examples instead of a full spec).

## Why does it exist?

Documentation habits matter more than memorization. No professional memorizes every flag of every
command — they know how to look it up fast, in the terminal, without breaking flow to open a
browser. This is the actual skill this documentation's stated goal ("never need to search Google
again") depends on.

## Where is it used?

Every time you encounter a command you don't fully remember the syntax for — which, realistically,
is often, even for experienced engineers. The habit of reaching for `man`/`--help`/`tldr` instead
of a web search is what separates efficient terminal work from constantly context-switching to a
browser.

## How it works

> 📊 Diagram: a decision flowchart — "Need a quick example?" → tldr. "Need the full spec (all
> flags, exit codes, edge cases)?" → man. "Just need a fast flag reminder?" → `--help`.

- **`man command`** — opens the full manual page: description, every option, exit status,
  sometimes examples, related commands ("SEE ALSO"). Organized into numbered sections (Section 1
  = user commands, Section 5 = file formats, Section 8 = system administration commands — the
  same command name can exist in multiple sections, e.g. `man 5 passwd` vs `man 1 passwd`).
- **`command --help`** — a much shorter, built-in summary most commands support directly; faster
  than `man` when you just need a flag reminder.
- **`tldr command`** — not built into Linux; a separately-installed community tool that shows
  common practical examples instead of the full formal specification. Excellent for "how do I
  actually use this" when `man`'s formal language is more than you need.

## Real-world example

An engineer forgets the exact flag combination for `tar` to extract a `.tar.gz` file. Instead of
searching the web (and landing on an outdated blog post), they run `tldr tar` for a quick,
practical example, or `man tar` if they need to understand a less common flag `tldr` doesn't
cover. Either path stays inside the terminal, in flow, with zero context switch.

## Commands

- [`man`](../../commands/man.md) — the canonical reference tool

## Production example

```
$ man chmod
CHMOD(1)                    User Commands                    CHMOD(1)

NAME
       chmod - change file mode bits

SYNOPSIS
       chmod [OPTION]... MODE[,MODE]... FILE...
       ...
```

```
$ chmod --help
Usage: chmod [OPTION]... MODE[,MODE]... FILE...
  or:  chmod [OPTION]... OCTAL-MODE FILE...
...
```

Same command, two different depths of help — `man` for the full picture, `--help` for a fast
reminder.

## Do / Don't

| Do | Don't |
|---|---|
| Try `man`/`--help`/`tldr` before searching the web | Default to a web search for basic command syntax |
| Use `man -k keyword` to search for a command by topic | Give up when you don't remember a command's exact name |
| Use `tldr` for quick practical examples | Rely only on `tldr` when you need the full, authoritative spec |

## Common mistakes

- Reaching for a web search before trying `man`/`--help`/`tldr` — slower, and often surfaces
  outdated or distro-mismatched information.
- Not knowing `man -k keyword` exists for finding a command by topic when you don't remember its
  name.
- Treating `tldr` as a full replacement for `man` — it's deliberately incomplete, optimized for
  common cases, not edge cases.

## Best practices

- Build the reflex: unfamiliar command or flag → `man`/`--help`/`tldr` first, web search only as
  a last resort.
- Install `tldr` early — it dramatically speeds up daily terminal work once installed (package
  manager install covered in [Module 14](../14-package-managers/index.md)).
- Use `man -k` when you know what you want to do but not the command name for it.

## Exercises

1. Run `man ls` and find the description of the `-h` flag inside it.
2. Run `ls --help` and compare how much shorter it is than the man page.
3. If you have `tldr` installed, run `tldr tar` and compare its style to `man tar`.

## Quiz

**Q: What's the difference between `man command` and `command --help`?**
<details><summary>Show answer</summary>
`man` opens the full manual page with complete detail; `--help` is a much shorter, faster,
built-in summary most commands support directly.
</details>

**Q: Is `tldr` a built-in Linux tool?**
<details><summary>Show answer</summary>
No — it's a separately-installed, community-maintained tool. `man` and `--help` are what's
available by default.
</details>

## Interview questions

- How would you find documentation for a command you're unfamiliar with, without leaving the
  terminal? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `man` = full reference, `--help` = quick flag summary, `tldr` = practical examples — three
  different depths for three different needs.
- Building the "check in-terminal help first" reflex is what makes Linux fluency compound over
  time instead of requiring constant lookup elsewhere.
- `man -k keyword` finds a command by topic when you don't remember its name.

## Further Reading

- [tldr-pages project](https://tldr.sh)

## Related topics

- [Keyboard Shortcuts and History](keyboard-shortcuts-and-history.md)
