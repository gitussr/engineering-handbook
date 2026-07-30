---
title: "ufw — Uncomplicated Firewall"
description: "Debian/Ubuntu's simplified firewall front end — allow/deny rules by port or app profile, built on top of iptables."
relatedConcepts: ["12-networking/firewalls-iptables-firewalld-ufw", "12-networking/ports-and-sockets-explained", "19-security/firewall-configuration-in-depth"]
relatedCommands: ["iptables", "firewall-cmd"]
careerRelevance: ["devops", "cybersecurity", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: [{"context": "Debian/Ubuntu", "note": "Default firewall front end on Debian/Ubuntu. Not installed by default on RHEL-family distros, which default to firewalld instead."}]
updatedAt: "2026-07-28"
keywords: ["ufw allow port", "ufw status", "ufw enable", "ufw command examples", "ufw rate limit", "ufw logging"]
canonicalUrl: "/commands/ufw"
---

# ufw

🟡 Good to Know · Relevant for: DevOps · Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `ufw allow 443/tcp` opens a port. `ufw status` shows current rules and whether the
> firewall is active. Simpler syntax than raw `iptables`, and the Debian/Ubuntu-family default.

## Purpose

`ufw` manages firewall rules with simplified syntax on Debian/Ubuntu — see
[Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
for how it compares to `iptables` and `firewalld`.

## Syntax

```
ufw [OPTIONS] COMMAND
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action: `enable`, `status`, `allow`, `deny`, etc. | Yes |

## Options

| Flag | Meaning |
|---|---|
| `enable` / `disable` | Turn the firewall on/off |
| `status` | Show whether it's active and list current rules |
| `allow PORT/PROTOCOL` | Allow traffic on a port/protocol |
| `deny PORT/PROTOCOL` | Deny traffic on a port/protocol |
| `allow from IP` | Allow traffic from a specific address |
| `delete RULE` | Remove a previously added rule |
| `limit PORT/PROTOCOL` | Rate-limit connections to a port, denying an address after repeated rapid attempts (a lightweight, built-in brute-force mitigation) |
| `logging on/off/LEVEL` | Enable/disable logging of firewall activity, or set the verbosity level |
| `reset` | Disable ufw and remove all rules, restoring the pre-configured default |

## Examples

```
$ ufw status
```
Check whether the firewall is active and list current rules.

```
$ ufw allow 443/tcp
```
Allow inbound TCP traffic on port 443.

```
$ ufw allow from 203.0.113.5 to any port 22
```
Allow SSH only from a specific IP address.

```
$ ufw enable
```
Turn the firewall on (applies the default-deny policy plus any configured allow rules).

```
$ ufw limit 22/tcp
```
Rate-limit SSH connection attempts — an address making repeated rapid connections gets
automatically denied for a period, a lightweight complement to
[Fail2ban](../docs/19-security/fail2ban.md) for the same threat. See
[Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md).

```
$ ufw logging on
```
Enable logging of firewall activity, useful when investigating unexpected blocked/allowed
traffic.

## Expected Output

```
$ ufw status
Status: active

To                         Action      From
--                         ------      ----
443/tcp                    ALLOW       Anywhere
22/tcp                     ALLOW       203.0.113.5
```

## Exit Status

`0` on success, non-zero on invalid syntax or insufficient privilege.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `ERROR: initcaught exception uid != 0` (or similar permission error) | Not running as root | Use `sudo` |
| Enabling `ufw` locks out an active SSH session | SSH port wasn't allowed before enabling | Always `ufw allow 22/tcp` (or your actual SSH port) before `ufw enable` |
| Rule added but `ufw status` shows `inactive` | Firewall was never enabled | Run `ufw enable` |

## Security Considerations

Enabling `ufw` without first explicitly allowing the port you're connected over (commonly SSH) can
immediately lock you out of a remote server — always allow the management port first, enable
second.

## Performance Considerations

Negligible for typical rule counts — `ufw` is a thin, simplified layer over `iptables`/`nftables`
rule management.

## Compatibility Notes

`ufw` is the Debian/Ubuntu-family default; it's not installed by default on RHEL-family distros,
which use `firewalld` instead.

## Production Usage

`ufw allow 22/tcp` before `ufw enable` is the standard, non-negotiable order of operations on any
remote server — reversing it is one of the most common ways engineers accidentally lock
themselves out of a box they only have SSH access to.

## Related Commands

- [`iptables`](iptables.md) — the underlying mechanism `ufw` manages
- [`firewall-cmd`](firewall-cmd.md) — the RHEL-family equivalent front end

## Related Concepts

- [Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
- [Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md)
