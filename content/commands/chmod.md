---
title: "chmod — Change File Permissions"
description: "Set file and directory permissions using symbolic (u+x) or octal (755) notation, including recursive changes."
relatedConcepts: ["07-permissions/chmod-symbolic-and-octal", "07-permissions/rwx-explained"]
relatedCommands: ["chown", "chgrp"]
careerRelevance: ["devops", "linux-administrator", "backend", "cybersecurity"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["chmod command", "chmod 755", "chmod recursive", "chmod symbolic"]
canonicalUrl: "/commands/chmod"
---

# chmod

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Cybersecurity

> **TL;DR:** `chmod 755 file` sets exact permissions via octal. `chmod u+x file` adds one
> permission symbolically without disturbing the rest. `-R` applies recursively to a directory.

## Purpose

`chmod` changes a file or directory's permissions — see
[chmod: Symbolic and Octal](../docs/07-permissions/chmod-symbolic-and-octal.md) for full notation
coverage.

## Syntax

```
chmod [OPTIONS] MODE FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `MODE` | Octal (`755`) or symbolic (`u+x`) permission specification | Yes |
| `FILE` | One or more files/directories to change | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-R` | `--recursive` | Apply to a directory and everything inside it |
| `-v` | `--verbose` | Print each change made |
| `--reference=FILE` | | Copy another file's permissions instead of specifying a mode |

## Examples

```
$ chmod 755 deploy.sh
```
Set exact octal permissions.

```
$ chmod u+x deploy.sh
```
Add execute for the owner only, symbolically.

```
$ chmod -R 644 configs/
```
Recursively set every file under `configs/` to `644` (caution: this also affects directories,
which need `x` to be enterable — recursive chmod with a single octal value affects files and
directories identically, a common gotcha).

## Expected Output

`chmod` produces no output by default; `-v` shows each change:

```
$ chmod -v 644 config.yaml
mode of 'config.yaml' changed from 0600 (rw-------) to 0644 (rw-r--r--)
```

## Exit Status

`0` on success, `1` if any target doesn't exist or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `chmod: changing permissions of 'file': Operation not permitted` | Not the file's owner and not root | Only the owner or root can change a file's permissions |
| Recursive `chmod` broke directory access | Applied a file-appropriate octal value (e.g. `644`) to directories too, removing their needed `x` | Use `find` with `-type f`/`-type d` to apply different modes to files vs. directories, or use symbolic `X` (capital, context-sensitive execute) |

## Security Considerations

`chmod 777` (or any overly permissive recursive change) is a common, serious misconfiguration —
always verify the narrowest permission that actually satisfies the requirement, not the broadest
one that makes an error go away.

## Performance Considerations

`chmod -R` on a very large directory tree can take noticeable time; not usually a practical
concern, but worth knowing for scripting against huge filesystems.

## Production Usage

Deployment scripts commonly run `chmod +x` on freshly deployed scripts/binaries as a standard
step, since file permissions from a copy/extract operation don't always preserve the execute bit
depending on how the artifact was packaged.

## Related Commands

- [`chown`](chown.md), [`chgrp`](chgrp.md) — change ownership rather than permissions

## Related Concepts

- [chmod: Symbolic and Octal](../docs/07-permissions/chmod-symbolic-and-octal.md)
- [rwx Explained](../docs/07-permissions/rwx-explained.md)
