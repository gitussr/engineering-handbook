---
title: "chown — Change File Owner and Group"
description: "Change a file's owning user and/or group in one command."
relatedConcepts: ["07-permissions/chown-chgrp"]
relatedCommands: ["chgrp", "chmod"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["chown command", "chown recursive", "chown user group syntax"]
canonicalUrl: "/commands/chown"
---

# chown

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `chown user:group file` sets both owner and group at once. Requires root/sudo unless
> you're already root — a regular user can't give away files they own.

## Purpose

`chown` changes a file's owning user and, optionally, its owning group in the same command — see
[chown and chgrp](../docs/07-permissions/chown-chgrp.md).

## Syntax

```
chown [OPTIONS] OWNER[:GROUP] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `OWNER` | New owning user | Yes |
| `:GROUP` | New owning group (optional, appended with `:`) | No |
| `FILE` | One or more files/directories | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-R` | `--recursive` | Apply to a directory and everything inside it |
| `-v` | `--verbose` | Print each change made |

## Examples

```
$ sudo chown appuser app.py
```
Change owner only.

```
$ sudo chown appuser:appgroup app.py
```
Change owner and group together.

```
$ sudo chown -R appuser:appuser /opt/app
```
Recursively change ownership of an entire directory tree.

## Expected Output

`chown` produces no output by default; `-v`:

```
$ sudo chown -v appuser app.py
changed ownership of 'app.py' from root to appuser
```

## Exit Status

`0` on success, `1` if the operation isn't permitted or the target doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `chown: changing ownership of 'file': Operation not permitted` | Insufficient privileges | Use `sudo` |
| `chown: invalid user: 'name'` | The specified user doesn't exist | Verify the username with `id` or check `/etc/passwd` (Module 08) |

## Security Considerations

Because giving away file ownership is privileged, `chown` requiring root/sudo is a deliberate
boundary preventing a compromised or careless account from reassigning files to escape its own
permission constraints.

## Performance Considerations

Negligible, except `-R` on a very large tree, similar to `chmod -R`.

## Production Usage

Fixing ownership after extracting an archive as root (so the application's actual runtime user
can access its own files) is one of the most common `chown` uses in deployment automation — see
the example in [chown and chgrp](../docs/07-permissions/chown-chgrp.md).

## Related Commands

- [`chgrp`](chgrp.md) — change group only
- [`chmod`](chmod.md) — change permission bits, not ownership

## Related Concepts

- [chown and chgrp](../docs/07-permissions/chown-chgrp.md)
