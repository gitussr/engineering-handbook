---
title: "groupdel — Delete a Group"
description: "Remove a group, which fails if it's still any user's primary group and never modifies member accounts."
relatedConcepts: ["09-groups/groupadd-groupmod-groupdel"]
relatedCommands: ["groupadd", "groupmod"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["groupdel command", "delete group linux", "groupdel fails primary group"]
canonicalUrl: "/commands/groupdel"
---

# groupdel

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `groupdel name` removes a group — but fails if it's still someone's primary group.
> It never modifies or deletes the user accounts that were members.

## Purpose

`groupdel` removes a group entry from `/etc/group` — see
[groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md).

## Syntax

```
groupdel GROUPNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `GROUPNAME` | The group to remove | Yes |

## Options

`groupdel` has no commonly used flags beyond the group name itself — notably, no `-r`-style
option, since deleting a group never touches member accounts or files.

## Examples

```
$ sudo groupdel old-project-group
```
Remove a group.

## Expected Output

`groupdel` produces no output on success.

## Exit Status

`0` on success, non-zero if the group doesn't exist or is still a user's primary group.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `groupdel: cannot remove the primary group of user 'name'` | The group is still someone's primary group | Change that user's primary group first with `usermod -g` |
| `groupdel: group 'name' does not exist` | Typo or already removed | Verify with `getent group` |

## Security Considerations

Deleting a group doesn't remove or reassign file ownership referencing its GID — files simply end
up with a numeric, unmapped group owner, which can look confusing in `ls -l` output (a number
instead of a name) until investigated.

## Performance Considerations

Not applicable.

## Production Usage

Removing stale, no-longer-needed groups is part of the same account-hygiene discipline covered in
[Module 08's hardening topic](../docs/08-users/user-account-security-hardening.md) — unused
groups with lingering file permissions are exactly the kind of stale access an audit should catch.

## Related Commands

- [`groupadd`](groupadd.md), [`groupmod`](groupmod.md)

## Related Concepts

- [groupadd, groupmod, groupdel](../docs/09-groups/groupadd-groupmod-groupdel.md)
