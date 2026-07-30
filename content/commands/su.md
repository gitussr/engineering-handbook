---
title: "su — Switch User"
description: "Start a new session as another user, requiring that user's own password."
relatedConcepts: ["08-users/su-vs-sudo"]
relatedCommands: ["sudo"]
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["su command linux", "su dash login shell", "switch user linux"]
canonicalUrl: "/commands/su"
---

# su

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** `su - username` starts a full login session as that user (with the dash — always
> use it). `su username` without the dash keeps your current environment, which can cause subtle
> bugs.

## Purpose

`su` switches to another user's session, requiring that user's password — see
[su vs sudo](../docs/08-users/su-vs-sudo.md) for when to prefer this over `sudo`.

## Syntax

```
su [OPTIONS] [-] [USERNAME]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `USERNAME` | The account to switch to | No (defaults to root) |

## Options

| Flag | Meaning |
|---|---|
| `-`, `-l`, `--login` | Start a full login shell with the target user's environment |
| `-c COMMAND` | Run a single command as the target user, then return |

## Examples

```
$ su
```
Switch to root (prompts for root's password).

```
$ su - deploy
```
Full login session as `deploy`, with a dash for a clean environment.

```
$ su -c "systemctl restart nginx"
```
Run a single command as root, then return.

## Expected Output

```
$ su - deploy
Password:
deploy@server:~$
```

## Exit Status

`0` on success, `1` on authentication failure.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `su: Authentication failure` | Wrong password for the target account | Verify the correct password for that specific account |
| Confusing environment/path issues after switching | Forgot the dash (`-`) | Use `su -` for a clean, correct environment |

## Security Considerations

Requires knowing the target account's actual password — on a team-managed system, this generally
means sharing a root password, which eliminates individual accountability. See
[su vs sudo](../docs/08-users/su-vs-sudo.md) for why `sudo` is generally preferred instead.

## Performance Considerations

Not applicable.

## Production Usage

`su - serviceaccount` is still common for testing "as" a service account during debugging (e.g.
confirming a service account can actually read a file it needs), even on systems that otherwise
default to `sudo` for administrative work.

## Related Commands

- [`sudo`](sudo.md) — the generally preferred alternative for privileged commands

## Related Concepts

- [su vs sudo](../docs/08-users/su-vs-sudo.md)
