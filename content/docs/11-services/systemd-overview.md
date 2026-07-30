---
title: "systemd Overview"
description: "systemd's architecture beyond 'it's PID 1' — unit types, where unit files actually live, and why parallel startup was the whole point."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["11-services/what-is-a-service-daemon"]
relatedTopics: ["systemctl-start-stop-enable-disable-status"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#systemd-unit-types"]
relatedCheatsheet: "systemctl"
furtherReading: [{"label": "systemd.unit(5) man page", "url": "https://www.freedesktop.org/software/systemd/man/systemd.unit.html"}]
nextTopic: "11-services/systemctl-start-stop-enable-disable-status"
prevTopic: "11-services/what-is-a-service-daemon"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["systemd architecture", "systemd unit types", "systemd unit file location", "systemd parallel boot"]
canonicalUrl: "/docs/services/systemd-overview"
---

# systemd Overview

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** systemd manages more than services — units come in several types (service, socket,
> timer, mount, target, and others), defined in files under `/etc/systemd/system/` (local
> overrides) or `/usr/lib/systemd/system/` (package-installed defaults). Its defining advantage
> over SysV init was starting independent services in parallel instead of one at a time.

## What is it?

systemd is the init system and service manager on most modern Linux distros — as
[Module 10 previewed](../10-processes/systemd-process-management-preview.md), it's PID 1 and
applies process supervision mechanics to services. This page covers its broader architecture:
what a "unit" actually is beyond just services, and where its configuration lives.

## Why does it exist?

Its predecessor, SysV init (full comparison in this module's last topic), started services
sequentially via shell scripts, in a fixed order, even when services had no actual dependency on
each other — slow, and it made expressing "only start B after A is actually ready" clumsy.
systemd was designed around explicit, declarative dependencies, enabling genuinely parallel
startup wherever no dependency actually requires waiting.

## Where is it used?

Nearly every mainstream distro today (Ubuntu, Debian, RHEL/CentOS/Rocky, Fedora, SUSE) uses
systemd as PID 1 — this is close to universal in current production Linux, making it foundational
knowledge regardless of which distro family a job uses.

## How it works

> 📊 Diagram: a small icon set showing systemd's major unit types — `.service` (a managed
> process), `.socket` (network/IPC socket activation), `.timer` (systemd's cron alternative,
> Module 17), `.mount` (a filesystem mount, Module 05), `.target` (a synchronization point/group,
> this module's Dependencies topic) — each with a one-line label, showing systemd manages far more
> than just "services."

- **Unit**: systemd's generic term for anything it manages — a service, a mount point, a timer,
  a device, a network socket, and more, each with its own unit file type.
- **Unit file locations**: `/usr/lib/systemd/system/` (or `/lib/systemd/system/`) holds
  package-installed defaults; `/etc/systemd/system/` holds local overrides and custom units —
  `/etc/` takes precedence, following the same "local config overrides package defaults"
  convention seen elsewhere in Linux (e.g. Module 04's shell config layering).
- **Parallel startup**: because units declare their actual dependencies explicitly (Module 11's
  Dependencies topic), systemd can start everything with no unmet dependency simultaneously,
  rather than working through one fixed sequential list — the core reason systemd boots
  meaningfully faster than SysV init did.

## Real-world example

A server boots with a database service and a completely unrelated logging agent, neither
depending on the other. Under SysV init's fixed sequential script order, one would always wait
for the other to finish starting even though there's no real reason to. Under systemd, both start
in parallel the moment their own actual prerequisites are met — a direct, measurable boot-time
improvement from the same underlying architectural change. A custom application added later gets
its own unit file at `/etc/systemd/system/myapp.service` — placed under `/etc/` specifically
because it's a local addition, not a package-managed default, following the same override
convention as everything else placed under `/etc/`.

## Commands

No command example on this page — this page is architectural context. See
[systemctl: start, stop, enable, disable, status](systemctl-start-stop-enable-disable-status.md)
for the first hands-on systemd commands in this module.

## Production example

Not applicable — see the next topic for the first terminal session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Place custom unit files under `/etc/systemd/system/` | Edit package-installed unit files under `/usr/lib/systemd/system/` directly |
| Think of systemd as managing units broadly, not just services | Assume systemd only manages `.service` units |

## Common mistakes

- Assuming systemd only manages services — it manages mounts, timers, sockets, devices, and more,
  all as different unit types under the same framework.
- Editing package-installed unit files directly under `/usr/lib/systemd/system/`, which can be
  overwritten by the next package update — local customizations belong in `/etc/systemd/system/`
  instead (or as an override, covered in the unit file topic).
- Assuming systemd's parallel startup means "no ordering at all" — dependencies still express
  required ordering; what changed is that *unrelated* units no longer wait on each other
  unnecessarily.

## Best practices

- Place custom or locally-modified unit files under `/etc/systemd/system/`, never editing
  package-provided files under `/usr/lib/systemd/system/` directly.
- Think in terms of "what unit type actually fits this need" (timer vs. cron, socket activation
  vs. always-running service) rather than reflexively reaching for `.service` for everything.

## Exercises

1. List the unit files present in `/etc/systemd/system/` on any Linux system you have access to.
2. Find a `.service`, `.timer`, or `.mount` unit file under `/usr/lib/systemd/system/` and open
   it to see its raw contents (full syntax covered in this module's unit file topic).
3. Explain in one sentence why systemd boots faster than SysV init typically did.

## Quiz

**Q: Does systemd only manage services?**
<details><summary>Show answer</summary>
No — it manages a variety of unit types including services, mounts, timers, sockets, and targets,
all under the same general framework.
</details>

**Q: Where should a custom, locally-created unit file be placed?**
<details><summary>Show answer</summary>
`/etc/systemd/system/` — package-installed defaults live under `/usr/lib/systemd/system/` and
shouldn't be edited directly, since package updates can overwrite them.
</details>

## Interview questions

- What unit types does systemd manage beyond services? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A "unit" is systemd's generic term for anything it manages — services, mounts, timers, sockets,
  targets, and more.
- `/etc/systemd/system/` holds local/custom units; `/usr/lib/systemd/system/` holds package
  defaults — `/etc/` takes precedence.
- Parallel startup based on explicit dependencies is systemd's core architectural advantage over
  SysV init.

## Further Reading

- [systemd.unit(5) man page](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)

## Related topics

- [systemctl: start, stop, enable, disable, status](systemctl-start-stop-enable-disable-status.md)
