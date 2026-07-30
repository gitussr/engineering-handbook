---
title: "hostnamectl — View and Set the System Hostname"
description: "View or change a systemd-based system's hostname, and see its OS, kernel, and architecture in one summary — the standard post-install step for naming a new server."
relatedConcepts: ["02-installing-linux/post-install-checklist"]
relatedCommands: []
careerRelevance: ["linux-administrator", "devops", "sre", "cloud"]
difficulty: "must-know"
compatibility: [{"context": "systemd-based distros", "note": "hostnamectl requires systemd; non-systemd systems use hostname or editing /etc/hostname directly instead"}]
updatedAt: "2026-07-30"
keywords: ["hostnamectl command", "set hostname linux", "hostnamectl set-hostname", "check hostname linux"]
canonicalUrl: "/commands/hostnamectl"
---

# hostnamectl

🟢 Must Know · Relevant for: Linux Administrator · DevOps · SRE · Cloud

> **TL;DR:** `hostnamectl` with no arguments shows the current hostname, OS, kernel, and
> architecture in one summary. `hostnamectl set-hostname {name}` changes it — and takes effect
> immediately, no reboot required.

## Purpose

`hostnamectl` is systemd's tool for viewing and setting a machine's hostname, along with related
identity metadata (OS pretty name, kernel version, architecture, virtualization type) — see
[Post-Install Checklist](../docs/02-installing-linux/post-install-checklist.md) for why setting a
meaningful hostname is one of the first things to do on a freshly provisioned server.

## Syntax

```
hostnamectl [COMMAND] [HOSTNAME]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `HOSTNAME` | The new hostname, only used with `set-hostname` | No — omit entirely to just view current status |

## Options

| Flag/subcommand | Meaning |
|---|---|
| *(none)* | Show current hostname and system information |
| `set-hostname {name}` | Set a new static hostname |
| `status` | Same as running with no arguments — explicit form |

## Examples

```
$ hostnamectl
```
Show the current hostname alongside OS, kernel, and architecture information.

```
$ sudo hostnamectl set-hostname web-prod-07
```
Set the system's hostname — a standard early step when provisioning a new server, so it's
identifiable in logs, monitoring, and `ssh` prompts instead of a generic default.

```
$ hostnamectl
```
Re-run after setting a new hostname to confirm the change took effect.

## Expected Output

```
$ hostnamectl
 Static hostname: web-prod-07
       Icon name: computer-vm
         Chassis: vm
      Machine ID: 3f7a8b9c1d2e4f5a6b7c8d9e0f1a2b3c
         Boot ID: a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
  Virtualization: kvm
Operating System: Ubuntu 22.04.4 LTS
          Kernel: Linux 6.8.0-45-generic
    Architecture: x86-64
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Success |
| Non-zero | Insufficient privileges for `set-hostname` (requires root/sudo), or systemd/hostnamectl unavailable on a non-systemd system |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Permission denied` on `set-hostname` | Not run with `sudo`/root | Re-run with `sudo` |
| Hostname change doesn't appear in the shell prompt | Some shells cache the hostname at session start | Open a new terminal session, or manually refresh the prompt |
| `hostnamectl: command not found` | Non-systemd distro | Use `hostname {name}` and edit `/etc/hostname` directly instead |

## Security Considerations

Setting the hostname itself carries no direct security risk, but a meaningful, unique hostname
matters indirectly for security operations — logs, alerts, and audit trails that only show a
generic or duplicated hostname across a fleet are much harder to attribute during an incident.

## Performance Considerations

Instantaneous — this changes a small piece of system configuration with no measurable performance
impact.

## Compatibility Notes

`hostnamectl` is specific to systemd-based distros (most modern mainstream distros). Non-systemd
systems (some minimal/embedded distros, older SysV-init systems) use the plain `hostname` command
and manual edits to `/etc/hostname` instead.

## Production Usage

Setting a clear, convention-following hostname (e.g. `web-prod-07`, indicating role, environment,
and instance number) immediately after provisioning is standard practice — it's the first thing
that shows up in `ssh` prompts, logs, and monitoring dashboards, and a generic default hostname
(`ubuntu`, `localhost`) becomes a real source of confusion the moment there's more than one server.

## Related Commands

- [`uname`](uname.md) — shows kernel/architecture information without the hostname-management
  functionality

## Related Concepts

- [Post-Install Checklist](../docs/02-installing-linux/post-install-checklist.md)
