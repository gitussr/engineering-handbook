---
title: "ip addr, ip route (and legacy ifconfig)"
description: "Viewing and configuring interface addresses and routing tables with the modern ip command, and why ifconfig still shows up in older documentation."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/networking-fundamentals"]
relatedTopics: ["network-interfaces-and-bonding"]
relatedCommands: ["ip"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ip-vs-ifconfig"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/ping-traceroute-mtr"
prevTopic: "12-networking/networking-fundamentals"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["ip addr show", "ip route show", "ifconfig deprecated", "ip a command linux"]
canonicalUrl: "/docs/networking/ip-addr-ip-route-ifconfig"
---

# ip addr, ip route (and legacy ifconfig)

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `ip addr` shows interface IP addresses; `ip route` shows the routing table
> (including the default gateway from the previous topic). `ip` is the modern, actively
> maintained tool — `ifconfig` still appears in older docs and scripts but is deprecated and not
> installed by default on many current distros.

## What is it?

`ip` is the modern command-line tool for viewing and configuring network interfaces, addresses,
and routes — the practical, hands-on counterpart to the concepts introduced in
[Networking Fundamentals](networking-fundamentals.md). `ifconfig` is the older tool it replaced.

## Why does it exist?

Every concept from the previous topic — a machine's own IP address, its subnet, and its default
gateway — needs a way to actually be inspected and configured on a running system. `ip` is that
tool, part of the modern `iproute2` package that replaced the older, more limited `net-tools`
package `ifconfig` came from.

## Where is it used?

Confirming a machine's own IP configuration during initial setup or troubleshooting, checking the
routing table when traffic isn't reaching an expected destination, and — for `ip link` — checking
whether an interface is administratively up or down.

## How it works

> 📊 Diagram: one terminal pane running `ip addr show` with its output annotated (interface name,
> state UP/DOWN, inet address with CIDR suffix) next to a second pane running `ip route show`
> with its output annotated (default route line highlighted, pointing at the gateway from the
> previous topic).

| Command | Shows |
|---|---|
| `ip addr show` (or `ip a`) | Every interface's IP address(es), in CIDR notation |
| `ip route show` (or `ip r`) | The routing table, including the default gateway |
| `ip link show` | Interfaces and their administrative state (UP/DOWN), no IP info |

`ifconfig` combined address and interface-state info into one command's output; `ip` deliberately
splits this into `ip addr` (addresses) and `ip link` (interface state) as separate, more
composable subcommands.

## Real-world example

An engineer inherits a runbook written years ago that says "run `ifconfig` to check the server's
IP." On a fresh minimal server install, the command doesn't exist at all — `net-tools` isn't
installed by default on most current distros. `ip addr show` is the direct, actively-maintained
replacement, and updating the runbook to reference it (rather than installing `net-tools` just to
keep an old command working) is the better long-term fix.

## Commands

- [`ip`](../../commands/ip.md) — full syntax and examples

## Production example

```
$ ip addr show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0

$ ip route show
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.50
```

The first line of `ip route show` output is the default route — `via 192.168.1.1` is the gateway
from the previous topic.

## Do / Don't

| Do | Don't |
|---|---|
| Use `ip addr`/`ip route` as the default tool on any current distro | Assume `ifconfig` is installed by default on a fresh minimal server |
| Read `ip route show`'s `default via ...` line to find the gateway | Search for a gateway setting in a separate, dedicated command |
| Use `ip link show` when you only need interface up/down state | Parse `ip addr` output just to check whether an interface is up |

## Common mistakes

- Writing runbooks or scripts around `ifconfig` on the assumption it's always present — it's
  often not installed by default anymore.
- Not realizing `ip route show`'s first `default via ...` line is the default gateway — looking
  for gateway configuration somewhere else instead.
- Confusing `ip addr` (addresses) with `ip link` (administrative interface state) — they answer
  different questions.

## Best practices

- Default to `ip` for all interface and routing inspection on current systems; treat `ifconfig`
  familiarity as reading old documentation, not something to actively use going forward.
- Check `ip route show` early in any "can't reach an external host" investigation — a missing or
  wrong default route is a common, easy-to-spot cause.
- Use `ip a` / `ip r` (the short forms) for quick interactive checks; use the full `ip addr show`
  / `ip route show` in scripts and documentation for clarity.

## Exercises

1. Run `ip addr show` on a system you have access to and identify your own IP address and subnet
   in CIDR notation.
2. Run `ip route show` and identify the default gateway.
3. Explain in one sentence why `ip addr` and `ip link` are separate subcommands instead of one.

## Quiz

**Q: Which line in `ip route show` output identifies the default gateway?**
<details><summary>Show answer</summary>
The line starting with `default via <IP>` — the IP address after `via` is the gateway.
</details>

**Q: Why might `ifconfig` fail to run on a freshly installed minimal server?**
<details><summary>Show answer</summary>
`ifconfig` comes from the older `net-tools` package, which isn't installed by default on many
current distros — `ip` (from `iproute2`) is the actively maintained replacement.
</details>

## Interview questions

- What's the modern replacement for `ifconfig`, and why did it replace it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `ip addr show` displays interface IP addresses; `ip route show` displays the routing table.
- The default gateway appears as the `default via <IP>` line in `ip route show`.
- `ifconfig` is deprecated and often absent by default — `ip` is the modern standard.
- `ip` splits address info (`ip addr`) and interface state (`ip link`) into separate subcommands.

## Related topics

- [Network Interfaces and Bonding](network-interfaces-and-bonding.md)
- [Networking Fundamentals](networking-fundamentals.md)
