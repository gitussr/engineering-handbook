---
title: "traceroute — Show the Hop-by-Hop Path to a Host"
description: "Map the route packets take to a destination, one router at a time, to localize where connectivity breaks down."
relatedConcepts: ["12-networking/ping-traceroute-mtr"]
relatedCommands: ["ping"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: [{"context": "Linux traceroute", "note": "May need separate installation (traceroute package) on minimal distro installs; some distros alias it to tracepath by default."}]
updatedAt: "2026-07-27"
keywords: ["traceroute command linux", "traceroute explained", "hop by hop network path", "traceroute asterisks meaning"]
canonicalUrl: "/commands/traceroute"
---

# traceroute

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `traceroute host` shows every router hop between your machine and the destination,
> one line per hop — the tool for localizing *where* a path breaks down, not just whether it does.

## Purpose

`traceroute` maps the hop-by-hop network path to a destination — see
[ping, traceroute, mtr](../docs/12-networking/ping-traceroute-mtr.md) for how it complements
`ping` and `mtr`.

## Syntax

```
traceroute [OPTIONS] HOST
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `HOST` | Hostname or IP address to trace the path to | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-n` | Numeric output only, skip reverse-DNS lookups (faster) |
| `-m MAX_HOPS` | Maximum number of hops to probe before giving up |
| `-w SECONDS` | Timeout to wait per probe |

## Examples

```
$ traceroute example.com
```
Show the path to `example.com`, hop by hop.

```
$ traceroute -n example.com
```
Same, but skip reverse-DNS lookups on each hop (much faster).

```
$ traceroute -m 15 example.com
```
Stop after 15 hops even if the destination hasn't been reached yet.

## Expected Output

```
$ traceroute example.com
 1  192.168.1.1 (192.168.1.1)  1.123 ms
 2  10.10.0.1 (10.10.0.1)  8.456 ms
 3  * * *
 4  93.184.216.34 (93.184.216.34)  11.203 ms
```

Each numbered line is one hop; `* * *` means that hop didn't respond to the probe (not
necessarily a failure — see Common Errors).

## Exit Status

`0` if the trace completed (reached the destination or the max hop count), non-zero on invalid
arguments or an unresolvable hostname.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `* * *` at a hop | That router doesn't respond to probes (common, often by policy) | Not necessarily a problem — check whether later hops still respond |
| `* * *` at every hop from a point onward, trace never completes | Genuine path breakdown, or a firewall dropping the probe protocol entirely | Confirm with `ping` to the same destination, and check firewall rules |
| Very slow output | Reverse-DNS lookup on each hop | Add `-n` to skip name resolution |

## Security Considerations

Some firewalls and routers deliberately drop or rate-limit `traceroute` probes as a hardening
measure — an incomplete trace doesn't always mean the destination itself is unreachable by other
means.

## Performance Considerations

Slower than `ping` since it sends multiple probes with increasing TTL per hop; add `-n` to skip
reverse-DNS lookups and speed it up significantly.

## Production Usage

`traceroute -n` is the standard fast, script-friendly form used when localizing which hop along a
path stops responding, immediately followed by checking whether that hop's non-response is
consistent (a real problem) or occasional (likely just a router configured not to reply).

## Related Commands

- [`ping`](ping.md) — quick reachability check, run first before reaching for `traceroute`

## Related Concepts

- [ping, traceroute, mtr](../docs/12-networking/ping-traceroute-mtr.md)
