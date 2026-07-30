---
title: "passwd — Change a User's Password"
description: "Change your own password, set another user's password as root, or lock/unlock an account."
relatedConcepts: ["08-users/passwd-command", "07-permissions/suid-sgid-sticky-bit"]
relatedCommands: ["useradd", "usermod"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["passwd command", "change password linux", "passwd -l", "passwd -e"]
canonicalUrl: "/commands/passwd"
---

# passwd

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `passwd` changes your own password. `sudo passwd username` sets someone else's.
> `-l`/`-u` lock/unlock, `-e` forces a change at next login.

## Purpose

`passwd` updates a user's password hash in `/etc/shadow` — see
[The passwd Command](../docs/08-users/passwd-command.md) for full context, including why a
regular user can run it at all despite `/etc/shadow`'s restrictive permissions.

## Syntax

```
passwd [OPTIONS] [USERNAME]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The account to change (root/sudo only for other users) | No (defaults to yourself) |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-l` | `--lock` | Lock the account (disable password login) |
| `-u` | `--unlock` | Unlock a previously locked account |
| `-e` | `--expire` | Force a password change at next login |
| `-S` | `--status` | Show the account's password status |

## Examples

```
$ passwd
```
Change your own password (prompts for current password first).

```
$ sudo passwd newhire
```
Set another user's password.

```
$ sudo passwd -l suspicious-account
```
Lock an account without deleting it.

## Expected Output

```
$ passwd
Changing password for user deploy.
Current password:
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

## Exit Status

`0` on success, non-zero if authentication fails or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `passwd: Authentication token manipulation error` | Password policy rejected the new password (too short, too similar, etc.) | Choose a password meeting the system's policy requirements |
| `passwd: Permission denied` when setting another user's password | Not run as root/sudo | Use `sudo` |

## Security Considerations

`passwd`'s SUID bit (see
[Module 07: SUID, SGID, Sticky Bit](../docs/07-permissions/suid-sgid-sticky-bit.md)) is precisely
what makes self-service password changes possible without broader root access — a canonical,
correct use of SUID rather than a risk in itself.

## Performance Considerations

Not applicable.

## Production Usage

`passwd -e` immediately after an admin sets a new hire's initial password is standard practice —
it ensures the admin-known password is never the one actually in long-term use.

## Related Commands

- [`useradd`](useradd.md), [`usermod`](usermod.md)

## Related Concepts

- [The passwd Command](../docs/08-users/passwd-command.md)
