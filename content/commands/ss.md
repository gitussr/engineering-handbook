---
title: "ss — Investigate Sockets"
description: "List listening and active TCP/UDP sockets, the modern, faster replacement for netstat."
relatedConcepts: ["12-networking/netstat-ss", "12-networking/ports-and-sockets-explained"]
relatedCommands: ["ip"]
careerRelevance: ["devops", "sre", "linux-administrator", "cybersecurity"]
difficulty: "must-know"
compatibility: [{"context": "iproute2 (Linux)", "note": "Replaces netstat, which is deprecated and often not installed by default on current distros."}]
updatedAt: "2026-07-27"
keywords: ["ss command linux", "ss -tuln", "ss vs netstat", "check listening ports"]
canonicalUrl: "/commands/ss"
---

# ss

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Cybersecurity

> **TL;DR:** `ss -tuln` lists every listening TCP/UDP socket, numerically, with no name
> resolution delay — the fastest way to answer "what's this machine actually listening on?"

## Purpose

`ss` inspects active connections and listening sockets — see
[netstat, ss](../docs/12-networking/netstat-ss.md) for how it replaced `netstat`, and
[Ports and Sockets Explained](../docs/12-networking/ports-and-sockets-explained.md) for the
concept behind its output.

## Syntax

```
ss [OPTIONS]
```

## Arguments

None — `ss` is entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening sockets only |
| `-n` | Numeric output — skip resolving ports/hosts to names |
| `-p` | Show the owning process (may require root for other users' processes) |
| `-a` | All sockets, not just listening |

## Examples

```
$ ss -tuln
```
List all listening TCP and UDP sockets, numerically.

```
$ ss -tulnp
```
Same, plus the owning process (run with `sudo` to see processes owned by other users).

```
$ ss -tuln | grep 8080
```
Check whether anything is listening on port 8080.

```
$ ss -tan
```
List all TCP sockets (not just listening ones), numerically — useful for seeing active
connections, not just listeners.

## Expected Output

```
$ ss -tuln | grep 8080
tcp   LISTEN  0  128  0.0.0.0:8080  0.0.0.0:*
```

`LISTEN` state, local address `0.0.0.0:8080` (listening on all interfaces on port 8080), no
established peer yet (`0.0.0.0:*`).

## Exit Status

`0` on success, non-zero on invalid options.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Empty output for a port you expect to be listening | Service isn't actually bound to that port, or crashed/failed to start | Check the service's own logs (`journalctl`) before suspecting the network |
| `Permission denied` seeing process names for other users | `-p` requires root to see other users' process ownership | Re-run with `sudo` |
| Slow output | Reverse-DNS/service-name resolution without `-n` | Add `-n` |

## Security Considerations

`ss -tulnp` (with sufficient privilege) is a standard part of a security review — an unexpected
listening port is one of the first things to check for on a server that shouldn't have one.

## Performance Considerations

`ss` reads kernel socket data more directly than the older `netstat` did, and stays fast even on
systems with very large numbers of connections — the specific reason it replaced `netstat`.

## Compatibility Notes

`netstat` (from the older `net-tools` package) covered similar ground with different flag names
(`netstat -tuln` maps closely to `ss -tuln`) but is deprecated and often absent by default on
current distros.

## Production Usage

`ss -tulnp | grep PORT` is the standard first check when a service seems unreachable — confirming
it's actually listening (and on the process you expect) before investigating DNS or the firewall.

## Related Commands

- [`ip`](ip.md) — interface addresses and routing, the companion inspection tool

## Related Concepts

- [netstat, ss](../docs/12-networking/netstat-ss.md)
- [Ports and Sockets Explained](../docs/12-networking/ports-and-sockets-explained.md)
