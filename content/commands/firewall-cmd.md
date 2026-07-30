---
title: "firewall-cmd — Manage firewalld Zones and Rules"
description: "The command-line front end for firewalld, RHEL/CentOS/Rocky/Fedora's default firewall manager, organized around reusable zones."
relatedConcepts: ["12-networking/firewalls-iptables-firewalld-ufw", "12-networking/ports-and-sockets-explained", "19-security/firewall-configuration-in-depth"]
relatedCommands: ["iptables", "ufw"]
careerRelevance: ["devops", "cybersecurity", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: [{"context": "RHEL/CentOS/Rocky/Fedora", "note": "Default firewall manager on RHEL-family distros. Not installed by default on Debian/Ubuntu, which default to ufw instead."}]
updatedAt: "2026-07-28"
keywords: ["firewall-cmd examples", "firewalld zones explained", "firewall-cmd add-port", "firewalld permanent rule", "firewall-cmd rich rule", "firewall-cmd panic mode"]
canonicalUrl: "/commands/firewall-cmd"
---

# firewall-cmd

🟡 Good to Know · Relevant for: DevOps · Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `firewall-cmd --add-port=443/tcp --permanent` opens a port persistently; without
> `--permanent`, a change only lasts until reload/reboot. Rules are grouped into zones (`public`,
> `internal`, etc.) rather than a flat rule list.

## Purpose

`firewall-cmd` manages `firewalld`, the default firewall manager on RHEL-family distros — see
[Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
for how it compares to `iptables` and `ufw`.

## Syntax

```
firewall-cmd [OPTIONS]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `--state` | Check whether firewalld is running |
| `--list-all` | Show all rules for the active (default) zone |
| `--list-ports` | Show explicitly opened ports for the active zone |
| `--add-port=PORT/PROTOCOL` | Open a port for a protocol |
| `--remove-port=PORT/PROTOCOL` | Close a previously opened port |
| `--permanent` | Make a change persist across reloads/reboots (without it, the change is runtime-only) |
| `--reload` | Apply permanent changes to the running configuration |
| `--zone=ZONE` | Target a specific zone instead of the default |
| `--add-service=SERVICE` | Open a predefined service profile (e.g. `http`, `https`) instead of a raw port |
| `--add-rich-rule='RULE'` | Add a more expressive rule — source-restricted, logged, or rate-limited — beyond a plain port open |
| `--panic-on` / `--panic-off` | Immediately block/unblock all traffic — an emergency killswitch during an active incident |
| `--get-services` | List all predefined service profiles available for `--add-service` |

## Examples

```
$ firewall-cmd --state
```
Check whether firewalld is active.

```
$ firewall-cmd --list-ports
```
List explicitly opened ports in the active zone.

```
$ firewall-cmd --add-port=443/tcp --permanent
$ firewall-cmd --reload
```
Open port 443 permanently, then apply it.

```
$ firewall-cmd --zone=public --add-port=22/tcp --permanent
```
Open a port in a specific named zone.

```
$ firewall-cmd --add-rich-rule='rule family="ipv4" source address="203.0.113.0/24" port port="22" protocol="tcp" accept' --permanent
$ firewall-cmd --reload
```
Allow SSH only from a specific address range — a rich rule expresses source restrictions a plain
`--add-port` can't. See
[Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md) for the
full pattern.

```
$ firewall-cmd --panic-on
```
Immediately block all traffic in an active-incident emergency — use `--panic-off` to restore
normal filtering once resolved.

## Expected Output

```
$ firewall-cmd --list-ports
443/tcp

$ firewall-cmd --add-port=8080/tcp --permanent
success
```

## Exit Status

`0` on success, non-zero if firewalld isn't running or the syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Change works now but disappears after reboot | `--permanent` wasn't used | Re-run with `--permanent`, then `--reload` |
| `--add-port` runs but rule doesn't seem active | Ran with `--permanent` but forgot `--reload` | Run `firewall-cmd --reload` to apply it |
| `FirewallD is not running` | firewalld service isn't started | Start it via `systemctl start firewalld` (Module 11) |

## Security Considerations

Zones let different network contexts (e.g. a `public`-facing interface vs. an `internal` one)
have genuinely different rule sets on the same machine — using the wrong zone for an interface is
a common way to end up either over- or under-exposed.

## Performance Considerations

Negligible for typical rule counts; `firewalld` reloads apply new rules without dropping existing
tracked connections, unlike some raw `iptables` reload approaches.

## Compatibility Notes

`firewalld` is the default on RHEL/CentOS/Rocky/Fedora; it's not installed by default on
Debian/Ubuntu, which default to `ufw` instead — know which one your target distro actually uses.

## Production Usage

The `--add-port ... --permanent` followed by `--reload` pattern is the standard, safe way to open
a port on a RHEL-family server without risking the change being lost on the next reboot.

## Related Commands

- [`iptables`](iptables.md) — the lower-level mechanism firewalld manages underneath
- [`ufw`](ufw.md) — the Debian/Ubuntu-family equivalent front end

## Related Concepts

- [Firewalls: iptables, firewalld, ufw](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)
- [Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md)
