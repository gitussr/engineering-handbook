---
title: "Networking"
description: "Module 12 of the Linux roadmap — IP/subnet/gateway/DNS fundamentals, interface and routing commands, reachability testing, socket inspection, HTTP tools, name resolution config, firewalls, DNS troubleshooting, bonding, and the port/socket model underneath all of it."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
type: "module-index"
nextTopic: "12-networking/networking-fundamentals"
updatedAt: "2026-07-27"
canonicalUrl: "/docs/networking"
---

# Networking

Module 12 of 34 · Stage: Intermediate · Previous: [11 Services](../11-services/index.md)

This module moves from managing what's running on a single machine (Modules 10–11) to how that
machine talks to everything else. It starts with the four concepts every later topic assumes (IP,
subnet, gateway, DNS), moves through the commands used to inspect and test connectivity day to
day, and ends with the port/socket model that explains what all of those commands were actually
showing you.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Networking Fundamentals: IP, Subnet, Gateway, DNS](networking-fundamentals.md) | 🟢 Must Know |
| [ip addr, ip route (and legacy ifconfig)](ip-addr-ip-route-ifconfig.md) | 🟢 Must Know |
| [ping, traceroute, mtr](ping-traceroute-mtr.md) | 🟢 Must Know |
| [netstat, ss](netstat-ss.md) | 🟢 Must Know |
| [curl, wget](curl-wget.md) | 🟢 Must Know |
| [/etc/hosts, /etc/resolv.conf](etc-hosts-resolv-conf.md) | 🟢 Must Know |
| [Firewalls: iptables, firewalld, ufw](firewalls-iptables-firewalld-ufw.md) | 🟡 Good to Know |
| [DNS Troubleshooting: dig, nslookup](dns-troubleshooting-dig-nslookup.md) | 🟡 Good to Know |
| [Network Interfaces and Bonding](network-interfaces-and-bonding.md) | 🔴 Expert |
| [Ports and Sockets Explained](ports-and-sockets-explained.md) | 🟢 Must Know |

## What you should be able to do after this module

- Explain what an IP address, subnet mask, default gateway, and DNS lookup each do, and how they
  relate to each other.
- Inspect a machine's own IP configuration and routing table with `ip addr`/`ip route`, and
  recognize legacy `ifconfig` output in older documentation.
- Test reachability and localize where a connection breaks down with `ping`, `traceroute`, and
  `mtr`.
- Inspect listening ports and active connections with `ss`, and translate legacy `netstat` syntax
  to it.
- Test and debug HTTP(S) endpoints with `curl`, and download files with `wget`.
- Explain how `/etc/hosts` and `/etc/resolv.conf` control name resolution before/alongside DNS.
- Configure basic firewall rules with `ufw`, `firewalld`, or `iptables`, and explain why they're
  different front ends over the same underlying mechanism.
- Query DNS directly with `dig` to isolate a resolution problem from a routing or local-config
  problem.
- Explain what network interface bonding solves and the tradeoff between its common modes.
- Explain what a port and a socket actually are, and why protocol (TCP/UDP) matters alongside a
  port number.

## Known, intentional gaps in this module

- Canonical command pages exist for `ip`, `ping`, `traceroute`, `ss`, `curl`, `wget`, `dig`,
  `iptables`, `ufw`, `firewall-cmd`, and `nslookup` — eleven commands, reflecting how
  command-dense this module's roadmap entry is.
- `ifconfig` (legacy, superseded by `ip`), `mtr` (combines `ping`+`traceroute`, often not
  installed by default), and `netstat` (legacy, superseded by `ss`) are covered in full within
  their topic pages but don't get separate canonical command pages, the same treatment `htop`
  received in Module 10.
- `nmap`, `tcpdump`, and `wireshark` (CLI) are referenced ahead of their own module — full
  coverage in [Module 27: Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md).
- SSH itself (`ssh`, `scp`, `sftp`, `rsync`) is deliberately out of scope here — it gets its own
  full module next.

**Previous module:** [11 Services](../11-services/index.md)
**Next module:** [13 SSH →](../13-ssh/index.md)
