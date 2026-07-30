---
title: "Ports and Sockets Explained"
description: "What a port number actually identifies, what a socket is, and why every command earlier in this module keeps referring back to both."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/network-interfaces-and-bonding"]
relatedTopics: ["networking-fundamentals", "netstat-ss", "firewalls-iptables-firewalld-ufw"]
relatedCommands: ["ss"]
careerRelevance: ["devops", "backend", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#port-vs-socket"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "13-ssh/what-is-ssh-and-how-it-works"
prevTopic: "12-networking/network-interfaces-and-bonding"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["what is a port number", "what is a socket linux", "well-known ports explained", "tcp vs udp port"]
canonicalUrl: "/docs/networking/ports-and-sockets-explained"
---

# Ports and Sockets Explained

🟢 Must Know · Relevant for: DevOps · Backend · SRE · Linux Administrator

> **TL;DR:** A port is a number (0–65535) that identifies a specific service or connection on a
> machine, on top of its IP address. A socket is the actual endpoint of a specific connection — an
> IP address plus a port, plus (for an established connection) the same pair on the other side.
> Ports are how one machine can run many network services at once without them colliding.

## What is it?

The conceptual layer underneath every command earlier in this module: what a port number actually
identifies on top of an IP address ([Networking Fundamentals](networking-fundamentals.md)), and
what a socket is — the actual bound or connected endpoint that [`ss`](netstat-ss.md) reported on
and that [firewall rules](firewalls-iptables-firewalld-ufw.md) filter by.

## Why does it exist?

An IP address identifies a machine, but a single machine routinely runs many network services at
once — a web server, an SSH daemon, a database — all reachable at the same IP address. Ports exist
to let each of those services claim a distinct, addressable number so traffic for one doesn't
collide with or get mistaken for traffic meant for another.

## Where is it used?

Every single networked service on a Linux system: knowing which port a service must be
configured to use, understanding what a firewall rule is actually restricting, and reading `ss`
output correctly — every line of it is expressed in terms of address:port pairs.

## How it works

> 📊 Diagram: one server with a single IP address running three separate services (web on 443,
> SSH on 22, a database on 5432), each shown as a distinct socket (the same IP paired with a
> different port) — and a fourth diagram element showing an established connection as a full
> socket pair: client IP:port on one side, server IP:port on the other.

**Port**: a 16-bit number (0–65535) that, combined with an IP address and a protocol (TCP or UDP),
identifies a specific service or connection.

| Range | Meaning |
|---|---|
| 0–1023 | "Well-known" ports, conventionally reserved for standard services (22 SSH, 80 HTTP, 443 HTTPS) |
| 1024–49151 | Registered ports, used by many common applications but not OS-reserved |
| 49152–65535 | Ephemeral ports, typically assigned automatically to the *client* side of an outgoing connection |

**Socket**: the actual endpoint of a specific connection or listener — an IP address and port
combined (`192.168.1.50:443`). For an established connection, a socket is really a *pair*: the
local address:port and the remote address:port together uniquely identify that one specific
connection, which is exactly what a row in [`ss`](netstat-ss.md)'s output represents.

**TCP vs. UDP ports are independent**: a service can listen on TCP port 53 and a completely
different service can listen on UDP port 53 simultaneously — the protocol is part of what makes a
port number unique, not just the number alone.

## Real-world example

A server runs both a web application (TCP port 8080) and a monitoring agent that happens to also
listen on port 8080, but over UDP. Because TCP and UDP ports are tracked independently, both
services coexist without conflict — `ss -tuln` shows them as two entirely separate socket entries
despite sharing the same numeric port, distinguished only by the `-t`/`-u` protocol column.

## Commands

No new command — this page explains the concept behind
[`ss`](../../commands/ss.md)'s output and
[firewall](firewalls-iptables-firewalld-ufw.md) port rules, both already covered earlier in this
module.

## Production example

```
$ ss -tuln | grep 8080
tcp   LISTEN  0  128  0.0.0.0:8080  0.0.0.0:*
udp   UNCONN  0  0    0.0.0.0:8080  0.0.0.0:*
```

Two separate rows, same port number, different protocol column (`tcp` vs. `udp`) — confirming
they're genuinely independent listeners, not a conflict.

## Do / Don't

| Do | Don't |
|---|---|
| Treat TCP and UDP on the same port number as entirely independent | Assume two services can't both use "port 8080" just because the number matches |
| Read a socket as an address:port pair, not just a bare port number | Think of a port as meaningful on its own, disconnected from an address |
| Use the well-known port ranges as a guide, not a hard technical rule | Assume a service must run on its "standard" port — it's convention, not enforcement |

## Common mistakes

- Assuming a port number alone (without protocol) fully identifies a service — TCP and UDP on the
  same number are entirely independent.
- Treating "port" and "socket" as interchangeable terms — a port is just the number; a socket is
  the actual bound or connected endpoint (address + port, sometimes as a pair).
- Assuming a service must run on its conventional well-known port — that's convention and
  configuration, not a technical requirement.

## Best practices

- Always specify protocol (TCP/UDP) alongside a port number in documentation and firewall rules —
  "port 53" alone is ambiguous.
- Read `ss` and firewall rule output as address:port pairs, not bare numbers, to correctly
  interpret what's actually being matched.
- Reserve well-known ports for their conventional purpose in your own services, even though
  nothing technically enforces it — it avoids confusing anyone reading your configuration later.

## Exercises

1. Explain in one sentence the difference between a port and a socket.
2. Give an example of a well-known port and the service conventionally associated with it.
3. Explain why a TCP service and a UDP service can both use port 8080 without conflicting.

## Quiz

**Q: What's the difference between a port and a socket?**
<details><summary>Show answer</summary>
A port is just a number identifying a service. A socket is the actual endpoint — an IP address
combined with a port (and, for an established connection, the same pair on the other side too).
</details>

**Q: Can a TCP service and a UDP service use the same port number at the same time?**
<details><summary>Show answer</summary>
Yes — TCP and UDP ports are tracked independently by the kernel, so the same numeric port can be
used by one service over TCP and a completely different service over UDP simultaneously.
</details>

## Interview questions

- What's the difference between a port and a socket, and why does protocol matter when discussing
  a port number? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A port is a number (0–65535) identifying a service or connection on top of an IP address.
- A socket is the actual endpoint — address + port, or an address:port pair for an established
  connection.
- TCP and UDP ports are tracked independently — the same number can be used by both
  simultaneously without conflict.
- Well-known ports (0–1023) are convention, not a technical requirement.

## Related topics

- [Networking Fundamentals](networking-fundamentals.md)
- [netstat, ss](netstat-ss.md)
- [Firewalls: iptables, firewalld, ufw](firewalls-iptables-firewalld-ufw.md)
- [Module 13: SSH](../13-ssh/index.md)
