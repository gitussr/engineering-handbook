---
title: "tcpdump — Capture and Inspect Network Traffic"
description: "Capture live packets crossing a network interface, filtered by host, port, or protocol — the ground truth when logs alone don't show what's actually happening on the wire."
relatedConcepts: ["27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli", "27-linux-for-cybersecurity/log-analysis-for-security-incidents"]
relatedCommands: ["nmap", "tshark"]
careerRelevance: ["cybersecurity", "devops", "sre", "platform"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-29"
keywords: ["tcpdump command", "tcpdump examples", "tcpdump filter port", "tcpdump write pcap", "capture network traffic linux"]
canonicalUrl: "/commands/tcpdump"
---

# tcpdump

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · SRE · Platform

> **TL;DR:** `tcpdump -i INTERFACE` captures live traffic; add a filter expression (`port 443`,
> `host 10.0.1.15`) to narrow it, and `-w file.pcap` to save a capture for deeper analysis in
> `tshark` afterward.

## Purpose

`tcpdump` captures packets crossing a network interface in real time — see
[Network Security Tools](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
for the full concept alongside `nmap` and `tshark`.

## Syntax

```
tcpdump [OPTIONS] [FILTER EXPRESSION]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILTER EXPRESSION` | BPF filter syntax (`host`, `port`, `tcp`, `udp`, combined with `and`/`or`) | No — omitting it captures everything on the interface |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-i` | `--interface` | Capture on a specific interface (`eth0`, `any`) |
| `-w` | `--write-file` | Write raw captured packets to a `.pcap` file instead of printing |
| `-r` | `--read-file` | Read and display packets from a previously saved `.pcap` file |
| `-c` | `--count` | Stop after capturing a specific number of packets |
| `-n` | | Don't resolve hostnames/ports — show raw IPs and port numbers, faster and less noisy |
| `-A` | | Show packet contents in ASCII (useful for plaintext protocols) |
| `-X` | | Show packet contents in hex and ASCII |

## Examples

```
$ sudo tcpdump -i eth0
```
Capture and print all traffic on `eth0` (requires root to open the interface).

```
$ sudo tcpdump -i eth0 port 443
```
Capture only traffic on port 443.

```
$ sudo tcpdump -i eth0 host 198.51.100.77 -c 20
```
Capture the next 20 packets to/from a specific host.

```
$ sudo tcpdump -i eth0 -w capture.pcap port 22
```
Save SSH traffic to a file for later analysis in `tshark`.

```
$ tcpdump -r capture.pcap -n
```
Read back a saved capture without resolving hostnames.

## Expected Output

```
$ sudo tcpdump -i eth0 port 443 -c 3
14:32:01.221033 IP web-03.443 > 198.51.100.10.51201: Flags [S.], seq 0, ack 1, win 65160
14:32:01.221501 IP 198.51.100.10.51201 > web-03.443: Flags [.], ack 1, win 502
14:32:01.222110 IP web-03.443 > 198.51.100.10.51201: Flags [P.], seq 1:518, ack 1
```

## Exit Status

`0` on a clean capture (including capture terminated normally via `-c` or Ctrl-C); non-zero if the
interface can't be opened (commonly a permissions error).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `You don't have permission to capture on that device` | Capturing requires elevated privileges | Run with `sudo` |
| No packets captured despite expected traffic | Wrong interface specified | Confirm the correct interface with [`ip addr`](ip.md) first |
| Overwhelming amount of output | No filter expression applied on a busy interface | Add a filter (`port`, `host`) to narrow the capture |

## Security Considerations

Capturing traffic can expose sensitive data in plaintext protocols (unencrypted HTTP, plaintext
auth) — handle `.pcap` files with the same care as any other sensitive data, and only capture on
networks/interfaces you're authorized to monitor (see
[Linux for Penetration Testing](../docs/27-linux-for-cybersecurity/linux-for-penetration-testing-kali-overview.md)).

## Performance Considerations

An unfiltered capture on a busy interface generates output (and file size, with `-w`) extremely
quickly — always scope a capture with a filter expression and/or `-c` count limit rather than
capturing indiscriminately.

## Production Usage

Production incident response typically captures narrowly and briefly (a specific host or port, a
bounded packet count or time window) rather than leaving `tcpdump` running unattended, both to
limit storage growth and to reduce the scope of sensitive data captured.

## Related Commands

- [`nmap`](nmap.md) — discovers what's listening; `tcpdump` shows what's actually being sent
- [`tshark`](tshark.md) — deeper protocol-level analysis of a capture, often taking a `tcpdump`
  `.pcap` file as input

## Related Concepts

- [Network Security Tools: nmap, tcpdump, Wireshark (CLI)](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
- [Log Analysis for Security Incidents](../docs/27-linux-for-cybersecurity/log-analysis-for-security-incidents.md)
