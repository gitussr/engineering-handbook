---
title: "Firewall Configuration in Depth"
description: "Beyond opening a port: custom iptables chains, firewalld rich rules and panic mode, and ufw rate-limiting — the advanced firewall patterns a hardening pass actually needs."
module: "19-security"
moduleTitle: "Security"
stage: "professional"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["19-security/server-hardening-checklist"]
relatedTopics: ["server-hardening-checklist", "fail2ban"]
relatedCommands: ["iptables", "firewall-cmd", "ufw"]
careerRelevance: ["cybersecurity", "devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#rich-rule-vs-plain-port"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "19-security/selinux-and-apparmor"
prevTopic: "19-security/server-hardening-checklist"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["iptables custom chain example", "firewalld rich rule example", "ufw rate limiting", "firewall panic mode"]
canonicalUrl: "/docs/security/firewall-configuration-in-depth"
---

# Firewall Configuration in Depth

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · Linux Administrator · SRE

> **TL;DR:** Beyond "open this port": organize related rules into custom `iptables` chains, use
> `firewalld` rich rules for source-restricted access, use `ufw limit` for lightweight
> brute-force mitigation, and know each tool's emergency killswitch for an active incident.

## What is it?

The advanced configuration patterns for the three firewall tools already introduced in
[Module 12](../12-networking/firewalls-iptables-firewalld-ufw.md) — custom chains, rich rules,
rate-limiting, and panic/emergency modes — needed once "open a port" isn't enough.

## Why does it exist?

Module 12 taught how to open and close ports — sufficient for a basic host. Production hardening
needs more: restricting access by source address without a full rule per address, organizing
related rules for maintainability, mitigating brute-force attempts automatically, and having an
immediate, tested response for an active incident.

## Where is it used?

Hardening internet-facing servers ([Server Hardening Checklist](server-hardening-checklist.md)),
restricting management access to known networks, and responding to an active attack where the
fastest possible mitigation matters more than a carefully-scoped rule.

## How it works

> 📊 Diagram: three labeled panels, one per tool — `iptables` showing a custom chain
> (`RATE_LIMIT`) receiving jumps from the main `INPUT` chain; `firewalld` showing a rich rule's
> three components (source match, port match, action) as a single expressive line; `ufw` showing
> `limit` as a built-in counter that trips after N connections in a time window — each panel
> ending in the same outcome (traffic allowed/blocked) via a different mechanism.

**`iptables` — custom chains for organization:**

```bash
iptables -N RATE_LIMIT
iptables -A RATE_LIMIT -m limit --limit 5/min -j ACCEPT
iptables -A RATE_LIMIT -j DROP
iptables -A INPUT -p tcp --dport 22 -j RATE_LIMIT
```

Grouping related logic (all the rate-limiting rules) into its own chain, then jumping matching
traffic into it from `INPUT`, keeps a growing rule set organized and auditable — rather than one
long, undifferentiated `INPUT` chain. See [`iptables`](../../commands/iptables.md) for `-N`/`-F`.

**`firewalld` — rich rules for source-restricted access:**

```bash
firewall-cmd --add-rich-rule='rule family="ipv4" source address="203.0.113.0/24" port port="22" protocol="tcp" accept' --permanent
firewall-cmd --reload
```

A plain `--add-port` opens a port to everyone; a rich rule expresses source restriction, logging,
or rate limiting in one declarative line — see [`firewall-cmd`](../../commands/firewall-cmd.md).

**`ufw` — built-in rate limiting:**

```bash
ufw limit 22/tcp
```

`limit` denies an address automatically after repeated rapid connection attempts — a lightweight,
built-in complement to [Fail2ban](fail2ban.md) (next page) for the exact same threat (SSH
brute-forcing), with less configurability but zero extra setup.

**Emergency/panic modes** — every tool has one:

| Tool | Command | Effect |
|---|---|---|
| `firewalld` | `firewall-cmd --panic-on` | Blocks all traffic immediately |
| `iptables` | `iptables -P INPUT DROP; iptables -F INPUT` | Manually equivalent — set default-drop, flush allow rules |
| `ufw` | `ufw disable` then re-enable with only emergency rules | No single-command panic mode; requires manual sequencing |

## Real-world example

During an active incident where a service is being actively exploited over the network, an
on-call engineer needs to cut off all external traffic immediately while investigation continues,
without losing their own SSH session. `firewall-cmd --panic-on` (RHEL-family) does exactly this in
one command — but blocks *all* traffic, including the engineer's own SSH, unless they've already
confirmed console/out-of-band access first. Knowing this tradeoff before an incident, not during
one, is exactly why this page exists.

## Syntax

```
iptables -N CHAIN
firewall-cmd --add-rich-rule='RULE' [--permanent]
ufw limit PORT/PROTOCOL
```

## Commands

See [`iptables`](../../commands/iptables.md), [`firewall-cmd`](../../commands/firewall-cmd.md),
and [`ufw`](../../commands/ufw.md) — all three extended for this module with the flags above.

## Production example

```
$ ufw limit 22/tcp
Rules updated
Rules updated (v6)

$ ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     LIMIT       Anywhere
```

A single command adds automatic rate-limiting to SSH — visible in `ufw status` as `LIMIT` instead
of plain `ALLOW`.

## Do / Don't

| Do | Don't |
|---|---|
| Use rich rules / custom chains for source-restricted or organized access | Write one plain `allow`/`ACCEPT` rule per source address as the list grows |
| Confirm out-of-band/console access before testing a panic mode | Run `--panic-on` on a server you only reach via the connection it will block |
| Rate-limit exposed management ports (`ufw limit`, or equivalent) | Leave SSH open with no brute-force mitigation at all |

## Common mistakes

- Running `firewall-cmd --panic-on` (or the `iptables` equivalent) without confirmed alternate
  access, locking yourself out along with the attacker.
- Writing dozens of individual `allow from IP` rules instead of a single rich rule or CIDR-scoped
  rule when the addresses share a range.
- Forgetting `--permanent` and `--reload` on `firewalld` rich rules, losing them on the next
  reload despite them working in the moment.

## Best practices

- Rate-limit every externally-reachable management port (SSH at minimum) as a baseline, whether
  via `ufw limit`, an `iptables` custom chain, or [Fail2ban](fail2ban.md).
- Test a panic-mode command's exact effect on a non-critical system first, so its behavior isn't
  a surprise during a real incident.
- Prefer rich rules / custom chains over an ever-growing flat list of near-duplicate rules —
  they scale better and are easier to audit.

## Exercises

1. Write an `iptables` custom chain that rate-limits a port, then jumps `INPUT` traffic on that
   port into it.
2. Write a `firewalld` rich rule that allows HTTPS only from a specific subnet.
3. Explain the tradeoff of using `firewall-cmd --panic-on` during an active incident.

## Quiz

**Q: What does a firewalld rich rule let you express that a plain --add-port doesn't?**
<details><summary>Show answer</summary>
Source address restriction, logging, and rate limiting — a plain `--add-port` opens a port to all
traffic with no additional conditions.
</details>

**Q: What's the risk of using an emergency panic mode like firewall-cmd --panic-on?**
<details><summary>Show answer</summary>
It blocks all traffic, including your own SSH session, unless you've already confirmed
console/out-of-band access — it can lock you out along with any attacker.
</details>

**Q: Why organize related iptables rules into a custom chain instead of one flat INPUT chain?**
<details><summary>Show answer</summary>
It keeps related logic (e.g. all rate-limiting rules) grouped and auditable as the rule set grows,
rather than an increasingly long, undifferentiated single chain.
</details>

## Interview questions

- How would you rate-limit SSH access on both a Debian/Ubuntu and a RHEL-family server? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Custom `iptables` chains, `firewalld` rich rules, and `ufw limit` cover the advanced patterns
  beyond a plain open port.
- Every firewall tool has an emergency/panic mode — know its exact effect before an incident, not
  during one.
- Rate-limiting exposed management ports is a baseline hardening step, complementary to
  [Fail2ban](fail2ban.md).
- These are extensions of the same three tools from Module 12, not new tools.

## Related topics

- [Server Hardening Checklist](server-hardening-checklist.md)
- [Fail2ban](fail2ban.md)
