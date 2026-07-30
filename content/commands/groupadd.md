---
title: "groupadd — Create a New Group"
description: "Create a new group, optionally with a specific GID instead of the next available one."
relatedConcepts: ["09-groups/groupadd-groupmod-groupdel"]
relatedCommands: ["groupmod", "groupdel"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["groupadd command", "create group linux", "groupadd -g gid"]
canonicalUrl: "/commands/groupadd"
---

# groupadd

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `groupadd name` creates a new group with the next available GID. Use `-g GID` only
> when a specific GID is genuinely required (e.g. matching a GID across multiple servers).

## Purpose

`groupadd` creates a new group entry in `/etc/group` — see
[groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md).

## Syntax

```
groupadd [OPTIONS] GROUPNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `GROUPNAME` | The new group's name | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-g GID` | `--gid` | Use a specific GID instead of the next available one |
| `-r` | | Create a system group (low GID range) |

## Examples

```
$ sudo groupadd developers
```
Create a group with the next available GID.

```
$ sudo groupadd -g 5000 shared-tools
```
Create a group with a specific GID.

## Expected Output

`groupadd` produces no output on success.

## Exit Status

`0` on success, non-zero if the group already exists or the GID is already in use.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `groupadd: group 'name' already exists` | Naming collision | Choose a different name or confirm the existing group is the right one |
| `groupadd: GID '5000' already exists` | Specified GID is already taken | Choose a different GID or omit `-g` for automatic assignment |

## Security Considerations

None specific — group creation alone grants no access; access is granted separately via file
permissions or sudoers entries referencing the group.

## Performance Considerations

Not applicable.

## Production Usage

Consistent GIDs across multiple servers (using `-g` to match a specific number) matters when
files are shared over a network filesystem (NFS) between servers — group ownership is stored as a
numeric GID, so mismatched GIDs across servers break expected access even with matching group
names.

## Related Commands

- [`groupmod`](groupmod.md), [`groupdel`](groupdel.md)

## Related Concepts

- [groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md)
