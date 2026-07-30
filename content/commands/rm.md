---
title: "rm — Remove Files and Directories"
description: "Permanently delete files and directories — no trash bin, no undo. The most dangerous command in everyday Linux use."
relatedConcepts: ["06-files/creating-copying-moving-removing-files"]
relatedCommands: ["cp", "mv", "touch"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["rm command linux", "rm -rf danger", "delete file linux permanently", "rm -i"]
canonicalUrl: "/commands/rm"
---

# rm

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `rm file` deletes permanently — no trash bin. `rm -rf directory` deletes recursively
> and forcefully with no confirmation. Read every `rm -rf` command back before running it; a
> typo here is the single most consequential mistake covered in this module.

## Purpose

`rm` removes (deletes) files and, with `-r`, directories — permanently, with no built-in recovery
— the deletion component of
[Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md).

## Syntax

```
rm [OPTIONS] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files/directories to delete | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-r`, `-R` | `--recursive` | Delete directories and their contents recursively |
| `-f` | `--force` | Never prompt, ignore nonexistent files |
| `-i` | `--interactive` | Prompt before every removal |
| `-v` | `--verbose` | Print each file as it's removed |

## Examples

```
$ rm old-file.txt
```
Delete a single file.

```
$ rm -r old-directory/
```
Delete a directory and everything inside it.

```
$ rm -i *.log
```
Delete matching files, confirming each one individually.

## Expected Output

`rm` produces no output by default (unless `-v` is used, or a prompt from `-i`).

## Exit Status

`0` on success, non-zero if a target doesn't exist (without `-f`) or can't be removed
(permissions).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `rm: cannot remove 'dir': Is a directory` | Tried to remove a directory without `-r` | Add `-r` (and confirm that's really intended) |
| `rm: cannot remove 'file': Permission denied` | No write permission on the containing directory | Check directory permissions (Module 07) — note file's own permissions aren't what matters here |
| Entire unintended tree deleted | Typo, stray space, or wrong variable in a scripted `rm -rf` | No fix after the fact — prevention (careful review, backups) is the only real mitigation |

## Security Considerations

`rm -rf` run with elevated privileges against a wrong or attacker-influenced path is a real,
severe risk — never construct an `rm -rf` path from unsanitized input (user input, an unchecked
variable) in a script.

## Performance Considerations

Deleting an extremely large directory tree can take a noticeable amount of time — not usually a
concern, but relevant when a script's timing assumptions depend on cleanup finishing quickly.

## Production Usage

Production teams commonly wrap or restrict raw `rm -rf` in scripts (e.g. requiring an explicit
confirmation flag, or preferring `mv target /tmp/trash-$(date +%s)` over immediate deletion) —
not because `rm` is broken, but because its total lack of an undo path makes a small mistake
disproportionately costly compared to almost any other command in this module.

## Related Commands

- [`cp`](cp.md), [`mv`](mv.md), [`touch`](touch.md)

## Related Concepts

- [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)
