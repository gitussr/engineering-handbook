---
title: "Monitoring Stacks Overview: Prometheus, Grafana, Nagios, Zabbix"
description: "How fleet-scale monitoring platforms differ from the CLI tools in this module — pull vs. push metric collection, and dashboards vs. threshold-based alerting."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/top-htop-vmstat-iostat"]
relatedTopics: ["top-htop-vmstat-iostat", "setting-up-a-basic-monitoring-agent"]
relatedCommands: []
careerRelevance: ["devops", "sre", "cloud", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#prometheus-vs-nagios"]
relatedCheatsheet: ""
furtherReading: [{"label": "Prometheus Documentation", "url": "https://prometheus.io/docs/"}, {"label": "Grafana Documentation", "url": "https://grafana.com/docs/"}]
nextTopic: "20-monitoring/setting-up-a-basic-monitoring-agent"
prevTopic: "20-monitoring/top-htop-vmstat-iostat"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["prometheus vs nagios", "grafana vs prometheus", "zabbix vs nagios", "monitoring stack comparison"]
canonicalUrl: "/docs/monitoring/monitoring-stacks-overview"
---

# Monitoring Stacks Overview: Prometheus, Grafana, Nagios, Zabbix

🟡 Good to Know · Relevant for: DevOps · SRE · Cloud · Platform

> **TL;DR:** Manually running `top`/`vmstat` doesn't scale past a handful of servers. Prometheus
> (metrics collection) + Grafana (dashboards) is the dominant modern combination; Nagios and
> Zabbix are older, more monolithic platforms combining collection, alerting, and dashboards in
> one tool.

## What is it?

Four widely-used platforms for fleet-scale monitoring, each solving the "manual per-server
checking doesn't scale" problem the previous page's CLI tools run into past a handful of machines.

## Why does it exist?

Running `vmstat`/`iostat` by hand on one server works fine; running it on a thousand servers, then
correlating the results, is a different problem entirely. These platforms exist to collect metrics
continuously across an entire fleet, store them queryably over time, visualize them, and alert on
them — automating what manual CLI checking cannot do at scale.

## Where is it used?

Any environment with more than a handful of servers to watch, and especially in cloud/container
environments where instances are ephemeral and manual per-machine checking is not just
inconvenient but often impossible (an instance may no longer exist by the time someone investigates
manually).

## How it works

> 📊 Diagram: two architectural patterns side by side — Prometheus (pull-based: a central server
> periodically scrapes metrics endpoints exposed by each monitored host/service) feeding into
> Grafana (a separate dashboarding layer querying Prometheus's stored data) — contrasted with
> Nagios/Zabbix (more monolithic: agents or checks push/report status to a central server that
> itself handles storage, alerting, and basic dashboards in one integrated tool).

**Prometheus** — a metrics collection and storage system, pull-based: it periodically scrapes a
`/metrics` HTTP endpoint exposed by each monitored target, storing the results as a time series
database queryable with its own query language (PromQL). Prometheus itself has only basic
built-in visualization.

**Grafana** — a dashboarding and visualization layer, commonly paired with Prometheus (and many
other data sources) rather than used alone — Prometheus collects and stores, Grafana visualizes
and lets engineers build custom dashboards querying that stored data.

**Nagios** — one of the older, more established monitoring platforms: primarily check-based
(runs periodic scripts/plugins that return an OK/WARNING/CRITICAL status) rather than continuous
metric collection, with alerting built directly into the core product.

**Zabbix** — a more monolithic, all-in-one platform combining agent-based data collection,
storage, alerting, and dashboards in a single system, positioned as a more complete out-of-the-box
solution compared to assembling Prometheus + Grafana + a separate alerting layer.

| Stack | Collection model | Built-in dashboards | Primary strength |
|---|---|---|---|
| Prometheus | Pull (scrapes targets) | Basic only | De facto standard for cloud-native/Kubernetes metrics |
| Grafana | N/A — visualization layer | Yes, rich and customizable | Dashboarding on top of Prometheus or other sources |
| Nagios | Push/check-based | Basic | Mature, check-based alerting; long production track record |
| Zabbix | Agent-based push | Yes, built-in | All-in-one platform, less assembly required |

## Real-world example

A team running a few dozen VMs starts with manual `top`/`vmstat` checks, which quickly becomes
unmanageable as the fleet grows past what one person can check by hand. They adopt Prometheus to
continuously scrape CPU/memory/disk metrics from every host, Grafana to build a fleet-wide
dashboard replacing dozens of manual SSH sessions, and an alerting rule (next page) that pages
on-call the moment any single host's memory usage sustains above 90% — the exact "steady climb"
trend from [Monitoring Fundamentals](monitoring-fundamentals.md)'s real-world example, now caught
automatically instead of discovered after a crash.

## Syntax

No single syntax — each platform has its own configuration format and query language, outside the
scope of this Linux-focused documentation.

## Commands

No canonical command pages exist for these platforms — they're external systems, not Linux shell
commands (the same treatment ELK and Grafana Loki received in
[Module 16](../16-logs/centralized-logging-concepts.md)), covered here conceptually.

## Production example

```
$ curl -s http://localhost:9100/metrics | head -5
# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 45123.45
node_cpu_seconds_total{cpu="0",mode="user"} 2103.22
node_cpu_seconds_total{cpu="0",mode="system"} 512.10
```

A raw Prometheus metrics endpoint (exposed by `node_exporter`, the next page's monitoring agent) —
the plain-text format Prometheus's central server periodically scrapes from every monitored host.

## Do / Don't

| Do | Don't |
|---|---|
| Choose a stack based on team needs (assembled flexibility vs. all-in-one simplicity) | Assume one stack is universally "correct" regardless of context |
| Understand the pull vs. push distinction before troubleshooting a missing metric | Debug a missing metric without knowing whether the platform pulls or expects a push |
| Use Grafana to visualize, and Prometheus (or similar) to collect/store | Expect Prometheus alone to provide rich, customizable dashboards |

## Common mistakes

- Assuming Grafana collects metrics itself, rather than visualizing data collected and stored by a
  separate source like Prometheus.
- Not understanding whether a stack pulls (Prometheus) or expects pushed data, causing confusion
  when a metric silently isn't appearing.
- Adopting a full assembled stack (Prometheus + Grafana + separate alerting) when a smaller team's
  needs would be served just as well by a simpler, all-in-one platform like Zabbix.

## Best practices

- Match the stack to team size and operational maturity — an all-in-one platform (Zabbix, Nagios)
  can be the right choice for a smaller team, not just a "legacy" fallback.
- Understand each component's specific job (Prometheus collects/stores, Grafana visualizes) before
  troubleshooting either in isolation.
- Standardize metric collection early in cloud/container environments, where manual per-instance
  checking becomes impossible as instances scale and rotate.

## Exercises

1. Explain the difference between Prometheus's pull model and a push-based alternative.
2. Describe what Grafana does and does not do on its own, without a data source like Prometheus.
3. Compare Zabbix's all-in-one approach to assembling Prometheus + Grafana + a separate alerting
   layer — what's the tradeoff?

## Quiz

**Q: Does Prometheus collect metrics via push or pull, by default?**
<details><summary>Show answer</summary>
Pull — it periodically scrapes a metrics endpoint exposed by each monitored target, rather than
targets pushing data to it.
</details>

**Q: Can Grafana collect and store metrics on its own?**
<details><summary>Show answer</summary>
No — Grafana is a visualization/dashboarding layer that queries data stored elsewhere (commonly
Prometheus), not a metrics collection or storage system itself.
</details>

**Q: What's the main tradeoff of Zabbix's all-in-one approach compared to Prometheus + Grafana?**
<details><summary>Show answer</summary>
Zabbix bundles collection, storage, alerting, and dashboards into one platform requiring less
assembly, at the cost of the flexibility and modularity of choosing best-of-breed separate tools
for each function.
</details>

## Interview questions

- Compare Prometheus/Grafana to Nagios or Zabbix — when would you choose one over the other? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Manual CLI monitoring doesn't scale past a handful of servers — these platforms automate
  fleet-wide collection, storage, visualization, and alerting.
- Prometheus (pull-based collection) + Grafana (visualization) is the dominant modern combination,
  especially in cloud-native environments.
- Nagios and Zabbix are more established/monolithic alternatives, trading some flexibility for
  simpler out-of-the-box setup.
- None of these are Linux shell commands — they're external systems configured and queried through
  their own interfaces.

## Related topics

- [top, htop, vmstat, iostat](top-htop-vmstat-iostat.md)
- [Setting Up a Basic Monitoring Agent](setting-up-a-basic-monitoring-agent.md)
