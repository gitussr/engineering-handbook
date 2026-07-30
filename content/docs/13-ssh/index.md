---
title: "SSH"
description: "Module 13 of the Linux roadmap — how SSH works, everyday connection usage, key-based authentication, file transfer tools, config file aliases, tunneling, hardening, and agent forwarding."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
type: "module-index"
nextTopic: "13-ssh/what-is-ssh-and-how-it-works"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/ssh"
---

# SSH

Module 13 of 34 · Stage: Intermediate · Previous: [12 Networking](../12-networking/index.md)

[Module 12](../12-networking/ports-and-sockets-explained.md) covered ports and sockets in
general; this module is entirely about the one protocol built on top of them that every other
topic in this documentation assumes you can already use: SSH. It moves from the protocol itself,
through daily-driver commands, to the two topics (tunneling, hardening) that separate basic usage
from production-grade practice.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [What is SSH and How It Works](what-is-ssh-and-how-it-works.md) | 🟢 Must Know |
| [ssh Command Usage](ssh-command-usage.md) | 🟢 Must Know |
| [SSH Key Generation and Key-Based Auth](ssh-key-generation-and-key-based-auth.md) | 🟢 Must Know |
| [scp, sftp, rsync](scp-sftp-rsync.md) | 🟢 Must Know |
| [SSH Config File: ~/.ssh/config](ssh-config-file.md) | 🟡 Good to Know |
| [SSH Tunneling and Port Forwarding](ssh-tunneling-and-port-forwarding.md) | 🔴 Expert |
| [SSH Hardening: Disable Root Login, Key-Only Auth](ssh-hardening-disable-root-login-key-only-auth.md) | 🟡 Good to Know |
| [SSH Agent and Agent Forwarding](ssh-agent-and-agent-forwarding.md) | 🔴 Expert |

## What you should be able to do after this module

- Explain what SSH provides (encryption, server and client authentication) and why it replaced
  older, plain-text remote access protocols.
- Connect to a remote host interactively and run a single command non-interactively.
- Generate an SSH key pair, install it on a server, and explain why key-based auth is more secure
  than passwords.
- Choose between `scp`, `sftp`, and `rsync` for a given file-transfer situation.
- Define host aliases in `~/.ssh/config` to avoid retyping long connection strings.
- Use local and remote port forwarding to reach a firewalled service without weakening the
  firewall.
- Apply baseline SSH hardening (disabling root login and password auth) in the correct, safe
  order.
- Explain what SSH agent forwarding actually does and does not expose to an intermediate host.

## Known, intentional gaps in this module

- Canonical command pages exist for `ssh`, `ssh-keygen`, `ssh-copy-id`, `scp`, `sftp`, `rsync`,
  `ssh-agent`, and `ssh-add` — every command this module centers on.
- `sshd_config` itself (server-side configuration beyond the two hardening directives covered
  here) is referenced but not exhaustively documented — deeper server-side hardening returns in
  [Module 19: Security](../19-security/index.md).
- `ProxyJump` (a config-file alternative to agent forwarding for reaching a host through a jump
  server) is mentioned as a best practice but doesn't get its own topic page — it's a single
  `~/.ssh/config` directive, not a distinct concept.

**Previous module:** [12 Networking](../12-networking/index.md)
**Next module:** [14 Package Managers →](../14-package-managers/index.md)
