---
title: "userdel — Delete a User Account"
description: "Remove a user account, optionally with its home directory and mail spool."
relatedConcepts: ["08-users/useradd-usermod-userdel"]
relatedCommands: ["useradd", "usermod"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["userdel command", "userdel -r", "delete user and home directory linux"]
canonicalUrl: "/commands/userdel"
---

# userdel

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `userdel username` removes the account but leaves the home directory behind.
> `userdel -r username` removes the account and its home directory/mail spool together.

## Purpose

`userdel` removes a user account from `/etc/passwd`/`/etc/shadow` — see
[useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md).

## Syntax

```
userdel [OPTIONS] USERNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The account to remove | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-r` | `--remove` | Also remove the home directory and mail spool |
| `-f` | `--force` | Force removal even if the user is currently logged in (use with caution) |

## Examples

```
$ sudo userdel formeruser
```
Remove the account, home directory left behind.

```
$ sudo userdel -r formeruser
```
Remove the account and its home directory together.

## Expected Output

`userdel` produces no output on success.

## Exit Status

`0` on success, non-zero if the user doesn't exist, is currently logged in (without `-f`), or the
operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `userdel: user 'name' is currently used by process N` | The user has active processes running | Investigate/terminate those processes, or use `-f` if forcing is genuinely appropriate |
| Orphaned home directory left behind | Ran `userdel` without `-r` | Remove the leftover directory manually if it's genuinely no longer needed |
| `userdel: user 'name' does not exist` | Typo or already removed | Verify with `id username` first |

## Security Considerations

Files still owned by a deleted user's now-unassigned UID don't disappear — they become owned by
a numeric UID with no matching username, which can be confusing during a later audit; consider
reassigning ownership of important files before deleting an account.

## Performance Considerations

Not applicable.

## Production Usage

Offboarding runbooks typically prefer `usermod -L` (lock, see [`usermod`](usermod.md)) over
immediate `userdel` for a retention period, preserving the account and its data for audit
purposes before final deletion.

## Related Commands

- [`useradd`](useradd.md) — create a new account
- [`usermod`](usermod.md) — modify an existing account, including locking it

## Related Concepts

- [useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md)
