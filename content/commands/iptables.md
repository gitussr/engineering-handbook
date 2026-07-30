---
title: "iptables — Configure Kernel Packet Filtering Rules"
description: "The lower-level, universal tool for filtering traffic by chain and rule, underlying both firewalld and ufw."
relatedConcepts: ["12-networking/firewalls-iptables-firewalld-ufw", "12-networking/ports-and-sockets-explained", "19-security/firewall-configuration-in-depth"]
relatedCommands: ["ufw", "firewall-cmd"]
careerRelevance: ["devops", "cybersecurity", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: [{"context": "netfilter (Linux kernel)", "note": "Increasingly a legacy interface as distros shift to nftables underneath, but iptables syntax and the iptables command itself remain widely supported via a compatibility layer."}]
updatedAt: "2026-07-28"
keywords: ["iptables rules explained", "iptables -L", "iptables allow port", "iptables chains", "iptables custom chain", "iptables-save persist rules"]
canonicalUrl: "/commands/iptables"
---

# iptables

🟡 Good to Know · Relevant for: DevOps · Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `iptables -L` lists current rules. Rules are organized into chains (`INPUT`,
> `OUTPUT`, `FORWARD`); each rule matches traffic and specifies a target (`ACCEPT`, `DROP`,
> `REJECT`). `ufw` and `firewalld` are both simplified front ends that ultimately configure the
> same underlying rules.

## Purpose

`iptables` configures the kernel's packet-filtering rules directly — see
[Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
for how it relates to the higher-level tools built on top of it.

## Syntax

```
iptables [-t TABLE] COMMAND CHAIN RULE-SPEC [-j TARGET]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `CHAIN` | Which chain to act on: `INPUT`, `OUTPUT`, `FORWARD` | Yes |
| `RULE-SPEC` | The matching criteria (protocol, port, source, etc.) | Yes, for rule commands |

## Options

| Flag | Meaning |
|---|---|
| `-L` | List current rules |
| `-A CHAIN` | Append a rule to the end of a chain |
| `-I CHAIN` | Insert a rule at the top of a chain |
| `-D CHAIN` | Delete a matching rule |
| `-p PROTOCOL` | Match a protocol (`tcp`, `udp`, `icmp`) |
| `--dport PORT` | Match a destination port |
| `-j TARGET` | The action to take: `ACCEPT`, `DROP`, `REJECT` |
| `-n` | Numeric output — skip name resolution |
| `-N CHAIN` | Create a new, custom (user-defined) chain |
| `-F [CHAIN]` | Flush (delete every rule in) a chain, or every chain if omitted |
| `-P CHAIN TARGET` | Set the default policy for a built-in chain (e.g. `DROP`) |
| `--line-numbers` | Show rule line numbers alongside `-L`, needed to target a specific rule for `-D`/insertion |

## Examples

```
$ iptables -L -n
```
List current rules for all chains, numerically.

```
$ iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```
Append a rule allowing inbound TCP traffic on port 443.

```
$ iptables -A INPUT -p tcp --dport 22 -s 203.0.113.0/24 -j ACCEPT
```
Allow SSH only from a specific address range.

```
$ iptables -P INPUT DROP
```
Set the default policy for the `INPUT` chain to drop anything not explicitly allowed.

```
$ iptables -N RATE_LIMIT
$ iptables -A INPUT -p tcp --dport 22 -j RATE_LIMIT
```
Create a custom chain and jump matching traffic into it — a way to organize related rules
(e.g. all rate-limiting logic) separately from the main built-in chains. See
[Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md) for the
full pattern.

```
$ iptables-save > /etc/iptables/rules.v4
```
Persist the current rule set to a file so it survives a reboot — raw `iptables` rules are
otherwise runtime-only (see Common Errors below).

## Expected Output

```
$ iptables -L INPUT -n
Chain INPUT (policy DROP)
target     prot opt source               destination
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
ACCEPT     tcp  --  203.0.113.0/24       0.0.0.0/0            tcp dpt:22
```

## Exit Status

`0` on success, non-zero on invalid rule syntax or insufficient privilege.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `iptables: Permission denied` | Not running as root | Use `sudo` |
| A rule added but traffic still blocked | Rule order matters — an earlier matching rule (e.g. a `DROP`) may take effect first | Check rule order with `-L --line-numbers`; use `-I` to insert at the top if needed |
| Changes lost after reboot | Rules aren't persisted automatically | Use your distro's persistence mechanism (`iptables-save`/`iptables-persistent`, or a higher-level tool like `firewalld`/`ufw`) |

## Security Considerations

Rule order is critical — chains are evaluated top to bottom, and the first matching rule wins.
Setting a default-drop policy (`iptables -P INPUT DROP`) before adding explicit allow rules is
the standard hardening posture; doing it in the wrong order (drop policy before the allow rules
exist) can lock out remote access, including your own SSH session.

## Performance Considerations

Rule evaluation happens per-packet; an excessively long, unordered rule set can add measurable
latency on very high-traffic systems — put the most frequently matched rules earlier in the chain.

## Compatibility Notes

Many current distros run `nftables` underneath by default, with `iptables` provided as a
compatibility layer translating to it — functionally equivalent for most everyday use, but worth
knowing if troubleshooting very low-level behavior.

## Production Usage

Most production systems don't hand-write raw `iptables` rules directly anymore — they configure
`ufw` or `firewalld`, which manage the underlying `iptables`/`nftables` rules for you.
Direct `iptables` use remains common for one-off diagnostics (`iptables -L -n`) and for
understanding what a higher-level tool actually configured underneath.

## Related Commands

- [`ufw`](ufw.md) — simplified front end (Debian/Ubuntu default)
- [`firewall-cmd`](firewall-cmd.md) — simplified front end (RHEL family default)

## Related Concepts

- [Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
- [Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md)
