---
title: "pwd — Print Working Directory"
description: "Print the full absolute path of your current working directory."
relatedConcepts: ["03-terminal/navigating-the-terminal", "03-terminal/pwd-ls-cd"]
relatedCommands: ["ls", "cd"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["pwd command", "print working directory", "current directory linux"]
canonicalUrl: "/commands/pwd"
---

# pwd

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** Prints the full absolute path of your current working directory. No arguments
> needed for the common case.

## Purpose

`pwd` prints the absolute path of the directory you're currently in. It's the answer to "where
am I right now" — the first question in the navigation loop covered in
[pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md).

## Syntax

```
pwd [OPTIONS]
```

## Arguments

None — `pwd` takes no positional arguments.

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-L` | `--logical` | Use the logical path, following symlinks as-shown (default behavior) |
| `-P` | `--physical` | Resolve symlinks and print the actual physical path |

## Examples

```
$ pwd
```
Prints the current directory's absolute path.

```
$ pwd -P
```
Prints the physical path, resolving any symlinks in the current path.

## Expected Output

```
$ pwd
/home/deploy/projects/app
```

## Exit Status

`0` on success. `pwd` essentially cannot fail under normal circumstances — a non-zero exit
generally means a severely broken shell environment, not a `pwd` issue.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `pwd` prints an unexpected path after `cd` into a symlinked directory | Default logical mode shows the symlink path, not the resolved physical path | Use `pwd -P` for the resolved physical path |

## Security Considerations

None specific to `pwd` — it only reads and prints information already visible to the current
user.

## Performance Considerations

Negligible — `pwd` is effectively instantaneous regardless of filesystem size or depth.

## Production Usage

Used constantly at the start of debugging sessions and inside scripts to confirm the execution
context before running path-dependent commands — a common defensive pattern in shell scripts is
`echo "Running from: $(pwd)"` at the top of a script for logging purposes.

## Related Commands

- [`ls`](ls.md) — see what's in the current directory
- [`cd`](cd.md) — move to a different directory

## Related Concepts

- [Navigating the Terminal](../docs/03-terminal/navigating-the-terminal.md)
- [pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md)
