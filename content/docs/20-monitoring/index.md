---
title: "Monitoring"
description: "Module 20 of the Linux roadmap — what to monitor and why, CLI observation tools, fleet-scale monitoring stacks, agent setup, alerting concepts, and log-based monitoring."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
type: "module-index"
nextTopic: "20-monitoring/monitoring-fundamentals"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/monitoring"
---

# Monitoring

Module 20 of 34 · Stage: Professional · Previous: [19 Security](../19-security/index.md)

Module 19 hardened a system against threats; this module watches it in production. Starting from
the resource dimensions that matter (CPU, memory, disk, network), through the CLI tools for a
single host, up to the fleet-scale stacks and alerting discipline that make monitoring practical
past a handful of servers — closing with log-based monitoring for the problems resource metrics
alone can never catch.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Monitoring Fundamentals: What to Watch and Why](monitoring-fundamentals.md) | 🟢 Must Know |
| [top, htop, vmstat, iostat](top-htop-vmstat-iostat.md) | 🟢 Must Know |
| [Monitoring Stacks Overview: Prometheus, Grafana, Nagios, Zabbix](monitoring-stacks-overview.md) | 🟡 Good to Know |
| [Setting Up a Basic Monitoring Agent](setting-up-a-basic-monitoring-agent.md) | 🔴 Expert |
| [Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md) | 🟡 Good to Know |
| [Log-Based Monitoring](log-based-monitoring.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Name the four core resource dimensions to monitor and distinguish metrics, logs, and alerts.
- Use `top`/`htop` for per-process activity, `vmstat` for system-wide memory/swap, and `iostat` for
  per-device disk I/O — and know which to reach for first.
- Compare Prometheus/Grafana against Nagios/Zabbix and explain the pull-vs-push distinction.
- Install and expose a basic monitoring agent as a systemd service, scoped correctly by firewall.
- Design alerting thresholds that avoid alert fatigue, using duration and severity deliberately.
- Derive metrics and alerts from log content for problems resource monitoring alone can't catch.

## Known, intentional gaps in this module

- Canonical command pages exist for [`vmstat`](../../commands/vmstat.md) and
  [`iostat`](../../commands/iostat.md) — the two commands newly introduced in this module's
  tooling bullet. [`top`](../../commands/top.md) (Module 10) was extended with batch-mode flags
  for scripted monitoring use, same pattern as the Module 16 `journalctl` extension.
- `htop` remains covered within [Module 10's topic page](../10-processes/ps-top-htop.md) rather
  than a separate canonical command page — an existing gap from Module 10 (its rich interactive
  UI doesn't suit this documentation's flag-driven command-page format), not new to this module.
- Prometheus, Grafana, Nagios, and Zabbix are external platforms, not Linux shell commands, and are
  covered conceptually — consistent with how ELK and Grafana Loki were treated in Module 16.
- `node_exporter` (used illustratively for agent setup) is a third-party monitoring agent binary,
  not a core Linux command, and doesn't get a canonical command page.

**Previous module:** [19 Security](../19-security/index.md)
**Next module:** [21 Performance →](../21-performance/index.md)
