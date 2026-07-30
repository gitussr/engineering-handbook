---
title: "Services"
description: "Module 11 of the Linux roadmap — daemons, systemd architecture, systemctl, unit files, journalctl, dependencies/targets, and the SysV-to-systemd transition."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
type: "module-index"
nextTopic: "11-services/what-is-a-service-daemon"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/services"
---

# Services

Module 11 of 34 · Stage: Intermediate · Previous: [10 Processes](../10-processes/index.md)

[Module 10's closing topic](../10-processes/systemd-process-management-preview.md) previewed how
systemd applies process concepts (PID 1, signals, reaping) to service supervision. This module
delivers the full picture: what a daemon actually is, systemd's architecture, the `systemctl`
commands you'll run daily, how to write a unit file, reading logs with `journalctl`, and how
services depend on each other.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [What is a Service / Daemon](what-is-a-service-daemon.md) | 🟢 Must Know |
| [systemd Overview](systemd-overview.md) | 🟢 Must Know |
| [systemctl: start, stop, enable, disable, status](systemctl-start-stop-enable-disable-status.md) | 🟢 Must Know |
| [Writing a systemd Unit File](writing-a-systemd-unit-file.md) | 🟡 Good to Know |
| [journalctl Basics](journalctl-basics.md) | 🟢 Must Know |
| [Service Dependencies and Targets](service-dependencies-and-targets.md) | 🔴 Expert |
| [init Systems: SysV vs systemd](init-systems-sysv-vs-systemd.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what a daemon is and how it differs from an ordinary foreground process.
- Explain systemd's architecture: unit types, unit file locations, and its role as PID 1.
- Use `systemctl` to start, stop, enable, disable, and check the status of a service — and
  explain why start/enable are two genuinely different things.
- Write a basic systemd unit file for a custom service.
- Read logs for a specific service with `journalctl`, including following live output.
- Explain service dependencies and targets, and how they replaced SysV runlevels.
- Explain, at a practical level, why systemd replaced SysV init.

## Known, intentional gaps in this module

- Canonical command pages exist for `systemctl` and `journalctl` — the two commands this module
  is built around.
- `systemd-analyze` and the legacy `service` compatibility command are referenced but don't have
  canonical pages yet.

**Previous module:** [10 Processes](../10-processes/index.md)
**Next module:** [12 Networking →](../12-networking/index.md)
