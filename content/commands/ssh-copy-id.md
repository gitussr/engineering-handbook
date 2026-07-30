---
title: "ssh-copy-id — Install a Public Key on a Remote Server"
description: "Copy a local public key into a remote account's ~/.ssh/authorized_keys, enabling key-based login."
relatedConcepts: ["13-ssh/ssh-key-generation-and-key-based-auth"]
relatedCommands: ["ssh-keygen", "ssh"]
careerRelevance: ["devops", "cloud", "sre"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["ssh-copy-id examples", "install ssh public key", "authorized_keys explained", "ssh-copy-id specific key"]
canonicalUrl: "/commands/ssh-copy-id"
---

# ssh-copy-id

🟢 Must Know · Relevant for: DevOps · Cloud · SRE

> **TL;DR:** `ssh-copy-id user@host` appends your public key to that account's
> `~/.ssh/authorized_keys` on the remote server — the standard, safe way to enable key-based
> login, instead of hand-editing the file.

## Purpose

`ssh-copy-id` installs a local public key on a remote server for key-based authentication — see
[SSH Key Generation and Key-Based Auth](../docs/13-ssh/ssh-key-generation-and-key-based-auth.md)
for the full concept.

## Syntax

```
ssh-copy-id [OPTIONS] [user@]host
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `[user@]host` | The remote account and host to install the key on | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-i FILE` | Use a specific public key file instead of the default |
| `-p PORT` | Connect on a non-default port |

## Examples

```
$ ssh-copy-id deploy@web-01.internal
```
Install your default public key (`~/.ssh/id_ed25519.pub` or similar) on the given account.

```
$ ssh-copy-id -i ~/.ssh/deploy_key.pub deploy@web-01.internal
```
Install a specific, non-default public key.

```
$ ssh-copy-id -p 2222 deploy@web-01.internal
```
Install a key on a server using a non-default SSH port.

## Expected Output

```
$ ssh-copy-id deploy@web-01.internal
Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'deploy@web-01.internal'"
and check to make sure that only the key(s) you wanted were added.
```

## Exit Status

`0` on success, non-zero if the connection or authentication (initially still password-based)
fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `ERROR: No identities found` | No public key exists locally yet | Run [`ssh-keygen`](ssh-keygen.md) first |
| Still prompted for a password after running | Server-side permissions on `~/.ssh` or `authorized_keys` are too permissive | Server's SSH will refuse an insecurely-permissioned `authorized_keys`; fix with `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys` on the server |
| Prompted for a password *during* `ssh-copy-id` itself | Expected — this initial connection still uses password auth to install the key in the first place | Enter the password once; subsequent logins use the key |

## Security Considerations

`ssh-copy-id` itself requires an initial working authentication method (typically password) to
connect and install the key — it's the bootstrap step before key-only auth
([this module's hardening topic](../docs/13-ssh/ssh-hardening-disable-root-login-key-only-auth.md))
can be enabled.

## Performance Considerations

Negligible — a one-time setup action per server/account.

## Production Usage

Run `ssh-copy-id` for every server/account combination before disabling password authentication
server-wide — confirming key-based login actually works is the required step before that
hardening change.

## Related Commands

- [`ssh-keygen`](ssh-keygen.md) — generates the key pair `ssh-copy-id` installs
- [`ssh`](ssh.md) — used afterward to confirm key-based login works

## Related Concepts

- [SSH Key Generation and Key-Based Auth](../docs/13-ssh/ssh-key-generation-and-key-based-auth.md)
