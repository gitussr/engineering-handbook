---
title: "Networking Fundamentals: IP, Subnet, Gateway, DNS"
description: "The four concepts every other page in this module builds on — what an IP address, subnet, gateway, and DNS lookup actually are, without yet touching a single command."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["11-services/init-systems-sysv-vs-systemd"]
relatedTopics: ["ports-and-sockets-explained"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ip-subnet-gateway-dns"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/ip-addr-ip-route-ifconfig"
prevTopic: "11-services/init-systems-sysv-vs-systemd"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["ip address explained", "subnet mask explained", "default gateway explained", "how dns works"]
canonicalUrl: "/docs/networking/networking-fundamentals"
---

# Networking Fundamentals: IP, Subnet, Gateway, DNS

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** An IP address identifies a machine on a network. A subnet mask defines which part of
> that address is "network" and which part is "host," so a machine can tell whether another
> address is local or needs to go through the gateway — the router that forwards traffic off the
> local network. DNS translates human-readable names into IP addresses so nothing above this
> layer has to work with numbers directly.

## What is it?

The four building blocks every other topic in this module assumes: an IP address (a numeric
identity on a network), a subnet mask (which part of that address is "network" and which is
"host"), a default gateway (where traffic goes when the destination isn't local), and DNS (the
system that maps names like `example.com` to IP addresses).

## Why does it exist?

Machines route traffic using numbers, not names, and need a way to decide — for every single
packet — whether the destination is reachable directly on the local network or needs to be
forwarded somewhere else. Subnetting and gateways solve that routing decision; DNS exists purely
because humans don't want to memorize numeric addresses for everything they connect to.

## Where is it used?

Every single networked interaction on a Linux system touches all four: connecting to a server by
hostname resolves through DNS, the resulting IP address is checked against the local subnet to
decide whether it's reachable directly or needs the gateway, and the reply traffic follows the
same logic in reverse.

## How it works

> 📊 Diagram: a laptop with IP `192.168.1.50/24` on a local network with a gateway/router at
> `192.168.1.1`, shown reaching another local device directly (same subnet) versus a device on
> the public internet (traffic routed through the gateway) — plus a separate DNS resolver box the
> laptop queries first to turn `example.com` into an IP address before any of that routing happens.

**IP address**: a numeric identity for a network interface. IPv4 addresses are four numbers
0–255 separated by dots (`192.168.1.50`). Every reachable interface on a network needs one.

**Subnet mask**: defines how much of the address is the "network" portion versus the "host"
portion. Written either as a dotted mask (`255.255.255.0`) or CIDR notation (`/24` — the modern,
far more common form). A `/24` means the first 24 bits are the network portion, leaving the last
8 bits (256 possible values) for hosts on that network.

| CIDR | Dotted mask | Usable hosts |
|---|---|---|
| `/24` | `255.255.255.0` | 254 |
| `/16` | `255.255.0.0` | 65,534 |
| `/8` | `255.0.0.0` | 16,777,214 |

**Default gateway**: the router a machine sends traffic to when the destination address isn't on
its own local subnet. Every machine needs exactly one to reach anything outside its local network
— without it, a machine can only talk to other devices on the same subnet.

**DNS (Domain Name System)**: a distributed, hierarchical lookup system that translates a
hostname (`example.com`) into an IP address. A DNS query happens before almost any other network
interaction — the application has to know the numeric address before it can route or connect to
anything.

## Real-world example

A newly provisioned server can `ping` its gateway's IP address successfully but can't reach
`example.com` at all. Pinging the same site's raw IP address directly works fine. The symptom
points at DNS specifically, not general connectivity — the routing (IP, subnet, gateway) is fine;
name resolution is broken. This exact diagnostic pattern — test by IP first, then by name — is
the standard first move in any "can't reach X" investigation, expanded on later in this module's
[DNS troubleshooting](dns-troubleshooting-dig-nslookup.md) page.

## Commands

No command example on this page — this page is conceptual foundation. See
[`ip addr`, `ip route`](ip-addr-ip-route-ifconfig.md) for the first hands-on commands in this
module.

## Production example

Not applicable — see [`ip addr`, `ip route`](ip-addr-ip-route-ifconfig.md) for the first terminal
session in this module.

## Do / Don't

| Do | Don't |
|---|---|
| Test connectivity by raw IP address first when name resolution is suspect | Assume "can't reach the site" always means a routing problem |
| Think in CIDR notation (`/24`) — it's what you'll see everywhere in practice | Rely on memorizing dotted subnet masks as your primary mental model |
| Remember every machine needs exactly one default gateway to leave its subnet | Assume a machine can reach the internet just because it has an IP address |

## Common mistakes

- Assuming an IP address alone guarantees reachability — without a correctly configured gateway,
  a machine can only reach other devices on its own subnet.
- Treating "the site is down" and "DNS is broken" as the same failure — they have completely
  different fixes, and testing by IP address first tells them apart immediately.
- Miscounting usable hosts in a subnet — a `/24` gives 254 usable addresses, not 256 (the network
  address and broadcast address aren't assignable to hosts).

## Best practices

- When troubleshooting "can't reach X," test the raw IP address before assuming the problem is
  routing — it immediately isolates DNS as a separate concern.
- Default to CIDR notation (`/24`, `/16`) when documenting or discussing network configuration —
  it's the form used throughout modern tooling (cloud consoles, `ip`, firewall rules).
- Know your own machine's IP, subnet, and gateway before an incident, not during one —
  [`ip addr`, `ip route`](ip-addr-ip-route-ifconfig.md), next in this module, is how you check.

## Exercises

1. Write down, from memory, what `/24` means in terms of network vs. host bits.
2. Explain in one sentence why a machine needs a default gateway to reach anything outside its
   own subnet.
3. Describe the diagnostic difference between "can't reach a site by name" and "can't reach a
   site by IP."

## Quiz

**Q: How many usable host addresses does a `/24` subnet provide?**
<details><summary>Show answer</summary>
254 — a `/24` allows 256 total addresses, minus one for the network address and one for the
broadcast address.
</details>

**Q: What does a default gateway do?**
<details><summary>Show answer</summary>
It's the router a machine sends traffic to when the destination isn't on its own local subnet —
without it, a machine can only reach devices on the same subnet.
</details>

**Q: Why does testing a site by raw IP address help diagnose a connectivity issue?**
<details><summary>Show answer</summary>
It isolates whether the problem is DNS (name resolution) or general routing/connectivity — if the
IP works but the name doesn't, the problem is specifically DNS.
</details>

## Interview questions

- What's the difference between a subnet mask and a default gateway? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)
- How would you determine whether a connectivity failure is a DNS issue or a routing issue? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- An IP address identifies a machine; a subnet mask defines the network/host split of that
  address; a gateway is where non-local traffic goes; DNS translates names to IP addresses.
- CIDR notation (`/24`) is the modern standard way to express subnet size.
- Testing by raw IP address first is the standard way to isolate a DNS problem from a routing
  problem.
- Every machine needs exactly one default gateway to reach anything outside its own subnet.

## Related topics

- [Ports and Sockets Explained](ports-and-sockets-explained.md)
- [`ip addr`, `ip route`](ip-addr-ip-route-ifconfig.md)
