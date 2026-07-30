---
title: "Centralized Logging Concepts (ELK, Grafana Loki)"
description: "Why logs need to leave the machine that produced them once a system spans more than a handful of servers — the shift from local files to a shipped, searchable, aggregated store."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["16-logs/logrotate"]
relatedTopics: ["reading-logs-during-a-production-incident"]
relatedCommands: []
careerRelevance: ["devops", "sre", "platform", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#centralized-logging-why"]
relatedCheatsheet: ""
furtherReading: [{"label": "Elastic Stack (ELK) overview", "url": "https://www.elastic.co/what-is/elk-stack"}, {"label": "Grafana Loki overview", "url": "https://grafana.com/oss/loki/"}]
nextTopic: "16-logs/reading-logs-during-a-production-incident"
prevTopic: "16-logs/logrotate"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["elk stack explained", "grafana loki explained", "centralized logging linux", "log aggregation concepts"]
canonicalUrl: "/docs/logs/centralized-logging-concepts"
---

# Centralized Logging Concepts (ELK, Grafana Loki)

🔴 Expert · Relevant for: DevOps · SRE · Platform · Cloud

> **TL;DR:** Everything covered so far in this module — the journal, `/var/log`, syslog — lives
> on one machine. Centralized logging ships logs off every machine into one searchable,
> aggregated store, because SSHing into dozens (or thousands) of servers individually to
> `grep` for one error doesn't scale.

## What is it?

The practice — and the tooling ecosystem (ELK: Elasticsearch, Logstash, Kibana; or Grafana Loki
and similar) — of shipping logs from every machine in a fleet to one centralized, searchable
store, instead of leaving them local to each machine the way every earlier topic in this module
has assumed.

## Why does it exist?

Every technique covered so far in this module — `journalctl`, reading `/var/log`, `rsyslog`
routing — works on a single machine. That breaks down completely once a system spans many
servers: an error might touch five different services on five different machines, and manually
SSHing into each one to search local logs doesn't scale and makes correlating events across
machines nearly impossible. Centralized logging solves exactly that.

## Where is it used?

Any environment beyond a handful of servers — which in practice means most production DevOps,
SRE, and platform environments — where logs from many machines need to be searched, correlated,
and retained centrally rather than per-machine.

## How it works

> 📊 Diagram: several servers each running a lightweight log-shipping agent, all forwarding logs
> to one central aggregation/storage/search layer, with a single dashboard querying across every
> machine's logs at once — contrasted with the per-machine model every earlier topic in this
> module assumed.

| Component (ELK-style) | Role |
|---|---|
| Shipping agent (e.g. Filebeat, Logstash) | Runs on each machine, forwards logs centrally |
| Storage/search engine (Elasticsearch) | Indexes and stores logs for fast search |
| Visualization (Kibana) | Dashboards and search UI over the aggregated data |

**Grafana Loki takes a different, lighter-weight approach**: rather than indexing full log
content (as Elasticsearch does), Loki indexes only metadata (labels) and stores raw log content
cheaply, trading some search flexibility for significantly lower resource cost — a deliberate
design choice, not a lesser version of the same idea.

**The conceptual shift this topic represents**: everything before this page in the module assumed
"the logs are on this machine" — centralized logging is the recognition that this assumption
breaks down at scale, and a fleet-wide search/correlation layer becomes necessary.

## Real-world example

A request fails somewhere in a system spanning a load balancer, three application servers, and a
database, all on separate machines. Without centralized logging, diagnosing this means SSHing
into each machine individually and manually correlating timestamps across each one's local logs —
slow and error-prone. With centralized logging, one search across the aggregated store, filtered
by a request ID or timestamp window, surfaces the relevant log lines from every machine involved
in a single view.

## Commands

No new command — ELK and Loki are external systems/services with their own dashboards and query
languages, not Linux shell commands; this module's earlier topics (`journalctl`, `/var/log`,
`rsyslog`) are what typically feed data into them.

## Production example

Conceptual — a typical flow: `rsyslog` (or a lightweight shipping agent) on each server forwards
logs to a central Loki or Elasticsearch instance; an engineer then queries across the entire
fleet from one dashboard, rather than connecting to any individual machine.

## Do / Don't

| Do | Don't |
|---|---|
| Adopt centralized logging once a system spans more than a handful of machines | Rely on per-machine SSH-and-grep once the fleet has grown significantly |
| Choose a tool (ELK, Loki, or similar) based on actual query needs versus resource cost | Assume every centralized logging tool makes the same indexing tradeoff |
| Use centralized logging for cross-machine correlation, not just storage | Treat centralization as only a bigger disk for logs, missing the correlation benefit |

## Common mistakes

- Continuing to rely on individually SSHing into each server to check logs long after the fleet
  has grown past the point that scales reasonably.
- Assuming ELK and Loki are interchangeable, missing that they make genuinely different tradeoffs
  (full content indexing vs. label-based, lighter-weight indexing).
- Treating centralized logging purely as consolidated storage, missing its main value: correlating
  events across multiple machines in one search.

## Best practices

- Introduce centralized logging before fleet size makes per-machine investigation genuinely
  painful, not after.
- Choose ELK-style full indexing when rich, flexible search matters most; choose Loki-style
  label-based indexing when resource cost and simplicity matter more.
- Design log messages (structured fields, consistent identifiers like a request ID) with
  cross-machine correlation in mind from the start.

## Exercises

1. Explain in one sentence why centralized logging becomes necessary as a fleet grows.
2. Describe the key tradeoff between an ELK-style approach and Grafana Loki's approach.
3. Explain how a request ID included in log messages helps correlate an issue across multiple
   machines.

## Quiz

**Q: Why does per-machine log checking (SSH + grep) stop working well as a fleet grows?**
<details><summary>Show answer</summary>
Correlating an issue that touches multiple machines requires manually checking each one
individually and cross-referencing timestamps — slow, error-prone, and increasingly impractical
as the number of machines grows.
</details>

**Q: What's the key tradeoff Grafana Loki makes compared to an ELK-style approach?**
<details><summary>Show answer</summary>
Loki indexes only metadata/labels (not full log content), trading some search flexibility for
significantly lower resource cost compared to Elasticsearch's full-content indexing.
</details>

## Interview questions

- Why does centralized logging become necessary once a system spans many servers? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Centralized logging ships logs from every machine into one searchable, aggregated store.
- It exists because per-machine investigation (SSH + grep) doesn't scale past a handful of
  servers.
- ELK (full-content indexing) and Loki (label-based, lighter-weight indexing) make different
  resource/flexibility tradeoffs.
- The core value is cross-machine correlation, not just consolidated storage.

## Related topics

- [Reading Logs During a Production Incident](reading-logs-during-a-production-incident.md)
- [logrotate](logrotate.md)
