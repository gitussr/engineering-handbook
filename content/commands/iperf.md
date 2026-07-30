---
title: "iperf — Measure Network Throughput"
description: "Actively generate traffic between two hosts to measure real achievable bandwidth, distinct from ping's latency-only check."
relatedConcepts: ["21-performance/network-performance-iperf-sar", "21-performance/benchmarking-tools"]
relatedCommands: ["ping", "sar"]
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
difficulty: "expert"
compatibility: [{"context": "iperf3", "note": "The modern, actively maintained version; most current usage and documentation refers to iperf3 specifically, which is not fully command-line compatible with the original iperf2."}]
updatedAt: "2026-07-28"
keywords: ["iperf3 examples", "iperf3 server client", "measure bandwidth between two servers", "iperf throughput test"]
canonicalUrl: "/commands/iperf"
---

# iperf

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** `iperf3 -s` on one host, `iperf3 -c SERVER_IP` on the other — actively measures real
> achievable throughput between them, something `ping`'s latency check can never reveal.

## Purpose

`iperf` (in its current form, `iperf3`) actively measures achievable network throughput between
two hosts — see
[Network Performance: iperf, sar](../docs/21-performance/network-performance-iperf-sar.md) for the
full concept.

## Syntax

```
iperf3 -s
iperf3 -c SERVER_IP [OPTIONS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SERVER_IP` | The address of the host running `iperf3 -s`, for the client to connect to | Yes, for `-c` |

## Options

| Flag | Meaning |
|---|---|
| `-s` | Run in server mode, listening for a client connection |
| `-c SERVER_IP` | Run in client mode, connecting to and testing against a server |
| `-t SECONDS` | Test duration (default 10 seconds) |
| `-P N` | Run N parallel streams, useful for testing aggregate multi-connection throughput |
| `-u` | Use UDP instead of the default TCP |
| `-R` | Reverse mode — measure server-to-client throughput instead of client-to-server |

## Examples

```
$ iperf3 -s
```
Start listening as a server, waiting for a client to connect and test against.

```
$ iperf3 -c 203.0.113.10
```
Run a 10-second throughput test against a server at that address.

```
$ iperf3 -c 203.0.113.10 -t 30 -P 4
```
Run a longer, 30-second test using 4 parallel streams, testing aggregate throughput.

```
$ iperf3 -c 203.0.113.10 -R
```
Test throughput in the reverse direction (server to client).

## Expected Output

```
$ iperf3 -c 203.0.113.10
Connecting to host 203.0.113.10, port 5201
[  5] local 10.0.0.5 port 44982 connected to 203.0.113.10 port 5201
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.00  sec  1.10 GBytes   943 Mbits/sec                  sender
[  5]   0.00-10.00  sec  1.09 GBytes   937 Mbits/sec                  receiver
```

## Exit Status

`0` on a completed test, non-zero if the connection fails or the server is unreachable.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Connection refused` | No `iperf3 -s` server listening on the target host | Start the server side first (`iperf3 -s`) |
| Firewall blocks the connection | Default port (5201) not open between the two hosts | Open the port via [firewall configuration](../docs/19-security/firewall-configuration-in-depth.md) — scoped to the specific testing host only |
| Result far lower than expected link capacity | A genuinely constrained link, or single-stream limitations | Retry with `-P N` for multiple parallel streams before concluding the link itself is the problem |

## Security Considerations

Only run an `iperf3` server temporarily and on networks you control — leaving one listening
indefinitely on an exposed interface is unnecessary attack surface. Scope firewall access
tightly to the specific testing host, per
[Module 19's](../docs/19-security/firewall-configuration-in-depth.md) least-privilege patterns.

## Performance Considerations

`iperf3` deliberately saturates the link being tested to measure its real capacity — running it
against a production path during peak load will itself add real, measurable congestion; schedule
tests deliberately.

## Production Usage

`iperf3` is the standard way to validate that a newly-provisioned network path (a new cloud
inter-region link, a new VPN tunnel) actually delivers its expected bandwidth before relying on it
for production traffic.

## Related Commands

- [`ping`](ping.md) — confirms reachability and latency only, not throughput (see
  [Module 12](../docs/12-networking/ping-traceroute-mtr.md))
- [`sar`](sar.md) — historical network statistics, complementary to `iperf3`'s active,
  point-in-time measurement

## Related Concepts

- [Network Performance: iperf, sar](../docs/21-performance/network-performance-iperf-sar.md)
- [Benchmarking Tools](../docs/21-performance/benchmarking-tools.md)
