---
title: "cat — Concatenate and Display Files"
description: "Dump a file's full content to the terminal, or concatenate multiple files together."
relatedConcepts: ["06-files/viewing-files"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["cat command linux", "concatenate files linux", "cat multiple files"]
canonicalUrl: "/commands/cat"
---

# cat

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `cat file` prints a file's entire content at once. `cat file1 file2 > combined`
> concatenates multiple files together — the feature the name actually comes from.

## Purpose

`cat` ("concatenate") prints one or more files' content to standard output, or combines them
together — see [Viewing Files](../docs/06-files/viewing-files.md) for when to reach for it versus
`less`.

## Syntax

```
cat [OPTIONS] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files to display/concatenate | No (reads stdin if omitted) |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-n` | `--number` | Number all output lines |
| `-A` | `--show-all` | Show non-printing characters (useful for spotting hidden characters) |
| `-s` | `--squeeze-blank` | Collapse multiple blank lines into one |

## Examples

```
$ cat file.txt
```
Print a file's content.

```
$ cat file1.txt file2.txt > combined.txt
```
Concatenate two files into a new one.

```
$ cat -n script.sh
```
Print with line numbers — useful when discussing a specific line.

## Expected Output

```
$ cat -n short.txt
     1  first line
     2  second line
```

## Exit Status

`0` on success, `1` if a specified file doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `cat: file: No such file or directory` | Typo or wrong path | Verify with `ls`/tab completion |
| Terminal flooded with output | Used `cat` on a very large file | Use `less` instead for large files |

## Security Considerations

`cat -A` can be used to spot hidden or unusual characters in a file (e.g. suspicious control
characters) — a minor but real diagnostic use during security investigation.

## Performance Considerations

`cat` reads the entire file before/while printing — fine for small-to-moderate files, a poor
choice for anything large enough to flood a terminal (see
[Viewing Files](../docs/06-files/viewing-files.md)).

## Production Usage

`cat file1 file2 file3 > merged.log` is a quick, common way to combine several log files (e.g.
rotated logs) into one before searching across all of them.

## Related Commands

None documented yet — `less`, `head`, `tail` are covered in
[Viewing Files](../docs/06-files/viewing-files.md) but don't have separate canonical pages yet.

## Related Concepts

- [Viewing Files](../docs/06-files/viewing-files.md)
