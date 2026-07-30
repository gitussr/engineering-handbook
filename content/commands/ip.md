---
title: "ip — Show and Configure Addresses, Routes, and Links"
description: "The modern tool for interface addresses, routing tables, and link state, replacing ifconfig/route/netstat."
relatedConcepts: ["12-networking/ip-addr-ip-route-ifconfig", "12-networking/network-interfaces-and-bonding"]
relatedCommands: ["ss"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
difficulty: "must-know"
compatibility: [{"context": "iproute2 (Linux)", "note": "Standard on all current distros. The older net-tools suite (ifconfig, route, netstat) it replaced is often not installed by default anymore."}]
updatedAt: "2026-07-27"
keywords: ["ip addr show", "ip route show", "ip link show", "ip command linux"]
canonicalUrl: "/commands/ip"
---

# ip

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `ip addr show` lists interface IP addresses. `ip route show` lists the routing table.
> `ip link show` lists interfaces and their up/down state. One tool, several object types, each
> its own subcommand.

## Purpose

`ip` inspects and configures network interfaces, addresses, and routes — see
[ip addr, ip route (and legacy ifconfig)](../docs/12-networking/ip-addr-ip-route-ifconfig.md) for
when and why.

## Syntax

```
ip [OPTIONS] OBJECT COMMAND
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `OBJECT` | What to act on: `addr`, `route`, `link`, etc. | Yes |
| `COMMAND` | The action: `show`, `add`, `del`, etc. | No — defaults to `show` if omitted |

## Options

| Flag | Meaning |
|---|---|
| `addr` (or `a`) | Interface IP addresses |
| `route` (or `r`) | Routing table |
| `link` (or `l`) | Interface administrative state (UP/DOWN), no IP info |
| `-4` | Restrict output to IPv4 |
| `-6` | Restrict output to IPv6 |

## Examples

```
$ ip addr show
```
List every interface's IP address(es).

```
$ ip addr show eth0
```
List only `eth0`'s address(es).

```
$ ip route show
```
Show the routing table, including the default gateway.

```
$ ip link show
```
Show interfaces and whether each is administratively up or down.

```
$ ip -4 addr show eth0
```
Show only `eth0`'s IPv4 address, skipping IPv6.

## Expected Output

```
$ ip addr show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0
```

`UP` in the flags list and `state UP` both need to be true for the interface to be usable;
`inet 192.168.1.50/24` is the address in CIDR notation.

## Exit Status

`0` on success, non-zero if the specified interface doesn't exist or the syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Cannot find device "X"` | Interface name is wrong or doesn't exist on this machine | Run `ip link show` with no argument to list actual interface names |
| Interface shows no `inet` line | No IP address assigned (DHCP not run yet, or static config missing) | Check DHCP client status or static configuration |
| `RTNETLINK answers: Operation not permitted` | Attempting to modify (not just view) without sufficient privilege | Use `sudo` for `add`/`del`/`set` operations |

## Security Considerations

Viewing (`show`) requires no special privilege; modifying interfaces, addresses, or routes
requires root — a compromised unprivileged process cannot alter network configuration through
`ip` alone.

## Performance Considerations

Negligible — `ip` reads kernel networking state directly, the same underlying data
`/proc/net/*` exposes.

## Compatibility Notes

`ifconfig`, `route`, and `netstat` (the older `net-tools` suite) covered overlapping ground with
`ip addr`, `ip route`, and `ss` respectively, but are often not installed by default on current
distros. `ip` is the actively maintained standard.

## Production Usage

`ip route show`'s first line (`default via ...`) is the fastest way to confirm a machine's
default gateway during any "can't reach anything outside the local network" investigation.

## Related Commands

- [`ss`](ss.md) — socket/connection inspection, the companion tool for "what's listening"

## Related Concepts

- [ip addr, ip route (and legacy ifconfig)](../docs/12-networking/ip-addr-ip-route-ifconfig.md)
- [Network Interfaces and Bonding](../docs/12-networking/network-interfaces-and-bonding.md)
