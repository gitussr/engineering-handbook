---
title: "ssh — Log Into or Run Commands on a Remote Host"
description: "Open an encrypted remote shell, run a single remote command non-interactively, or carry other traffic through the connection via tunneling."
relatedConcepts: ["13-ssh/ssh-command-usage", "13-ssh/what-is-ssh-and-how-it-works", "13-ssh/ssh-tunneling-and-port-forwarding"]
relatedCommands: ["scp", "sftp", "ssh-keygen"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: [{"context": "OpenSSH (Linux)", "note": "The near-universal standard SSH client on Linux distros. Behavior is consistent across distros; differences mostly come from server-side sshd_config, not the client."}]
updatedAt: "2026-07-27"
keywords: ["ssh command examples", "ssh user@host", "ssh port forwarding flags", "ssh verbose debug"]
canonicalUrl: "/commands/ssh"
---

# ssh

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `ssh user@host` opens a remote shell; `ssh user@host "command"` runs one command and
> returns. `-i` selects a specific key, `-p` a non-default port, `-L`/`-R` set up tunneling,
> `-v` shows verbose connection debugging.

## Purpose

`ssh` opens an encrypted, authenticated connection to a remote host — see
[ssh Command Usage](../docs/13-ssh/ssh-command-usage.md) and
[What is SSH and How It Works](../docs/13-ssh/what-is-ssh-and-how-it-works.md) for the full
concept.

## Syntax

```
ssh [OPTIONS] [user@]host [command]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `[user@]host` | Target host, optionally with a username | Yes |
| `command` | A command to run remotely and exit, instead of opening an interactive shell | No |

## Options

| Flag | Meaning |
|---|---|
| `-i FILE` | Use a specific private key file |
| `-p PORT` | Connect on a non-default port |
| `-v` / `-vvv` | Verbose (increasingly detailed) connection debugging |
| `-L local:host:remote` | Local port forwarding |
| `-R remote:host:local` | Remote port forwarding |
| `-A` | Forward the local SSH agent to the remote host |
| `-N` | Don't execute a remote command — used with `-L`/`-R` for tunnel-only connections |

## Examples

```
$ ssh deploy@web-01.internal
```
Open an interactive remote shell.

```
$ ssh deploy@web-01.internal "systemctl status nginx"
```
Run a single command remotely and return immediately.

```
$ ssh -i ~/.ssh/prod_key -p 2222 deploy@web-01.internal
```
Connect using a specific key and non-default port.

```
$ ssh -L 5432:localhost:5432 deploy@dbserver
```
Forward a remote-only-accessible database port to your local machine.

## Expected Output

```
$ ssh deploy@web-01.internal
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-31-generic x86_64)
deploy@web-01:~$
```

## Exit Status

`0` on a successful session (or successful remote command), non-zero on connection failure,
authentication failure, or (for the remote-command form) the remote command's own non-zero exit
status.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Connection refused` | Nothing listening on the target port, or firewall blocking it | Verify with [`ss`](ss.md) on the target and check the firewall (Module 12) |
| `Permission denied (publickey)` | Key not installed on the server, or wrong key specified | Confirm with [`ssh-copy-id`](ssh-copy-id.md) or specify the correct key with `-i` |
| `Host key verification failed` | The server's host key changed since last connection | Investigate before proceeding — could be a legitimate server rebuild or a security concern |

## Security Considerations

Never disable host key checking (`StrictHostKeyChecking no`) as a blanket default — it removes
the server-authentication protection SSH is specifically designed to provide. Prefer key-based
auth over passwords wherever possible.

## Performance Considerations

Connection setup (key exchange, authentication) has fixed overhead per connection — for many
repeated connections to the same host, `ControlMaster`/`ControlPersist` (connection multiplexing,
configurable in `~/.ssh/config`) can reuse one underlying connection instead of renegotiating
each time.

## Compatibility Notes

OpenSSH is the standard client across virtually all Linux distros; behavior differences typically
come from the server side (`sshd_config`), not the client.

## Production Usage

`ssh user@host "command"` (the non-interactive form) is the building block automation tools
(Ansible, deploy scripts) use internally — worth using directly in your own scripts rather than
scripting an interactive session.

## Related Commands

- [`scp`](scp.md) / [`sftp`](sftp.md) — file transfer over the same connection type
- [`ssh-keygen`](ssh-keygen.md) — generate the key pair `ssh` authenticates with

## Related Concepts

- [ssh Command Usage](../docs/13-ssh/ssh-command-usage.md)
- [SSH Tunneling and Port Forwarding](../docs/13-ssh/ssh-tunneling-and-port-forwarding.md)
