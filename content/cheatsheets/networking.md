---
title: "Linux Networking Cheat Sheet"
description: "A single-page quick reference for Linux networking commands: IP and interfaces, connectivity testing, DNS, ports and connections, and firewalls."
type: "cheatsheet"
module: "networking"
updatedAt: "2026-07-30"
keywords: ["linux networking cheat sheet", "linux network commands cheat sheet", "ip command cheat sheet", "linux firewall cheat sheet"]
canonicalUrl: "/cheatsheets/networking"
---

# Linux Networking Cheat Sheet

The commands for inspecting, testing, and troubleshooting a Linux server's network — from
checking an IP address to diagnosing why a connection is failing.

## IP addresses and interfaces

| Command | Description |
|---|---|
| [`ip`](../commands/ip.md) `addr` | Show IP addresses assigned to all interfaces |
| `ip route` | Show the routing table (default gateway and beyond) |
| `ip link` | Show network interfaces and their up/down state |
| `ifconfig` | Legacy equivalent of `ip addr` — still common on older systems |

Full explanation → [ip addr, ip route (and ifconfig)](../docs/12-networking/ip-addr-ip-route-ifconfig.md)

## Connectivity testing

| Command | Description |
|---|---|
| [`ping`](../commands/ping.md) `host` | Test basic reachability and round-trip latency |
| [`traceroute`](../commands/traceroute.md) `host` | Show the hop-by-hop path packets take to a host |
| `mtr host` | Combines `ping` and `traceroute` into a live, continuously-updating view |

Full explanation → [ping, traceroute, mtr](../docs/12-networking/ping-traceroute-mtr.md)

## DNS

| Command | Description |
|---|---|
| [`dig`](../commands/dig.md) `domain` | Query DNS records with full detail — the modern, preferred tool |
| [`nslookup`](../commands/nslookup.md) `domain` | Simpler, older DNS lookup tool |
| `/etc/hosts` | Static hostname-to-IP overrides, checked before DNS |
| `/etc/resolv.conf` | Configures which DNS resolvers the system queries |

Full explanation → [DNS Troubleshooting (dig, nslookup)](../docs/12-networking/dns-troubleshooting-dig-nslookup.md) · [/etc/hosts, /etc/resolv.conf](../docs/12-networking/etc-hosts-resolv-conf.md)

## Ports and connections

| Command | Description |
|---|---|
| `ss -tuln` | List listening TCP/UDP ports (modern, preferred) |
| `netstat -tuln` | Legacy equivalent of `ss` |
| `ss -tan state established` | List established TCP connections |

Full explanation → [netstat, ss](../docs/12-networking/netstat-ss.md) · [Ports and Sockets Explained](../docs/12-networking/ports-and-sockets-explained.md)

## Transferring data

| Command | Description |
|---|---|
| [`curl`](../commands/curl.md) `-O url` | Fetch a URL, optionally saving output to a file |
| [`wget`](../commands/wget.md) `url` | Download a file from a URL, resumable by default for interrupted transfers |

Full explanation → [curl, wget](../docs/12-networking/curl-wget.md)

## Firewalls

| Command | Description |
|---|---|
| [`ufw`](../commands/ufw.md) `allow 22/tcp` | Simplest firewall front-end (Debian/Ubuntu-oriented) |
| [`firewall-cmd`](../commands/firewall-cmd.md) `--add-port=22/tcp --permanent` | Firewalld's CLI (RHEL/CentOS-oriented) |
| [`iptables`](../commands/iptables.md) `-L -n` | List the low-level netfilter rules underneath both front-ends |

Full explanation → [Firewalls (iptables, firewalld, ufw)](../docs/12-networking/firewalls-iptables-firewalld-ufw.md)

## Related topics

- [SSH Cheat Sheet](ssh.md)
- [Linux Commands Cheat Sheet](linux-commands.md)
- [Networking Module](../docs/12-networking/index.md)
- [Cheat Sheets Hub](index.md)
