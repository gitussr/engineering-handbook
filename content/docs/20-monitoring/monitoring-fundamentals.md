---
title: "Monitoring Fundamentals: What to Watch and Why"
description: "The four resource dimensions (CPU, memory, disk, network) and the difference between metrics, logs, and alerts — the vocabulary the rest of this module builds on."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["19-security/security-workflows-for-soc-pentest-roles"]
relatedTopics: ["top-htop-vmstat-iostat", "alerting-concepts-and-thresholds"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#what-to-monitor"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "20-monitoring/top-htop-vmstat-iostat"
prevTopic: "19-security/security-workflows-for-soc-pentest-roles"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["what to monitor on a linux server", "metrics vs logs vs alerts", "cpu memory disk network monitoring", "monitoring fundamentals"]
canonicalUrl: "/docs/monitoring/monitoring-fundamentals"
---

# Monitoring Fundamentals: What to Watch and Why

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** Monitor four resource dimensions — CPU, memory, disk, network — plus application-level
> health. Metrics (numbers over time), logs ([Module 16](../16-logs/index.md)), and alerts
> (thresholds that page someone) are three distinct, complementary tools, not interchangeable
> terms.

## What is it?

The practice of continuously observing a system's resource usage and health so problems are
caught proactively — before a user reports them, and ideally before they cause an outage at all.

## Why does it exist?

A system with no monitoring only reveals problems when something breaks visibly — often too late
to prevent user impact. Monitoring exists to surface the trend *toward* a problem (rising memory
usage, growing disk consumption, increasing latency) while there's still time to act, and to
provide the data needed to understand an incident after the fact.

## Where is it used?

Every production system, but the discipline scales from a single server checked manually
([Module 10's](../10-processes/index.md) `top`/`ps`) to fleets of thousands monitored by dedicated
stacks ([Monitoring Stacks Overview](monitoring-stacks-overview.md)) — the underlying resource
dimensions being watched don't change, only the tooling and scale do.

## How it works

> 📊 Diagram: four resource gauges (CPU, memory, disk, network) feeding into a shared timeline,
> with three labeled output paths branching off it — "metrics" (numeric time series, queryable and
> graphable), "logs" (discrete recorded events, [Module 16](../16-logs/index.md)), and "alerts"
> (a threshold crossed on any of the above, triggering notification) — visually distinguishing
> three related but distinct concepts.

**The four core resource dimensions:**

| Dimension | What it measures | Symptom when exhausted |
|---|---|---|
| CPU | Processing capacity in use | Slow response times, request queuing |
| Memory | RAM (and swap) in use | Out-of-memory kills, swapping, thrashing |
| Disk | Storage space and I/O throughput | Write failures (space), slow reads/writes (I/O) |
| Network | Bandwidth and connection counts | Timeouts, dropped connections, latency |

Application-level health (is the specific service actually responding correctly, not just "is the
server up") is a fifth dimension layered on top, closer to
[Module 16's](../16-logs/reading-logs-during-a-production-incident.md) incident-investigation
skills than raw resource monitoring.

**Metrics vs. logs vs. alerts — three distinct concepts:**

- **Metrics** — numeric values sampled over time (CPU percentage, memory used, requests per
  second) — good for trends and graphing, not for explaining *why* something happened.
- **Logs** — discrete recorded events with context ([Module 16](../16-logs/index.md)) — good for
  explaining *why*, not efficient for spotting a slow trend across thousands of data points.
- **Alerts** — a rule that fires when a metric crosses a threshold ([Alerting Concepts and
  Thresholds](alerting-concepts-and-thresholds.md)) — the mechanism that turns a metric into a
  notification a human actually sees.

## Real-world example

A server's memory usage climbs steadily over two weeks due to a slow leak in a long-running
process, with no single dramatic event to notice. Without metrics tracked over time, this trend is
invisible — the server "just" runs out of memory and crashes one day, seemingly out of nowhere. A
basic memory metric graphed over the same two weeks would have shown the steady climb clearly,
turning a surprise outage into a proactively-scheduled restart or fix days in advance.

## Syntax

No single syntax — this page is conceptual; the next page introduces the first tools.

## Commands

No command example on this page — see [top, htop, vmstat, iostat](top-htop-vmstat-iostat.md) for
the first ones.

## Production example

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           15Gi        12Gi       512Mi        45Mi       2.5Gi       2.8Gi
```

A single-point-in-time snapshot (Module 15/21) — useful, but exactly the kind of data that matters
far more as a *trend over time* than as one isolated reading, which is precisely the gap
monitoring tooling fills.

## Do / Don't

| Do | Don't |
|---|---|
| Track metrics as trends over time, not just isolated snapshots | Rely solely on a single point-in-time check to judge system health |
| Treat metrics, logs, and alerts as complementary, distinct tools | Use the terms "metrics," "logs," and "alerts" interchangeably |
| Monitor all four core resource dimensions, not just the one that failed last time | Focus monitoring only on whichever resource caused the most recent incident |

## Common mistakes

- Monitoring only the resource that caused the last incident, leaving the other three dimensions
  unwatched until they cause the next one.
- Treating a single manual check (`top` once) as equivalent to actual trend monitoring over time.
- Confusing metrics, logs, and alerts as interchangeable, when each answers a different question
  (what's the trend / what happened / who needs to know right now).

## Best practices

- Monitor all four resource dimensions (CPU, memory, disk, network) as a baseline on every
  production system, regardless of which one has caused problems historically.
- Track metrics as continuous trends, not one-off checks, so gradual problems (slow leaks, growing
  disk usage) are visible before they become outages.
- Keep metrics, logs, and alerts working together: metrics show the trend, an alert notifies a
  human when a threshold is crossed, and logs explain why once someone is looking.

## Exercises

1. List the four core resource dimensions this page introduces and one symptom of each being
   exhausted.
2. Explain the difference between a metric and a log in your own words.
3. Describe why a slow memory leak is a good example of why trend monitoring matters more than a
   single snapshot.

## Quiz

**Q: What are the four core resource dimensions to monitor on any Linux server?**
<details><summary>Show answer</summary>
CPU, memory, disk, and network — each with its own exhaustion symptoms (slow response, OOM kills,
write failures, dropped connections respectively).
</details>

**Q: What's the difference between a metric and an alert?**
<details><summary>Show answer</summary>
A metric is a numeric value tracked over time; an alert is a rule that fires a notification when a
metric crosses a defined threshold — the alert acts on the metric, they aren't the same thing.
</details>

**Q: Why is a single point-in-time check insufficient for catching a slow resource leak?**
<details><summary>Show answer</summary>
A slow leak only becomes visible as a trend over time — one isolated snapshot can't distinguish
normal usage from a steadily worsening problem.
</details>

## Interview questions

- What's the difference between metrics, logs, and alerts? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Monitor four core resource dimensions: CPU, memory, disk, network — plus application-level
  health.
- Metrics, logs, and alerts are distinct, complementary tools, not interchangeable terms.
- Trend monitoring over time catches gradual problems that a single snapshot cannot.
- This page sets up the vocabulary the rest of this module (tools, stacks, alerting, log-based
  monitoring) builds on.

## Related topics

- [top, htop, vmstat, iostat](top-htop-vmstat-iostat.md)
- [Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md)
