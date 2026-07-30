---
title: "nmap — Network Discovery and Port Scanner"
description: "Discover live hosts, open ports, and running services on a network, from outside the target — the standard first step in any authorized network security assessment."
relatedConcepts: ["27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli", "27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview"]
relatedCommands: ["tcpdump", "ss"]
careerRelevance: ["cybersecurity", "devops", "sre"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-29"
keywords: ["nmap command", "nmap port scan", "nmap service version detection", "nmap examples", "nmap sV sT sS"]
canonicalUrl: "/commands/nmap"
---

# nmap

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · SRE

> **TL;DR:** `nmap TARGET` discovers what's alive and listening; `nmap -sV TARGET` additionally
> identifies what service and version is behind each open port — always run only against targets
> you're explicitly authorized to scan.

## Purpose

`nmap` (Network Mapper) discovers hosts on a network and the ports/services running on them — see
[Network Security Tools](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
for the full concept and authorization requirements.

## Syntax

```
nmap [OPTIONS] TARGET
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `TARGET` | An IP address, hostname, or CIDR range to scan | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-sV` | `--version-detection` | Identify service/version running on each open port |
| `-sS` | `--syn-scan` | Fast, stealthy TCP SYN ("half-open") scan — requires root |
| `-sT` | `--connect-scan` | Full TCP connect scan — no elevated privileges required |
| `-p` | `--ports` | Scan specific ports/ranges instead of the default top 1000 |
| `-O` | `--os-detection` | Attempt operating system fingerprinting |
| `-A` | `--aggressive` | Enable OS detection, version detection, script scanning, and traceroute together |
| `-oN`/`-oX` | `--output-normal`/`--output-xml` | Save results in normal or XML format |

## Examples

```
$ nmap 10.0.1.15
```
Scan the default top 1000 ports on a single host.

```
$ nmap -sV 10.0.1.15
```
Scan and identify the service/version behind each open port.

```
$ nmap -p 1-65535 10.0.1.15
```
Scan every port instead of just the common ones.

```
$ sudo nmap -sS -O 10.0.1.15
```
Fast SYN scan with OS fingerprinting (requires root for the raw-socket SYN scan).

```
$ nmap 10.0.1.0/24
```
Discover live hosts across an entire subnet.

## Expected Output

```
$ nmap -sV 10.0.1.15
Starting Nmap 7.94 ( https://nmap.org )
Nmap scan report for 10.0.1.15
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9
443/tcp  open  ssl/http nginx 1.24.0

Nmap done: 1 IP address (1 host up) scanned in 4.21 seconds
```

## Exit Status

`0` on a successfully completed scan (regardless of what it finds); non-zero if `nmap` itself
fails to run (invalid target, permission error for a scan type requiring root).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `You requested a scan type which requires root privileges` | `-sS`/`-O` need raw socket access | Run with `sudo`, or use `-sT` instead |
| Host reported as `down` when you know it's up | ICMP ping blocked by a firewall | Add `-Pn` to skip the initial host-discovery ping |
| Scan takes far longer than expected | Scanning all 65535 ports, or a slow/filtered network | Scan the default top ports first, narrow down from there |

## Security Considerations

Running `nmap` against any host or network without explicit authorization is unauthorized access
in most jurisdictions — always confirm written authorization before scanning anything you don't
own (see
[Linux for Penetration Testing](../docs/27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview.md)).
A scan itself can also trigger intrusion-detection alerts, which is expected and often intentional
during an authorized assessment.

## Performance Considerations

Scanning all 65535 ports (`-p 1-65535`) takes substantially longer than the default top-1000-port
scan — for a large network, scope scans narrowly (specific hosts, specific port ranges) rather than
scanning everything by default.

## Production Usage

Production security teams typically integrate `nmap` into scheduled, authorized scanning rather
than running it ad hoc — feeding results into asset inventory and vulnerability management tooling
rather than reading raw terminal output for a large network.

## Related Commands

- [`tcpdump`](tcpdump.md) — captures actual traffic, where `nmap` only probes and reports what's
  listening
- [`ss`](ss.md) — the local equivalent (Module 12): what's listening from inside the host itself

## Related Concepts

- [Network Security Tools: nmap, tcpdump, Wireshark (CLI)](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
- [Linux for Penetration Testing (Kali Overview)](../docs/27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview.md)
