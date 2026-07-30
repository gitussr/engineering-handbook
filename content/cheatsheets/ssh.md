---
title: "SSH Cheat Sheet"
description: "A single-page quick reference for SSH: connecting, key management, file transfer, tunneling, and config — the commands you'll use to access and secure remote Linux servers."
type: "cheatsheet"
module: "ssh"
updatedAt: "2026-07-30"
keywords: ["ssh cheat sheet", "ssh commands cheat sheet", "ssh key cheat sheet", "ssh config cheat sheet"]
canonicalUrl: "/cheatsheets/ssh"
---

# SSH Cheat Sheet

The commands for connecting to remote servers, managing keys, transferring files, and tunneling
traffic — grouped the way you'll actually reach for them.

## Connecting

| Command | Description |
|---|---|
| [`ssh`](../commands/ssh.md) `user@host` | Connect to a remote server |
| `ssh -p 2222 user@host` | Connect on a non-default port |
| `ssh -i ~/.ssh/id_ed25519 user@host` | Connect using a specific private key |
| `ssh user@host "command"` | Run a single remote command without opening an interactive shell |

Full explanation → [ssh Command Usage](../docs/13-ssh/ssh-command-usage.md) · [What is SSH and How It Works](../docs/13-ssh/what-is-ssh-and-how-it-works.md)

## Key management

| Command | Description |
|---|---|
| [`ssh-keygen`](../commands/ssh-keygen.md) `-t ed25519` | Generate a new key pair (Ed25519 is the modern default) |
| [`ssh-copy-id`](../commands/ssh-copy-id.md) `user@host` | Copy your public key to a server's `authorized_keys` |
| [`ssh-agent`](../commands/ssh-agent.md) | Start an agent that holds decrypted keys in memory for the session |
| [`ssh-add`](../commands/ssh-add.md) `~/.ssh/id_ed25519` | Add a key to the running agent |

Full explanation → [SSH Key Generation and Key-Based Auth](../docs/13-ssh/ssh-key-generation-and-key-based-auth.md) · [SSH Agent and Agent Forwarding](../docs/13-ssh/ssh-agent-and-agent-forwarding.md)

## File transfer

| Command | Description |
|---|---|
| [`scp`](../commands/scp.md) `file user@host:/path` | Copy a file to a remote server over SSH |
| [`sftp`](../commands/sftp.md) `user@host` | Interactive file transfer session over SSH |
| [`rsync`](../commands/rsync.md) `-avz src/ user@host:dst/` | Sync files efficiently, transferring only what changed |

Full explanation → [scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md)

## Tunneling and port forwarding

| Command | Description |
|---|---|
| `ssh -L 8080:localhost:80 user@host` | Local forward: reach a remote-only port via `localhost:8080` |
| `ssh -R 9000:localhost:3000 user@host` | Remote forward: expose a local port to the remote server |
| `ssh -D 1080 user@host` | Dynamic forward: turn the SSH connection into a SOCKS proxy |

Full explanation → [SSH Tunneling and Port Forwarding](../docs/13-ssh/ssh-tunneling-and-port-forwarding.md)

## Config

| File / setting | Description |
|---|---|
| `~/.ssh/config` | Per-user host aliases, default users/ports/keys — avoids retyping long `ssh` commands |
| `Host myserver` / `HostName ...` / `User ...` / `IdentityFile ...` | Typical config block for one server, invoked as `ssh myserver` |
| `~/.ssh/authorized_keys` | Public keys allowed to log in as a given user (on the server) |

Full explanation → [SSH Config File](../docs/13-ssh/ssh-config-file.md)

## Hardening

| Setting (`/etc/ssh/sshd_config`) | Effect |
|---|---|
| `PermitRootLogin no` | Disables direct root SSH login |
| `PasswordAuthentication no` | Requires key-based auth, disables password login |
| `Port {non-default}` | Reduces automated scan noise (not a real security boundary on its own) |

Full explanation → [SSH Hardening (Disable Root Login, Key-Only Auth)](../docs/13-ssh/ssh-hardening-disable-root-login-key-only-auth.md)

## Related topics

- [Networking Cheat Sheet](networking.md)
- [Permissions Cheat Sheet](permissions.md)
- [SSH Module](../docs/13-ssh/index.md)
- [Cheat Sheets Hub](index.md)
