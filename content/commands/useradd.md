---
title: "useradd — Create a New User Account"
description: "Create a new user account, optionally with a home directory, login shell, and initial group memberships."
relatedConcepts: ["08-users/useradd-usermod-userdel"]
relatedCommands: ["usermod", "userdel", "passwd"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["useradd command", "useradd -m -s", "create user linux command"]
canonicalUrl: "/commands/useradd"
---

# useradd

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `useradd -m -s /bin/bash username` creates a user with a home directory and bash
> shell — the form you'll use almost every time. Without `-m`, no home directory is created on
> many distros.

## Purpose

`useradd` creates a new user account, updating `/etc/passwd` and `/etc/shadow` safely — see
[useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md).

## Syntax

```
useradd [OPTIONS] USERNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The new account's username | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-m` | `--create-home` | Create a home directory |
| `-s SHELL` | `--shell` | Set the login shell |
| `-G GROUPS` | `--groups` | Set supplementary group memberships (comma-separated) |
| `-c COMMENT` | `--comment` | Set the GECOS/comment field (e.g. full name) |
| `-r` | | Create a system account (low UID, typically no login) |

## Examples

```
$ sudo useradd -m -s /bin/bash deploy
```
Create a user with a home directory and bash shell.

```
$ sudo useradd -m -s /bin/bash -G developers,docker newhire
```
Create a user with initial supplementary group memberships.

```
$ sudo useradd -r -s /usr/sbin/nologin serviceacct
```
Create a system/service account with no interactive login shell.

## Expected Output

`useradd` produces no output on success.

## Exit Status

`0` on success, non-zero if the username already exists or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `useradd: Permission denied` | Not run as root/sudo | Use `sudo` |
| `useradd: user 'name' already exists` | Username collision | Choose a different username or check if the account already exists |
| New user has no home directory | Forgot `-m` | Re-create with `-m`, or create the home directory manually and fix ownership |

## Security Considerations

New accounts are created **without** a usable password by default (locked) until
[`passwd`](passwd.md) is run — verify this is the intended state rather than assuming the account
is immediately loginable.

## Performance Considerations

Not applicable.

## Production Usage

Provisioning automation almost always wraps `useradd -m -s /bin/bash -G ...` into a single
scripted step as part of onboarding, ensuring consistent home directory, shell, and initial group
setup across every new account rather than relying on manual, inconsistent execution.

## Related Commands

- [`usermod`](usermod.md) — modify an existing account
- [`userdel`](userdel.md) — remove an account
- [`passwd`](passwd.md) — set the account's password

## Related Concepts

- [useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md)
