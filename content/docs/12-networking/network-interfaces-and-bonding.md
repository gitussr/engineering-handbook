---
title: "Network Interfaces and Bonding"
description: "Combining multiple physical network interfaces into one logical interface for redundancy or higher throughput — how it's configured and why it exists."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["12-networking/dns-troubleshooting-dig-nslookup"]
relatedTopics: ["ip-addr-ip-route-ifconfig"]
relatedCommands: ["ip"]
careerRelevance: ["linux-administrator", "sre", "platform"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#bonding-modes"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/ports-and-sockets-explained"
prevTopic: "12-networking/dns-troubleshooting-dig-nslookup"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["network interface bonding linux", "nic teaming linux", "bonding modes explained", "network redundancy linux"]
canonicalUrl: "/docs/networking/network-interfaces-and-bonding"
---

# Network Interfaces and Bonding

🔴 Expert · Relevant for: Linux Administrator · SRE · Platform

> **TL;DR:** Bonding combines two or more physical network interfaces into one logical interface,
> either for failover (if one physical link dies, traffic keeps flowing through the other) or for
> combined throughput, depending on the bonding mode configured.

## What is it?

Bonding (also called "NIC teaming") is the kernel's ability to present multiple physical network
interfaces to the rest of the system as one single logical interface, with a configured mode
determining how traffic is actually distributed across the underlying physical links.

## Why does it exist?

A server with a single network interface has a single point of failure — if that link, its
switch port, or its cable fails, the server loses connectivity entirely. Bonding exists so
production servers (especially in data centers, where physical redundancy is standard) can survive
the loss of one physical link without losing network connectivity, and in some modes, get
higher aggregate throughput than any single link could provide.

## Where is it used?

Production database and application servers where network downtime from a single failed link or
switch port is unacceptable, and high-throughput workloads that benefit from aggregating bandwidth
across multiple physical interfaces.

## How it works

> 📊 Diagram: two physical NICs (`eth0`, `eth1`) both connected to (ideally) two different
> switches, both feeding into one logical `bond0` interface that the rest of the system sees as a
> single interface — with a callout showing one physical link failing while `bond0` keeps
> functioning through the surviving link.

| Mode | Behavior |
|---|---|
| Active-backup | One interface active at a time; the other takes over only on failure — pure redundancy |
| Round-robin / balance-rr | Traffic distributed across all interfaces in turn — throughput-oriented |
| 802.3ad (LACP) | Requires switch-side support; aggregates links with proper load balancing and failover |

The rest of the system — applications, routing, firewall rules — interacts only with the single
logical `bond0` interface; the physical interface details and failover logic are handled beneath
it, invisible above that layer.

## Real-world example

A database server's single network cable is accidentally unplugged during unrelated rack
maintenance. On a server without bonding, this is an immediate, full outage. On a server
configured with active-backup bonding across two separate physical NICs connected to two separate
switches, the same event causes zero downtime — the bond automatically fails over to the
surviving link, and the maintenance mistake never becomes an incident.

## Commands

No new command — bonding builds on `ip link` (already covered via
[`ip`](../../commands/ip.md) earlier in this module); the bond interface itself is typically
defined in distro-specific network configuration (e.g. `nmcli`, `/etc/netplan`, or
`/etc/sysconfig/network-scripts`) rather than configured ad hoc from the command line each boot.

## Production example

```
$ cat /proc/net/bonding/bond0
Bonding Mode: fault-tolerance (active-backup)
Currently Active Slave: eth0
Slave Interface: eth0
  MII Status: up
Slave Interface: eth1
  MII Status: up
```

`/proc/net/bonding/bond0` (a virtual file, [Module 05](../05-file-system/proc-and-sys-virtual-filesystems.md))
shows the current bonding mode and which physical interface is presently active — the standard
way to confirm a bond is healthy and correctly configured.

## Do / Don't

| Do | Don't |
|---|---|
| Connect bonded interfaces to genuinely separate switches when redundancy is the goal | Bond two interfaces into the same switch and call it fully redundant |
| Confirm both slave interfaces show `MII Status: up` after configuring a bond | Assume a bond is healthy without checking `/proc/net/bonding/` |
| Match the bonding mode to the actual goal (failover vs. throughput) | Pick 802.3ad without confirming switch-side LACP support first |

## Common mistakes

- Bonding two interfaces that both connect to the same physical switch, defeating the redundancy
  purpose entirely — a single switch failure still takes down connectivity.
- Configuring 802.3ad (LACP) mode without confirming the connected switch actually supports LACP,
  resulting in a bond that doesn't work as expected.
- Never checking `/proc/net/bonding/` after initial setup, missing a silently degraded bond
  running on only one surviving link.

## Best practices

- Connect each interface in a bond to a physically separate switch whenever redundancy (not just
  throughput) is the goal.
- Periodically confirm bond health via `/proc/net/bonding/bond0` as part of routine monitoring,
  not just at initial setup.
- Choose the bonding mode based on the actual requirement — active-backup for pure redundancy,
  802.3ad for combined throughput with proper switch support.

## Exercises

1. Explain in one sentence the difference between active-backup and round-robin bonding modes.
2. Describe why bonding two interfaces into the same physical switch only partially achieves
   redundancy.
3. If available, check `/proc/net/bonding/` on a system that has a bond configured and identify
   the active slave interface.

## Quiz

**Q: What's the main purpose of active-backup bonding mode?**
<details><summary>Show answer</summary>
Pure redundancy — one interface is active at a time, and the other takes over automatically only
if the active one fails.
</details>

**Q: Why does bonding two interfaces into the same physical switch only partially solve
redundancy?**
<details><summary>Show answer</summary>
The switch itself remains a single point of failure — if that switch fails, both bonded
interfaces lose connectivity simultaneously.
</details>

## Interview questions

- What problem does network interface bonding solve, and what's the tradeoff between
  active-backup and 802.3ad modes? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Bonding combines multiple physical interfaces into one logical interface for redundancy,
  throughput, or both, depending on mode.
- Active-backup is pure failover; 802.3ad requires switch-side LACP support for load-balanced
  aggregation.
- True redundancy requires bonded interfaces to connect to physically separate switches.
- `/proc/net/bonding/bond0` shows current bond health and which interface is active.

## Related topics

- [ip addr, ip route (and legacy ifconfig)](ip-addr-ip-route-ifconfig.md)
- [Ports and Sockets Explained](ports-and-sockets-explained.md)
