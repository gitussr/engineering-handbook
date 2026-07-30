---
title: "usermod — Modify an Existing User Account"
description: "Change an existing account's shell, group memberships, or lock status — including the critical -aG append vs -G replace distinction."
relatedConcepts: ["08-users/useradd-usermod-userdel"]
relatedCommands: ["useradd", "userdel"]
careerRelevance: ["devops", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["usermod -aG", "usermod command", "add user to group linux", "lock user account linux"]
canonicalUrl: "/commands/usermod"
---

# usermod

🟢 Must Know · Relevant for: DevOps · Linux Administrator

> **TL;DR:** `usermod -aG group username` adds a group membership without disturbing existing
> ones — always include `-a`. Without it, `-G` replaces all supplementary groups instead of
> appending to them.

## Purpose

`usermod` modifies an existing user account's properties — see
[useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md), including the important
`-aG` vs. `-G` distinction.

## Syntax

```
usermod [OPTIONS] USERNAME
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The account to modify | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-aG GROUPS` | `--append --groups` | Add to supplementary groups, preserving existing ones |
| `-G GROUPS` | `--groups` (no `-a`) | **Replace** all supplementary groups with the specified list |
| `-s SHELL` | `--shell` | Change the login shell |
| `-L` | `--lock` | Lock the account (disable password login) |
| `-U` | `--unlock` | Unlock a previously locked account |

## Examples

```
$ sudo usermod -aG developers newhire
```
Add to a group, preserving existing group memberships.

```
$ sudo usermod -s /bin/zsh deploy
```
Change the login shell.

```
$ sudo usermod -L suspicious-account
```
Lock an account, disabling password login without deleting it.

## Expected Output

`usermod` produces no output on success.

## Exit Status

`0` on success, non-zero if the user doesn't exist or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| User unexpectedly lost group memberships | Used `-G` without `-a` | Always use `-aG` when adding a group, never bare `-G` |
| `usermod: user 'name' does not exist` | Typo or account doesn't exist | Verify with `id username` first |
| Changes don't apply to already-open sessions | Group/shell changes apply to new logins, not the current session | The user needs to log out and back in |

## Security Considerations

`usermod -L` (lock) is a fast, reversible way to disable an account during an incident or
offboarding without immediately deleting it — often preferable to `userdel` when investigation or
audit trail preservation matters.

## Performance Considerations

Not applicable.

## Production Usage

`usermod -aG` is the standard way to grant a user access to a new resource represented by a group
(e.g. `docker`, `developers`) — the `-a` flag is worth calling out explicitly in any onboarding
runbook, since forgetting it is a real, damaging, and easy mistake.

## Related Commands

- [`useradd`](useradd.md) — create a new account
- [`userdel`](userdel.md) — remove an account

## Related Concepts

- [useradd, usermod, userdel](../docs/08-users/useradd-usermod-userdel.md)
