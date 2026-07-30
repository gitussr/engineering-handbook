---
title: "ping — Test Network Reachability"
description: "Send ICMP echo requests to a host to confirm reachability and measure round-trip latency."
relatedConcepts: ["12-networking/ping-traceroute-mtr"]
relatedCommands: ["traceroute"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: [{"context": "GNU/iputils (Linux)", "note": "Runs indefinitely by default unless -c is given; some other platforms default to a fixed count instead."}]
updatedAt: "2026-07-27"
keywords: ["ping command linux", "ping -c", "ping packet loss", "test host reachability"]
canonicalUrl: "/commands/ping"
---

# ping

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `ping -c 4 host` sends four ICMP echo requests and reports round-trip time and
> packet loss — the fastest first check for "is this reachable at all?"

## Purpose

`ping` tests basic network reachability and latency to a host — see
[ping, traceroute, mtr](../docs/12-networking/ping-traceroute-mtr.md) for how it fits alongside
`traceroute` and `mtr`.

## Syntax

```
ping [OPTIONS] HOST
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `HOST` | Hostname or IP address to test | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-c N` | Send exactly N packets, then stop (without it, runs until interrupted) |
| `-i SECONDS` | Interval between packets |
| `-W SECONDS` | Timeout to wait for a reply |
| `-4` / `-6` | Force IPv4 or IPv6 |

## Examples

```
$ ping -c 4 example.com
```
Send 4 packets and stop, reporting a summary.

```
$ ping -c 4 -i 0.5 example.com
```
Send 4 packets at 0.5-second intervals.

```
$ ping 192.168.1.1
```
Test reachability of a raw IP address directly (useful for isolating DNS as a cause — see
[Networking Fundamentals](../docs/12-networking/networking-fundamentals.md)).

## Expected Output

```
$ ping -c 4 example.com
64 bytes from example.com (93.184.216.34): icmp_seq=1 ttl=56 time=11.2 ms
64 bytes from example.com (93.184.216.34): icmp_seq=2 ttl=56 time=10.8 ms
64 bytes from example.com (93.184.216.34): icmp_seq=3 ttl=56 time=11.0 ms
64 bytes from example.com (93.184.216.34): icmp_seq=4 ttl=56 time=10.9 ms

--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss
```

## Exit Status

`0` if at least one reply was received, non-zero if the host was unreachable or an argument was
invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Name or service not known` | Hostname doesn't resolve | Check DNS — try `ping` against a raw IP to confirm this is a DNS issue |
| `Destination Host Unreachable` | No route to the target from this machine | Check `ip route show` for a valid route |
| `100% packet loss` with no error | Reachable at the IP layer but not responding to ICMP, or genuinely down | Some hosts/firewalls deliberately block ICMP — this alone doesn't prove the host is down |

## Security Considerations

Some hosts and firewalls deliberately block ICMP echo requests as a hardening measure — a failed
`ping` does not always mean a host or service is actually down; other protocols may still be
reachable.

## Performance Considerations

Lightweight — a handful of small ICMP packets. Never run `ping` with no `-c` limit in an
automated script; it will run indefinitely.

## Compatibility Notes

Some platforms' `ping` defaults to a fixed number of packets automatically; GNU/iputils `ping`
(the Linux default) runs until interrupted (Ctrl+C) unless `-c` is given.

## Production Usage

`ping` against a raw IP address (not a hostname) is the standard first move to isolate whether a
"can't reach it" report is a DNS problem or an actual connectivity/routing problem.

## Related Commands

- [`traceroute`](traceroute.md) — shows the hop-by-hop path when `ping` fails or is slow

## Related Concepts

- [ping, traceroute, mtr](../docs/12-networking/ping-traceroute-mtr.md)
