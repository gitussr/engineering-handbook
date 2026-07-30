---
title: "Network Performance: iperf, sar"
description: "Actively measuring achievable throughput between two hosts with iperf, and reviewing historical system performance trends across every resource with sar."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["21-performance/disk-io-performance-iostat-iotop"]
relatedTopics: ["disk-io-performance-iostat-iotop", "kernel-tuning-sysctl"]
relatedCommands: ["iperf", "sar"]
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#iperf-vs-ping"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "21-performance/kernel-tuning-sysctl"
prevTopic: "21-performance/disk-io-performance-iostat-iotop"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["iperf3 examples", "sar historical performance data", "measure network throughput linux", "sar command examples"]
canonicalUrl: "/docs/performance/network-performance-iperf-sar"
---

# Network Performance: iperf, sar

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** `iperf3` actively measures the actual achievable throughput between two hosts — `ping`
> ([Module 12](../12-networking/ping-traceroute-mtr.md)) only confirms reachability and latency,
> not bandwidth. `sar` reviews historical performance trends across CPU, memory, disk, and network
> from data already being collected.

## What is it?

Two tools for network performance specifically: `iperf`, which actively generates traffic to
measure real achievable throughput between two hosts, and `sar`, which reports historical
system-wide performance trends (not limited to network) from continuously logged data.

## Why does it exist?

`ping`/`traceroute` ([Module 12](../12-networking/ping-traceroute-mtr.md)) confirm a host is
reachable and measure latency, but say nothing about actual achievable bandwidth between two
points — a link can have perfect latency and still be bandwidth-constrained. `iperf` exists
specifically to measure that. `sar` exists because live tools ([Module 20](../20-monitoring/index.md))
only show the present moment — investigating "was this slow yesterday too" needs historical data,
which `sar` (if configured to log continuously) already has.

## Where is it used?

Confirming whether a network link actually delivers its expected bandwidth (cloud provider
inter-region links, a new network configuration), and reviewing whether a currently-observed
performance issue is new or has a historical pattern (recurring at the same time each day, for
example).

## How it works

> 📊 Diagram: two hosts connected by a network link, with `iperf3` shown running a server process
> on one end and a client process on the other, actively generating and measuring a data stream
> between them (contrasted with `ping`'s single small packet round-trip) — alongside a separate
> panel showing `sar`'s historical log file being queried for a specific past time range, unlike
> every other tool in this module which only shows the current moment.

**`iperf3` — active throughput measurement, requires a listener on each end:**

```bash
# On the receiving host:
iperf3 -s

# On the sending host:
iperf3 -c SERVER_IP
```

```
Connecting to host 203.0.113.10, port 5201
[  5]   0.00-10.00  sec  1.10 GBytes   943 Mbits/sec
```

Unlike `ping`, this actually saturates the link temporarily to measure real achievable throughput
— informative, but not something to run against a production link without awareness that it
generates real load.

**`sar` — historical system performance data:**

```bash
sar -u          # CPU usage
sar -r          # memory usage
sar -d          # disk activity
sar -n DEV      # network statistics
sar -s 09:00:00 -e 10:00:00   # a specific time range from today's log
```

`sar` reads from data the `sysstat` package has already been logging continuously (commonly via a
cron job or systemd timer — [Module 17](../17-cron/index.md)) — meaning historical review is only
possible if this logging was already enabled *before* the period being investigated.

## Real-world example

A team suspects their application's slowness might be a recurring, predictable pattern (perhaps
tied to a nightly batch job) rather than a one-off event. `sar -u -s 02:00:00 -e 03:00:00`
retrieves CPU usage specifically from that window on a previous day, confirming a recurring spike
that lines up exactly with a scheduled job — a historical pattern invisible to any live-only tool
from this module or [Module 20](../20-monitoring/index.md), since none of them retain data before
the moment they're run, unlike `sar`'s continuously logged history.

## Syntax

```
iperf3 -s
iperf3 -c SERVER_IP
sar [-u|-r|-d|-n DEV] [-s TIME -e TIME]
```

## Commands

See [`iperf`](../../commands/iperf.md) and [`sar`](../../commands/sar.md) for full references.

## Production example

```
$ sar -u -s 14:00:00 -e 14:30:00
14:00:01        CPU     %user     %system   %iowait     %idle
14:05:01        all     72.30       8.20       2.10      17.40
14:10:01        all     68.50       7.90       1.80      21.80
14:15:01        all     91.20      12.30       3.50       2.00
14:20:01        all     45.00       5.10       0.90      49.00
```

A historical view showing a clear CPU spike at 14:15 — data that would be completely unavailable
without `sar` already logging continuously before this investigation began.

## Do / Don't

| Do | Don't |
|---|---|
| Use `iperf3` to measure actual achievable bandwidth, not just latency | Assume good `ping` results mean bandwidth is also fine |
| Ensure `sar`'s continuous logging is enabled before an incident, not after | Assume historical data will be available on demand without having enabled logging in advance |
| Run `iperf3` tests deliberately, aware they generate real load | Run bandwidth tests against a production link without considering the load they add |

## Common mistakes

- Assuming good `ping` latency means bandwidth is also fine — the two are unrelated; only `iperf3`
  actually measures throughput.
- Only realizing `sar`'s historical logging wasn't enabled after already needing the data it would
  have provided.
- Running `iperf3` against a live production link without considering the real traffic load it
  temporarily generates.

## Best practices

- Enable `sar`'s continuous data collection (via `sysstat`'s scheduled collection job) proactively
  on every production system, so historical data is available if it's ever needed — enabling it
  reactively after an incident is too late for that specific incident.
- Run `iperf3` tests during planned windows, or against non-production paths, given the real load
  they generate.
- Use `sar`'s different flags (`-u`, `-r`, `-d`, `-n DEV`) to review the same historical window
  across every resource dimension, not just one.

## Exercises

1. Explain why good `ping` latency doesn't guarantee good bandwidth between two hosts.
2. Describe what would happen if you tried to review yesterday's CPU usage with `sar`, but
   `sysstat`'s data collection had never been enabled.
3. Run `iperf3 -s` on one host and `iperf3 -c` from another (or explain the setup if you don't have
   two hosts available) and interpret the throughput result.

## Quiz

**Q: What does iperf3 measure that ping/traceroute don't?**
<details><summary>Show answer</summary>
Actual achievable throughput/bandwidth between two hosts — `ping`/`traceroute` measure reachability
and latency, not bandwidth.
</details>

**Q: Can sar show historical data from before it was run for the first time?**
<details><summary>Show answer</summary>
No — `sar` can only report on data that was already being continuously logged (typically via
`sysstat`'s scheduled collection); it can't retroactively produce historical data for a period
before logging was enabled.
</details>

**Q: Why should iperf3 tests be run deliberately rather than casually against a production link?**
<details><summary>Show answer</summary>
It actively generates substantial real traffic to measure throughput, temporarily consuming real
bandwidth — not a passive, low-impact check like `ping`.
</details>

## Interview questions

- How would you determine whether a network performance issue is new or has happened before? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `iperf3` actively measures real achievable throughput, distinct from `ping`'s latency/reachability
  check.
- `sar` reports historical performance trends across CPU, memory, disk, and network — but only for
  periods already being logged.
- Enable `sar`'s continuous data collection proactively — it can't be retroactively enabled for
  past incidents.
- Run `iperf3` deliberately, aware of the real load it generates on the link being tested.

## Related topics

- [Disk I/O Performance: iostat, iotop](disk-io-performance-iostat-iotop.md)
- [Kernel Tuning: sysctl](kernel-tuning-sysctl.md)
