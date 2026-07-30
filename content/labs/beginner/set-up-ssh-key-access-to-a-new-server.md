---
title: "Lab: Set Up SSH Key Access to a New Server"
description: "A beginner Linux lab, framed as a real onboarding ticket: configure secure SSH key-based access to a freshly provisioned server."
type: "lab"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops"]
updatedAt: "2026-07-30"
keywords: ["ssh key lab", "linux ssh access lab", "beginner linux lab ssh", "ssh key setup exercise"]
canonicalUrl: "/labs/beginner/set-up-ssh-key-access-to-a-new-server"
---

# Lab: Set Up SSH Key Access to a New Server

🟢 Beginner · Relevant for: Linux Administrator, DevOps

## Scenario

**Ticket #LX-101:** "New dev server was provisioned this morning — currently only accessible via
the cloud provider's password-based console login. Set it up so our team can SSH in with keys.
Password login should stay on for now until we confirm key access works."

## Environment Setup

- A fresh Linux VM (cloud free-tier instance or local VM) you can log into as `root` or via a
  provider console, with password authentication currently enabled.
- Your own machine with a terminal — no key pair generated yet.

## Tasks

1. Generate a new SSH key pair on your own machine.
2. Copy the public key to the server so you can log in without a password.
3. Confirm you can SSH into the server using the key, with no password prompt.
4. Leave password authentication enabled on the server for now (a later ticket will handle
   disabling it) — this task is about adding key access, not removing the old method yet.

## Hints

<details><summary>Show hint</summary>
`ssh-keygen` creates the key pair; the public key (the `.pub` file) is the one that goes on the
server, never the private key. There's a purpose-built command for copying it over that saves you
from manually editing `authorized_keys`.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# On your own machine — generate a key pair
ssh-keygen -t ed25519 -C "your-email@example.com"
# Press Enter to accept the default file location; set a passphrase or leave it empty

# Copy the public key to the server (prompts for the server's current password once)
ssh-copy-id user@server-ip

# Confirm key-based login works with no password prompt
ssh user@server-ip
```

If `ssh-copy-id` isn't available, the manual equivalent is appending the contents of
`~/.ssh/id_ed25519.pub` to `~/.ssh/authorized_keys` on the server, then ensuring
`~/.ssh` is `700` and `authorized_keys` is `600` — SSH silently refuses keys with looser
permissions.
</details>

## What You Learned

- How to generate an SSH key pair and understand which half goes where.
- How `ssh-copy-id` installs a public key into a server's `authorized_keys`.
- Why SSH enforces strict permissions on `~/.ssh` and `authorized_keys`.

## Related Modules

- [SSH](../../docs/13-ssh/index.md)
- [Users](../../docs/08-users/index.md)

## Related topics

- [SSH Cheat Sheet](../../cheatsheets/ssh.md)
- [SSH Key-Based Access Project](../../projects/beginner/ssh-key-based-access-to-a-personal-server.md)
- [Labs Hub](../index.md)
