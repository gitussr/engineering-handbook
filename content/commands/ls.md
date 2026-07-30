---
title: "ls — List Directory Contents"
description: "List files and directories, with options for hidden files, long format, human-readable sizes, and sorting."
relatedConcepts: ["03-terminal/navigating-the-terminal", "03-terminal/pwd-ls-cd"]
relatedCommands: ["pwd", "cd", "find"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
compatibility: [{"context": "GNU coreutils (most Linux distros)", "note": "This page documents GNU ls flags. macOS/BSD ls supports a different, smaller flag set."}]
updatedAt: "2026-07-25"
keywords: ["ls command", "list directory linux", "ls -la", "hidden files linux"]
canonicalUrl: "/commands/ls"
---

# ls

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** Lists directory contents. `ls -la` (all files including hidden, long format with
> permissions/owner/size) is the professional default — bare `ls` is rarely enough for real work.

## Purpose

`ls` lists the files and directories inside a given directory (or the current one, by default).
It's the answer to "what's here" in the navigation loop covered in
[pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md).

## Syntax

```
ls [OPTIONS] [PATH...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` | Directory (or file) to list. Defaults to the current directory if omitted. | No |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-l` | `--format=long` | Long format: permissions, owner, group, size, modified date |
| `-a` | `--all` | Show hidden files (names starting with `.`) |
| `-h` | `--human-readable` | Human-readable sizes (K, M, G) — used with `-l` |
| `-t` | | Sort by modification time, newest first |
| `-R` | `--recursive` | List subdirectories recursively |
| `-d` | `--directory` | List a directory itself, not its contents |

## Examples

```
$ ls
```
Lists names in the current directory.

```
$ ls -la
```
Lists everything, including hidden files, in long format — the day-to-day default.

```
$ ls -lh /var/log
```
Long format with human-readable file sizes, for a specific directory.

```
$ ls -lt
```
Long format, newest-modified files first — useful when hunting for a recently changed file.

## Expected Output

```
$ ls -la
total 24
drwxr-xr-x 2 deploy deploy 4096 Jul 25 09:12 .
drwxr-xr-x 5 deploy deploy 4096 Jul 25 09:10 ..
-rw-r--r-- 1 deploy deploy 1820 Jul 25 09:12 app.log
-rw-r--r-- 1 deploy deploy   45 Jul 20 14:01 .env
```

Reading this: permission string, owner, group, size in bytes, modified date, name — the `.env`
file only appears because `-a` was used.

## Exit Status

`0` on success, `2` if a specified path doesn't exist or can't be accessed.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `ls: cannot access 'path': No such file or directory` | The path doesn't exist or has a typo | Verify the path with `pwd` and tab completion |
| `ls: cannot open directory 'path': Permission denied` | No read permission on the directory | Check ownership/permissions (full detail in Module 07) |
| Expected files missing from output | Forgot `-a` — hidden files are excluded by default | Add `-a` to show dotfiles |

## Security Considerations

`ls -l` reveals ownership and permission information — useful for legitimately auditing a
system, but also information an attacker with shell access would use for reconnaissance. Not a
reason to avoid it, just context for why permission hardening (Module 07) matters even when
`ls` output itself is harmless.

## Performance Considerations

`ls -R` on a very large directory tree (e.g. `/`) can take a long time and produce enormous
output — for large-scale searching, [`find`](find.md) is the better tool (covered fully in
Module 06).

## Production Usage

`ls -lt` is a fast way to spot the most recently modified file in a directory during an
incident — "what changed most recently here" is often the first useful question when debugging.

## Related Commands

- [`pwd`](pwd.md) — confirm your current location before listing
- [`cd`](cd.md) — move into a directory to inspect it further
- [`find`](find.md) — search recursively when `ls -R` isn't the right tool (Module 06)

## Related Concepts

- [Navigating the Terminal](../docs/03-terminal/navigating-the-terminal.md)
- [pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md)
