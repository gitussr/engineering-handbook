---
title: "sudo — Execute a Command as Another User"
description: "Run a single command with elevated privileges, authenticating with your own password, with every use logged."
relatedConcepts: ["08-users/su-vs-sudo", "08-users/sudoers-and-visudo"]
relatedCommands: ["su"]
careerRelevance: ["devops", "linux-administrator", "cybersecurity"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["sudo command", "sudo -i", "sudo -u", "sudo log location"]
canonicalUrl: "/commands/sudo"
---

# sudo

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cybersecurity

> **TL;DR:** `sudo command` runs one command as root, authenticating with your own password —
> logged every time. `sudo -u user command` runs as a specific non-root user. `sudo -i` starts a
> full root shell, closer in effect to `su`.

## Purpose

`sudo` executes a command with another user's (typically root's) privileges, authenticating with
the invoking user's own password — see [su vs sudo](../docs/08-users/su-vs-sudo.md) and
[The sudoers File and visudo](../docs/08-users/sudoers-and-visudo.md) for the permission model
controlling who can use it for what.

## Syntax

```
sudo [OPTIONS] COMMAND
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The command to run with elevated privileges | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-u USER` | `--user` | Run as a specific user instead of root |
| `-i` | | Start a full login shell as the target user (closer to `su`'s behavior) |
| `-l` | `--list` | List what commands the current user is permitted to run via sudo |
| `-k` | `--reset-timestamp` | Force the next `sudo` to re-prompt for a password |

## Examples

```
$ sudo systemctl restart nginx
```
Run a single command as root.

```
$ sudo -u postgres psql
```
Run as a specific non-root user.

```
$ sudo -l
```
Check what you're permitted to run with sudo.

## Expected Output

```
$ sudo systemctl restart nginx
[sudo] password for deploy:
```

No further output on success (the underlying command's own output follows, if any).

## Exit Status

`0` on success, `1` on authentication failure, and the underlying command's own exit status
otherwise.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `user is not in the sudoers file. This incident will be reported.` | The user isn't granted sudo access at all | An admin needs to add them via `visudo` |
| `Sorry, user is not allowed to execute COMMAND` | The user has sudo access, but not for that specific command | Check `sudo -l` for what's actually permitted |
| Password prompt reappears constantly | The sudo timestamp cache expired (default ~15 min) | Expected behavior — re-enter the password |

## Security Considerations

Every `sudo` invocation is logged (typically `/var/log/auth.log` or `/var/log/secure`) — this
audit trail is the core security advantage over `su`, and is exactly what
[The sudoers File and visudo](../docs/08-users/sudoers-and-visudo.md) controls the granularity of.

## Performance Considerations

Not applicable.

## Production Usage

`sudo -l` is a fast, safe way to check exactly what a given account is permitted to do before
relying on it in a script or runbook — worth checking rather than assuming broad access.

## Related Commands

- [`su`](su.md) — full session switch, requires the target's own password

## Related Concepts

- [su vs sudo](../docs/08-users/su-vs-sudo.md)
- [The sudoers File and visudo](../docs/08-users/sudoers-and-visudo.md)
