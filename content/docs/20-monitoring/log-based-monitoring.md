---
title: "Log-Based Monitoring"
description: "Deriving metrics and alerts from log content itself — error-rate counting, pattern matching — for problems that never show up as a resource-usage number."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/alerting-concepts-and-thresholds"]
relatedTopics: ["alerting-concepts-and-thresholds"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#log-based-vs-metric-based-monitoring"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/performance-tuning-overview"
prevTopic: "20-monitoring/alerting-concepts-and-thresholds"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["log based monitoring", "error rate monitoring from logs", "log pattern alerting", "monitoring application errors via logs"]
canonicalUrl: "/docs/monitoring/log-based-monitoring"
---

# Log-Based Monitoring

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** Some problems never show up as a CPU/memory/disk number — a rising rate of
> application errors, a specific failure message repeating. Log-based monitoring derives metrics
> and alerts directly from log content, closing the gap resource monitoring alone can't cover.

## What is it?

Deriving metrics and alerts from the *content* of log entries — counting how often a pattern
appears, tracking an error rate over time — rather than only from system resource numbers
([Monitoring Fundamentals](monitoring-fundamentals.md)).

## Why does it exist?

A resource-based metric can look perfectly healthy (normal CPU, normal memory) while an
application is actively failing every request with a specific error — resource usage and
application correctness are different questions entirely. Log-based monitoring exists to catch
exactly this class of problem, connecting this module back to
[Module 16's](../16-logs/index.md) logging fundamentals and
[centralized logging](../16-logs/centralized-logging-concepts.md).

## Where is it used?

Tracking application-level error rates, watching for a specific known failure signature that
resource metrics would never surface, and — at fleet scale — feeding centralized log platforms
([Module 16](../16-logs/centralized-logging-concepts.md)) into the same alerting pipeline as
resource metrics.

## How it works

> 📊 Diagram: two parallel pipelines feeding the same alerting layer — the resource-metrics
> pipeline (CPU/memory/disk numbers, this module's earlier pages) and a log-based pipeline
> (log lines matched against a pattern, counted into a rate over time) — both converging on the
> same threshold/severity mechanism from
> [Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md), emphasizing that
> log-based monitoring uses the same alerting discipline, just a different data source.

**The core technique:** count occurrences of a log pattern over a time window, turning
unstructured log text into a numeric rate exactly like a resource metric:

```
error rate = (count of ERROR-level lines in last 5 minutes) / (total requests in last 5 minutes)
```

This rate can then be alerted on using the exact same threshold/duration/severity discipline from
[Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md) — log-based monitoring
isn't a separate alerting mechanism, just a different data source feeding the same one.

**Pattern-based alerting** — beyond a rate, alerting on the mere *presence* of a specific known-bad
log line at all (e.g. a specific fatal exception signature that should never occur), regardless of
frequency — a single occurrence may warrant immediate attention even if it's not yet a
meaningful "rate."

**Centralized logging as the practical prerequisite:** deriving a fleet-wide error rate requires
log data from every host in one searchable place — exactly what
[Module 16's centralized logging](../16-logs/centralized-logging-concepts.md) (ELK, Grafana Loki)
provides. Log-based monitoring on a single host's local logs works the same way in principle, but
doesn't scale to a fleet without that centralization already in place.

## Real-world example

A web application's server resource metrics (CPU, memory, disk) all look completely normal, giving
no indication anything is wrong — but a bug introduced in the latest deploy causes 30% of
checkout requests to fail with a specific exception. Resource-based monitoring alone would never
catch this; a log-based alert tracking the rate of that specific error message
(or a general application error-rate metric) crosses its threshold within minutes of the bad
deploy, catching a problem that would otherwise have gone unnoticed until enough users complained.

## Syntax

No single syntax — pattern matching and rate calculation are typically configured within a
centralized logging platform ([Module 16](../16-logs/centralized-logging-concepts.md)) or a log
shipper's own query language, not a standalone Linux command.

## Commands

No command example on this page — see
[Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md) for the last one in this
module; log-based monitoring is configured within centralized logging tooling rather than via new
Linux shell commands.

## Production example

```
# Conceptual query against a centralized log platform, not a Linux command
count(level="ERROR" AND service="checkout") over last 5m
  / count(*) over last 5m
  > 0.05  →  alert
```

An error-rate threshold expressed against aggregated log data — the same threshold/duration
concept from the previous page, applied to log content instead of a resource number.

## Do / Don't

| Do | Don't |
|---|---|
| Track error rates as a proportion of total activity, not raw counts alone | Alert on raw error counts without normalizing against traffic volume |
| Alert immediately on a known-bad pattern that should never occur, regardless of rate | Wait for a meaningful "rate" before acting on a clearly fatal, unexpected log line |
| Rely on centralized logging for fleet-wide log-based monitoring | Try to monitor error rates across a fleet from individual hosts' local logs |

## Common mistakes

- Alerting on raw error counts instead of a rate normalized against traffic volume, causing false
  alarms during naturally high-traffic periods and missed problems during low-traffic ones.
- Requiring a meaningful "rate" before alerting on a pattern that should genuinely never occur even
  once (a specific fatal, unexpected exception).
- Attempting fleet-wide log-based monitoring without centralized logging already in place, making
  the underlying data effectively unqueryable at scale.

## Best practices

- Normalize error counts against traffic volume (a rate, not a raw count) so alerts reflect actual
  severity relative to load.
- Reserve immediate, rate-independent alerting for genuinely known-bad patterns that should never
  occur under normal operation.
- Build log-based monitoring on top of centralized logging, not as a separate, disconnected
  per-host effort.

## Exercises

1. Explain why a raw error count, without normalizing against traffic volume, can be misleading.
2. Describe a log pattern in your own application/system context that would warrant immediate
   alerting regardless of frequency.
3. Explain how log-based monitoring depends on centralized logging at fleet scale.

## Quiz

**Q: Why is an error rate generally more useful than a raw error count for alerting?**
<details><summary>Show answer</summary>
A rate normalizes against traffic volume, so it reflects actual severity relative to load — a raw
count can be misleadingly high during naturally busy periods or misleadingly low during quiet
ones.
</details>

**Q: What kind of log pattern might warrant alerting regardless of how rarely it occurs?**
<details><summary>Show answer</summary>
A known-bad pattern that should genuinely never happen under normal operation — a specific fatal
exception, for example — where even a single occurrence is significant enough to warrant immediate
attention.
</details>

**Q: What does log-based monitoring depend on to work at fleet scale?**
<details><summary>Show answer</summary>
Centralized logging — deriving a fleet-wide error rate or pattern match requires log data from
every host aggregated into one searchable place.
</details>

## Interview questions

- Describe a scenario where resource metrics alone wouldn't catch a real production problem, but
  log-based monitoring would. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Log-based monitoring derives metrics and alerts from log content, catching problems resource
  metrics alone can't (application-level errors with normal-looking resource usage).
- Normalize error counts into rates against traffic volume, rather than alerting on raw counts.
- Some known-bad patterns warrant alerting on any occurrence, independent of rate.
- Log-based monitoring depends on centralized logging (Module 16) to work at fleet scale, and uses
  the same alerting discipline from this module's previous page.

## Related topics

- [Alerting Concepts and Thresholds](alerting-concepts-and-thresholds.md)
- [Module 21: Performance](../21-performance/index.md)
