---
title: "chgrp — Change File Group"
description: "Change a file's owning group without touching its owner."
relatedConcepts: ["07-permissions/chown-chgrp"]
relatedCommands: ["chown", "chmod"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["chgrp command", "change group linux", "chgrp recursive"]
canonicalUrl: "/commands/chgrp"
---

# chgrp

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `chgrp group file` changes just the owning group, leaving the owner untouched —
> `chown :group file` does the same thing if you'd rather use one command for both owner and
> group changes.

## Purpose

`chgrp` changes a file's owning group specifically — see
[chown and chgrp](../docs/07-permissions/chown-chgrp.md) for how it relates to `chown`.

## Syntax

```
chgrp [OPTIONS] GROUP FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `GROUP` | New owning group | Yes |
| `FILE` | One or more files/directories | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-R` | `--recursive` | Apply to a directory and everything inside it |
| `-v` | `--verbose` | Print each change made |

## Examples

```
$ chgrp developers shared-file.txt
```
Change the owning group.

```
$ chgrp -R developers /opt/shared/
```
Recursively change group ownership for an entire directory tree.

## Expected Output

`chgrp` produces no output by default; `-v`:

```
$ chgrp -v developers shared-file.txt
changed group of 'shared-file.txt' from staff to developers
```

## Exit Status

`0` on success, `1` if not permitted or the group doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `chgrp: changing group of 'file': Operation not permitted` | Not the owner and not root (a regular user can only set the group to one they belong to) | Use `sudo`, or set to a group you're already a member of |
| `chgrp: invalid group: 'name'` | The group doesn't exist | Verify with `getent group` or check `/etc/group` (Module 09) |

## Security Considerations

Unlike changing owner, a regular (non-root) file owner *can* change a file's group — but only to
a group they themselves belong to, preventing them from granting access to a group they have no
membership in.

## Performance Considerations

Negligible, except `-R` on very large directory trees.

## Production Usage

Setting up a shared project directory's group ownership (`chgrp -R developers /opt/project`)
paired with appropriate group permission bits (Module 07's `chmod` coverage) is the standard way
to grant a whole team consistent access without touching "other" permissions.

## Related Commands

- [`chown`](chown.md) — change owner (and optionally group)
- [`chmod`](chmod.md) — change permission bits, not ownership

## Related Concepts

- [chown and chgrp](../docs/07-permissions/chown-chgrp.md)
