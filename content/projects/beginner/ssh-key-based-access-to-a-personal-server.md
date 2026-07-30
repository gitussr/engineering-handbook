---
title: "Project: Set Up Secure SSH Key-Based Access to a Personal Server"
description: "A beginner Linux project: provision a server or VM, create a non-root user, and configure secure SSH key-based access with password login disabled."
type: "project"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops", "cybersecurity"]
updatedAt: "2026-07-30"
keywords: ["ssh key setup project", "ssh hardening beginner project", "linux server access project", "create a linux user with sudo access"]
canonicalUrl: "/projects/beginner/ssh-key-based-access-to-a-personal-server"
---

# Project: Set Up Secure SSH Key-Based Access to a Personal Server

🟢 Beginner · Relevant for: Linux Administrator, DevOps, Cybersecurity

## Goal

Take a fresh server or VM (a cloud free-tier instance or a local VM both work) and configure it
the way a real production server should be handed off: a dedicated non-root user, key-only SSH
access, and root login disabled.

## Requirements

- A non-root user created with sudo access.
- An SSH key pair generated on your own machine (not on the server).
- The public key installed in the new user's `authorized_keys`.
- `PasswordAuthentication` and `PermitRootLogin` both disabled in `sshd_config`.
- Verification that you can still log in as the new user after both changes — before you close
  your original session.

## Suggested Approach

1. Provision the server, and note the initial access method you're given (usually root + password,
   or a cloud-provider default key) — you'll disable this at the end, not the start.
2. Create the new user and add them to the sudo group.
3. Generate a key pair locally, then copy the public key to the new user's `authorized_keys`
   (manually or with a copy tool) — confirm you can SSH in as the new user with the key *before*
   touching any SSH server settings.
4. Edit `sshd_config` to disable password authentication and root login, restart the SSH service,
   and — critically — test the new connection in a *second* terminal window while keeping your
   current session open, so you have a way back in if something's misconfigured.
5. Only close the original session once the new key-based, non-root login is confirmed working.

## Stretch Goals

- Change the SSH port and confirm you can still connect on the new port.
- Set up an `~/.ssh/config` entry so you can connect with a short alias instead of the full command.
- Add a second SSH key from a different device and confirm both work independently.
- Write a short runbook documenting exactly what you did, as if handing the server to a teammate.

## Related Modules

- [Users](../../docs/08-users/index.md) — creating users, sudoers
- [SSH](../../docs/13-ssh/index.md) — key generation, hardening
- [Permissions](../../docs/07-permissions/index.md) — file/directory permissions on `~/.ssh`

## Related topics

- [SSH Cheat Sheet](../../cheatsheets/ssh.md)
- [File Organization and Backup Script Project](file-organization-and-backup-script.md)
- [Projects Hub](../index.md)
