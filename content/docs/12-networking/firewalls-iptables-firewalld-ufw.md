---
title: "Firewalls: iptables, firewalld, ufw"
description: "Three tools for the same underlying job — filtering traffic by port and address — chosen based on which distro family and which layer of abstraction you're working at."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/etc-hosts-resolv-conf"]
relatedTopics: ["ports-and-sockets-explained"]
relatedCommands: ["iptables", "ufw", "firewall-cmd"]
careerRelevance: ["devops", "cybersecurity", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#iptables-vs-firewalld-vs-ufw"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/dns-troubleshooting-dig-nslookup"
prevTopic: "12-networking/etc-hosts-resolv-conf"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["iptables vs firewalld vs ufw", "ufw allow port", "firewalld zones", "iptables rules explained"]
canonicalUrl: "/docs/networking/firewalls-iptables-firewalld-ufw"
---

# Firewalls: iptables, firewalld, ufw

🟡 Good to Know · Relevant for: DevOps · Cybersecurity · Linux Administrator · SRE

> **TL;DR:** All three filter network traffic by port, protocol, and address — they're different
> front ends over the same underlying kernel packet-filtering machinery, not three competing
> technologies. `ufw` is Debian/Ubuntu's simplified default; `firewalld` is RHEL/CentOS/Rocky's
> default, built around reusable "zones"; `iptables` is the older, lower-level tool both can sit
> on top of.

## What is it?

Three tools for configuring which network traffic a Linux machine accepts, rejects, or drops,
based on port, protocol, and source/destination address — the practical enforcement layer for the
port concept covered later in this module's [Ports and Sockets Explained](ports-and-sockets-explained.md).

## Why does it exist?

A machine listening on a port (confirmed with [`ss`](netstat-ss.md) earlier in this module) is
reachable by default to anything that can route to it — a firewall is what actually restricts
that to only the traffic that should be allowed. `iptables` was the long-standing lower-level tool
for this; `firewalld` and `ufw` exist to make the same underlying capability easier to manage
without hand-writing raw rule syntax.

## Where is it used?

Restricting a server to only accept traffic on the specific ports its services actually use,
allowing SSH access only from known IP ranges, blocking a specific address after detecting abuse,
and — for compliance-driven roles — satisfying hardening requirements that mandate an active,
default-deny firewall.

## How it works

> 📊 Diagram: three boxes labeled `ufw`, `firewalld`, `iptables`, all shown feeding into one
> shared kernel packet-filtering layer (netfilter) beneath them — illustrating they're different
> interfaces over the same enforcement mechanism, not three separate firewalls.

| Tool | Default on | Model |
|---|---|---|
| `ufw` | Debian/Ubuntu | Simple allow/deny rules by port or app profile |
| `firewalld` | RHEL/CentOS/Rocky, Fedora | Rules grouped into reusable "zones" (e.g. `public`, `internal`) |
| `iptables` | Universal, lower-level | Raw chain-and-rule syntax; both tools above can sit on top of it |

**Default posture matters more than the tool**: all three should be configured "default deny,
explicitly allow" — only open the specific ports a service actually needs, rather than starting
permissive and trying to close things down later.

## Real-world example

A new web server is provisioned and nginx is confirmed listening on port 443 via `ss -tuln`
(earlier in this module). External requests still time out. `ufw status` reveals the firewall is
active with a default-deny policy and port 443 was never explicitly allowed — the service itself
is fine; the firewall, not DNS or the application, is what's blocking traffic. `ufw allow 443/tcp`
resolves it immediately.

## Commands

- [`iptables`](../../commands/iptables.md) — full syntax and examples
- [`ufw`](../../commands/ufw.md) — full syntax and examples

`firewalld`'s command-line tool, `firewall-cmd`, is covered as this module's third firewall
command page — see [`firewall-cmd`](../../commands/firewall-cmd.md).

## Production example

```
$ ufw status
Status: active

$ ufw allow 443/tcp
Rule added

$ firewall-cmd --list-ports
443/tcp

$ iptables -L INPUT -n | head -3
Chain INPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
```

Three different tools, same underlying question answered: which ports are explicitly allowed
through.

## Do / Don't

| Do | Don't |
|---|---|
| Configure default-deny, then explicitly allow only needed ports | Start permissive and try to lock things down later |
| Check the firewall before assuming DNS or the application is broken | Assume a listening service is automatically reachable externally |
| Use the tool your distro defaults to (`ufw` on Debian/Ubuntu, `firewalld` on RHEL family) | Install a second firewall front end alongside the distro default without a clear reason |

## Common mistakes

- Confirming a service is listening (via `ss`) and stopping the investigation there, without
  checking whether the firewall actually allows that port through externally.
- Starting with a permissive ("allow all") firewall policy and planning to restrict it later —
  that plan is frequently never finished.
- Running two firewall front ends (e.g. `ufw` and raw `iptables` rules) without understanding
  they both manipulate the same underlying rules, causing conflicting or confusing state.

## Best practices

- Default every server to "deny by default, explicitly allow only what's needed."
- Check the firewall early in any "service unreachable externally but confirmed listening"
  investigation — it's a very common and quick thing to rule in or out.
- Stick to one firewall front end per system (whichever your distro defaults to) rather than
  mixing tools that manage the same underlying rules.

## Exercises

1. Check whether a firewall tool is active on a system you have access to, and list its current
   rules.
2. Explain in one sentence why "default deny, explicitly allow" is the recommended posture.
3. Describe the real-world example's diagnostic sequence: what did `ss` rule out, and what did
   the firewall check confirm?

## Quiz

**Q: Are `iptables`, `firewalld`, and `ufw` three competing firewall technologies?**
<details><summary>Show answer</summary>
No — they're different front ends over the same underlying kernel packet-filtering machinery
(netfilter), not three separate enforcement mechanisms.
</details>

**Q: What firewall posture should a production server default to?**
<details><summary>Show answer</summary>
Default deny, with only the specific ports a service actually needs explicitly allowed.
</details>

## Interview questions

- A service is confirmed listening with `ss` but unreachable externally — what would you check
  next? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `iptables`, `firewalld`, and `ufw` are different interfaces over the same kernel-level
  packet-filtering mechanism.
- `ufw` defaults on Debian/Ubuntu; `firewalld` defaults on RHEL-family distros.
- "Default deny, explicitly allow" is the correct posture, not "default allow, restrict later."
- A listening service (confirmed via `ss`) can still be unreachable externally if the firewall
  blocks it.

## Related topics

- [Ports and Sockets Explained](ports-and-sockets-explained.md)
- [netstat, ss](netstat-ss.md)
