---
title: "man — Display Manual Pages"
description: "Open the full manual page for a command, including how to search by keyword and navigate multiple man page sections."
relatedConcepts: ["03-terminal/man-help-tldr"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend", "software-engineering"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["man command", "man pages", "man -k", "linux manual pages"]
canonicalUrl: "/commands/man"
---

# man

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Software Engineering

> **TL;DR:** `man command` opens the full manual page. `man -k keyword` searches for a command by
> topic when you don't remember its name. Press `q` to quit, `/word` to search inside a page.

## Purpose

`man` displays the manual page for a command, a configuration file format, or a system call —
the full, authoritative reference, organized into numbered sections.

## Syntax

```
man [SECTION] PAGE
man -k KEYWORD
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PAGE` | The command, file, or topic to look up | Yes |
| `SECTION` | Restrict to a specific man page section (1 = commands, 5 = file formats, 8 = admin commands) | No |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-k` | `--apropos` | Search man page names/descriptions by keyword instead of opening a specific page |
| `-f` | `--whatis` | Show a one-line description of a command |

## Examples

```
$ man ls
```
Opens the manual page for `ls`.

```
$ man 5 passwd
```
Opens the file-format manual page for `/etc/passwd` specifically (section 5), not the `passwd`
command page (section 1).

```
$ man -k partition
```
Searches all man page names/descriptions for "partition," useful when you don't remember a
command's exact name.

## Expected Output

```
$ man -k partition
fdisk (8)            - manipulate disk partition table
parted (8)            - a partition manipulation program
```

## Exit Status

`0` if the page was found and displayed, `1` if no manual entry exists for the given name.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No manual entry for {name}` | The command doesn't have a man page, or a typo in the name | Check spelling; try `man -k` with a related keyword instead |
| Opens the wrong page (e.g. file format instead of command) | Multiple man page sections share the same name | Specify the section explicitly, e.g. `man 1 passwd` vs `man 5 passwd` |

## Security Considerations

None — `man` only reads local documentation files.

## Performance Considerations

Negligible — man pages are small local text files.

## Production Usage

`man -k` is significantly underused — engineers who don't remember an exact command name often
reach for a web search when `man -k keyword` would answer it instantly, offline, without leaving
the terminal.

## Related Commands

None — `man` is a reference tool for every other command, not paired with a specific complementary
command.

## Related Concepts

- [man, --help, tldr](../docs/03-terminal/man-help-tldr.md)
