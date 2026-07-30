---
title: "Logs"
description: "Module 16 of the Linux roadmap — why logging matters, /var/log, journalctl beyond the basics, syslog/rsyslog, logrotate, centralized logging, and reading logs under incident pressure."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
type: "module-index"
nextTopic: "16-logs/linux-logging-overview"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/logs"
---

# Logs

Module 16 of 34 · Stage: Intermediate · Previous: [15 Storage](../15-storage/index.md)

[Module 11](../11-services/journalctl-basics.md) already taught `journalctl`'s daily-driver
flags. This module builds the rest of the logging picture around that: the traditional
`/var/log` layout, `journalctl`'s deeper operational features (persistence, disk management),
syslog/rsyslog underneath it, automated rotation, centralized logging at fleet scale, and the
practical workflow for using all of it under real incident pressure.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Linux Logging Overview](linux-logging-overview.md) | 🟢 Must Know |
| [/var/log Directory Structure](var-log-directory-structure.md) | 🟢 Must Know |
| [journalctl Deep Dive](journalctl-deep-dive.md) | 🟢 Must Know |
| [syslog and rsyslog](syslog-and-rsyslog.md) | 🟡 Good to Know |
| [logrotate](logrotate.md) | 🟡 Good to Know |
| [Centralized Logging Concepts (ELK, Grafana Loki)](centralized-logging-concepts.md) | 🔴 Expert |
| [Reading Logs During a Production Incident](reading-logs-during-a-production-incident.md) | 🟢 Must Know |

## What you should be able to do after this module

- Explain why logging matters and identify which layer (kernel, systemd, application) likely
  recorded a given event.
- Navigate `/var/log` and recognize common log files across Debian/Ubuntu and RHEL-family distros.
- Manage journal persistence and disk usage, and inspect logs from a specific past boot.
- Explain how syslog/rsyslog categorizes and routes messages, and how it relates to the journal.
- Configure `logrotate` so a custom application's logs don't grow unbounded.
- Explain why centralized logging becomes necessary at fleet scale, and the ELK/Loki tradeoff.
- Apply a repeatable time-window-then-service-then-severity sequence when investigating logs
  during a live incident.

## Known, intentional gaps in this module

- A canonical command page exists for [`logrotate`](../../commands/logrotate.md); the existing
  [`journalctl`](../../commands/journalctl.md) page (Module 11) was extended with this module's
  deep-dive flags (`--disk-usage`, `--vacuum-size`/`--vacuum-time`, `--list-boots`, `-o json`)
  rather than duplicated into a second page — one command, one canonical page.
- ELK (Elasticsearch, Logstash, Kibana) and Grafana Loki are external systems, not Linux shell
  commands, and are covered conceptually rather than as canonical command pages.
- `rsyslog` itself is a service managed via `systemctl` (already covered) rather than a
  standalone interactive command — its configuration file, not a command, is what this module
  documents.

**Previous module:** [15 Storage](../15-storage/index.md)
**Next module:** [17 Cron →](../17-cron/index.md)
