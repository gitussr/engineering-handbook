---
title: "Alerting Concepts and Thresholds"
description: "Turning a metric into a notification a human actually acts on — static vs. dynamic thresholds, alert fatigue, and severity levels."
module: "20-monitoring"
moduleTitle: "Monitoring"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["20-monitoring/setting-up-a-basic-monitoring-agent"]
relatedTopics: ["setting-up-a-basic-monitoring-agent", "log-based-monitoring"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#alert-fatigue"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "20-monitoring/log-based-monitoring"
prevTopic: "20-monitoring/setting-up-a-basic-monitoring-agent"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["alert fatigue", "static vs dynamic threshold monitoring", "alert severity levels", "how to set monitoring thresholds"]
canonicalUrl: "/docs/monitoring/alerting-concepts-and-thresholds"
---

# Alerting Concepts and Thresholds

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** An alert fires when a metric crosses a defined threshold. Set thresholds deliberately
> — too sensitive causes alert fatigue (real alerts get ignored among noise), too loose misses
> real problems. Severity levels route alerts appropriately instead of treating everything as
> equally urgent.

## What is it?

The rules and practices that turn a raw metric ([Monitoring Fundamentals](monitoring-fundamentals.md))
into an actual notification a human sees and acts on — the mechanism that closes the loop between
"a problem exists in the data" and "someone knows about it."

## Why does it exist?

Metrics collected but never alerted on are only useful if someone happens to look at a dashboard
at exactly the right moment. Alerting exists to push relevant information to a human proactively,
the moment a defined condition is met — but done carelessly, it creates its own problem: too many
low-value alerts train people to ignore all of them, including the real ones.

## Where is it used?

Every production monitoring setup with an on-call rotation or team responsible for response —
alerting is the mechanism that actually pages someone, rather than requiring constant manual
dashboard-watching.

## How it works

> 📊 Diagram: a metric's value over time crossing a horizontal threshold line, triggering an
> alert at the crossing point — shown twice: once with a well-tuned threshold (crossing correlates
> with a real problem) and once with an overly sensitive threshold (the same metric's normal noisy
> fluctuation repeatedly crosses it, generating alerts that don't correspond to real problems) —
> visually motivating why threshold tuning matters.

**Static vs. dynamic thresholds:**

- **Static threshold** — a fixed value (e.g. "alert if memory usage exceeds 90%"). Simple to
  understand and configure, but can be wrong for a system whose normal baseline varies (a batch
  job that legitimately spikes CPU nightly).
- **Dynamic threshold** — based on historical patterns or statistical deviation from a baseline
  (e.g. "alert if usage is significantly higher than this time last week"), better suited to
  systems with predictable, varying load patterns, at the cost of more complex configuration.

**Alert fatigue — the central failure mode to design against:** an alerting setup with too many
low-value or frequently-false alerts trains responders to skim or ignore notifications, including
the rare genuinely critical one buried among the noise. This is widely considered a more dangerous
failure mode than under-alerting, because it silently defeats the entire point of having alerting
in the first place.

**Severity levels** — routing alerts by actual urgency rather than treating every threshold
breach identically:

| Severity | Typical response | Example |
|---|---|---|
| Critical | Immediate page, wakes someone up | Production service down |
| Warning | Visible during business hours, not urgent overnight | Disk usage approaching capacity, days of runway left |
| Info | Logged/dashboarded, no notification | A routine, expected event worth recording but not acting on |

## Real-world example

A team sets a memory alert threshold at 70%, which turns out to be well within normal operating
range for their workload — the alert fires dozens of times a day, and within a week, the on-call
engineer has started reflexively dismissing it without checking. When an actual memory leak later
pushes usage to a genuinely dangerous 95%, the alert fires alongside the usual noise and goes
unnoticed for hours — a direct consequence of alert fatigue from a poorly-tuned threshold, not a
failure of the underlying monitoring stack.

## Syntax

No single syntax — alerting rule configuration is specific to each monitoring stack
([Monitoring Stacks Overview](monitoring-stacks-overview.md)); this page covers the underlying
concepts common to all of them.

## Commands

No command example on this page — alerting is configured within a monitoring stack's own
interface, not via Linux shell commands directly.

## Production example

```
# Example Prometheus alerting rule (conceptual, not a Linux command)
- alert: HighMemoryUsage
  expr: node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes < 0.10
  for: 10m
  labels:
    severity: warning
```

A threshold (`< 0.10` available) combined with a duration requirement (`for: 10m`) — firing only
if the condition persists for ten minutes, not on a single momentary blip, directly addressing the
alert-fatigue risk from a too-sensitive, instant-trigger threshold.

## Do / Don't

| Do | Don't |
|---|---|
| Require a condition to persist before alerting (a duration, not an instant trigger) | Alert on every single momentary threshold crossing |
| Assign severity levels matching actual required urgency | Treat every alert as equally critical |
| Tune thresholds based on a system's real, observed baseline | Copy a threshold value from another system without validating it fits this one |

## Common mistakes

- Setting a threshold that fires on normal, expected fluctuation, training responders to ignore
  alerts through repeated false positives.
- Alerting on an instantaneous crossing instead of requiring the condition to persist, causing
  noisy alerts from momentary, self-resolving blips.
- Treating every alert as equally urgent, causing responders to lose the ability to distinguish a
  genuine emergency from routine noise.

## Best practices

- Require a threshold breach to persist for a meaningful duration before alerting, not fire on a
  single instantaneous sample.
- Regularly review and tune thresholds against real observed system behavior, rather than setting
  them once and never revisiting them.
- Use severity levels deliberately so the response (immediate page vs. business-hours review vs.
  no notification at all) matches actual urgency.

## Exercises

1. Explain the difference between a static and a dynamic threshold, with an example of when each
   is appropriate.
2. Describe what alert fatigue is and why it's considered more dangerous than under-alerting.
3. Design a severity scheme (critical/warning/info) for a hypothetical web application's key
   metrics.

## Quiz

**Q: What is alert fatigue, and why is it dangerous?**
<details><summary>Show answer</summary>
It's the effect of too many low-value or false alerts training responders to ignore notifications
— dangerous because it can cause a genuinely critical alert to go unnoticed among the noise.
</details>

**Q: Why require a threshold breach to persist for a duration before alerting?**
<details><summary>Show answer</summary>
To avoid firing on momentary, self-resolving blips that don't represent a real, ongoing problem —
requiring persistence filters out noise that would otherwise contribute to alert fatigue.
</details>

**Q: What's the difference between a static and a dynamic threshold?**
<details><summary>Show answer</summary>
A static threshold is a fixed value regardless of context; a dynamic threshold adjusts based on
historical patterns or statistical deviation from a baseline, better suited to systems with
naturally varying load.
</details>

## Interview questions

- What is alert fatigue, and how would you design an alerting system to avoid it? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Alerting turns a metric threshold breach into an actual notification a human acts on.
- Alert fatigue — too many low-value alerts — is a more dangerous failure mode than
  under-alerting, since it can bury genuinely critical alerts.
- Require thresholds to persist over a duration, not fire on instantaneous blips.
- Severity levels route alerts by actual urgency, rather than treating every breach identically.

## Related topics

- [Setting Up a Basic Monitoring Agent](setting-up-a-basic-monitoring-agent.md)
- [Log-Based Monitoring](log-based-monitoring.md)
