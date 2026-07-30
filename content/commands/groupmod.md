---
title: "groupmod — Modify an Existing Group"
description: "Rename a group or change its GID, in place, preserving existing membership and permission grants."
relatedConcepts: ["09-groups/groupadd-groupmod-groupdel"]
relatedCommands: ["groupadd", "groupdel"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["groupmod command", "rename group linux", "groupmod -n"]
canonicalUrl: "/commands/groupmod"
---

# groupmod

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `groupmod -n newname oldname` renames a group in place, preserving its GID — always
> preferred over deleting and recreating a group to rename it.

## Purpose

`groupmod` modifies an existing group's name or GID — see
[groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md).

## Syntax

```
groupmod [OPTIONS] GROUPNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `GROUPNAME` | The existing group to modify | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-n NEWNAME` | `--new-name` | Rename the group |
| `-g GID` | `--gid` | Change the group's GID |

## Examples

```
$ sudo groupmod -n platform-team backend-team
```
Rename a group.

```
$ sudo groupmod -g 6000 developers
```
Change a group's GID (rarely needed; affects any files/permissions referencing the old GID).

## Expected Output

`groupmod` produces no output on success.

## Exit Status

`0` on success, non-zero if the group doesn't exist or the new name/GID is already in use.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `groupmod: group 'name' does not exist` | Typo or wrong group name | Verify with `getent group` or `/etc/group` |
| Files' permissions appear broken after a GID change | Files on disk still reference the old GID numerically | Reassign file group ownership with `chgrp` if a GID change was genuinely intended |

## Security Considerations

Changing a group's GID (not just its name) can silently orphan existing file permissions that
reference the old GID — a much bigger, riskier change than a simple rename and rarely actually
needed.

## Performance Considerations

Not applicable.

## Production Usage

`groupmod -n` is the standard, safe way to keep group names in sync with organizational changes
(a team renamed, a project renamed) without disturbing the underlying GID that permissions
actually depend on.

## Related Commands

- [`groupadd`](groupadd.md), [`groupdel`](groupdel.md)

## Related Concepts

- [groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md)
