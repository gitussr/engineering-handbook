---
title: "groups — List a User's Group Memberships"
description: "Show all groups a user belongs to, primary and secondary combined."
relatedConcepts: ["09-groups/adding-users-to-groups"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["groups command linux", "list user groups", "groups username"]
canonicalUrl: "/commands/groups"
---

# groups

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `groups username` lists every group that user belongs to, primary and secondary
> combined — the fastest single check for "what does this account have access to."

## Purpose

`groups` displays the group memberships of a user — see
[Adding Users to Groups](../docs/09-groups/adding-users-to-groups.md) for how this fits alongside
`id` and `getent group`.

## Syntax

```
groups [USERNAME]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The account to check | No (defaults to yourself) |

## Options

`groups` takes no commonly used flags beyond the optional username.

## Examples

```
$ groups
```
List your own group memberships.

```
$ groups deploy
```
List a specific user's group memberships.

## Expected Output

```
$ groups deploy
deploy : deploy sudo docker
```

The first group listed (`deploy`, matching the username in this example) is typically the
primary group; the rest are secondary memberships — though `id` is more explicit about which is
which if that distinction matters for what you're checking.

## Exit Status

`0` on success, non-zero if the user doesn't exist.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `groups: 'name': no such user` | Typo or the account doesn't exist | Verify with `id` or check `/etc/passwd` |

## Security Considerations

A fast way to audit what a specific account currently has access to — useful during incident
response to quickly understand the blast radius of a potentially compromised account.

## Performance Considerations

Negligible.

## Production Usage

`groups username` is often the very first command run when investigating "why can/can't this
account do X" — faster to read than `id`'s more verbose UID/GID output when group membership
alone is the question.

## Related Commands

None documented yet — `getent` is referenced in
[Adding Users to Groups](../docs/09-groups/adding-users-to-groups.md) but doesn't have a separate
canonical page yet.

## Related Concepts

- [Adding Users to Groups](../docs/09-groups/adding-users-to-groups.md)
