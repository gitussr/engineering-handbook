---
title: "tshark — Wireshark's Command-Line Packet Analyzer"
description: "Wireshark's full protocol-analysis engine, used from the terminal — filtering and inspecting a live capture or a saved .pcap file at the protocol level."
relatedConcepts: ["27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli", "27-linux-for-cybersecurity/log-analysis-for-security-incidents"]
relatedCommands: ["tcpdump", "nmap"]
careerRelevance: ["cybersecurity", "devops", "sre"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-29"
keywords: ["tshark command", "tshark examples", "tshark filter", "wireshark cli", "tshark read pcap"]
canonicalUrl: "/commands/tshark"
---

# tshark

🟡 Good to Know · Relevant for: Cybersecurity · DevOps · SRE

> **TL;DR:** `tshark -r file.pcap -Y "FILTER"` analyzes a saved capture using Wireshark's full
> protocol-decoding engine and display-filter syntax — the terminal-based way to do what Wireshark
> does graphically, useful for scripting and headless servers alike.

## Purpose

`tshark` is Wireshark's command-line component — the roadmap bullet naming "wireshark (CLI)" refers
to this binary specifically, since Wireshark's graphical application has no meaningful command-line
mode of its own. See
[Network Security Tools](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
for the full concept alongside `nmap` and `tcpdump`.

## Syntax

```
tshark [OPTIONS]
```

## Arguments

`tshark` takes no positional arguments — all behavior is controlled through flags below.

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-i` | `--interface` | Capture live from a specific interface, like `tcpdump` |
| `-r` | `--read-file` | Read a saved `.pcap` file (often produced by `tcpdump -w`) |
| `-w` | `--write-file` | Write a live capture to a file |
| `-Y` | `--display-filter` | Apply a Wireshark display filter (e.g. `http.request`, `dns`) |
| `-c` | `--count` | Stop after a specific number of packets |
| `-T` | `--format` | Output format (`fields`, `json`, `pdml`) for scripted processing |
| `-z` | `--statistics` | Print protocol-specific statistics (e.g. `-z conv,tcp` for TCP conversations) |

## Examples

```
$ tshark -r capture.pcap
```
Display every packet in a saved capture with protocol decoding.

```
$ tshark -r capture.pcap -Y "http.request"
```
Show only HTTP request packets from a capture.

```
$ tshark -r capture.pcap -Y "dns" -T fields -e dns.qry.name
```
Extract just the queried domain names from every DNS packet in a capture.

```
$ sudo tshark -i eth0 -Y "tcp.port == 443"
```
Live-capture and filter for HTTPS traffic directly, without a separate `tcpdump` step first.

```
$ tshark -r capture.pcap -z conv,tcp
```
Summarize TCP conversations (who talked to whom, how much data) in a capture.

## Expected Output

```
$ tshark -r capture.pcap -Y "dns" -T fields -e dns.qry.name
a1b2c3.exfil-domain.example
d4e5f6.exfil-domain.example
```

## Exit Status

`0` on successful read/capture and filter application; non-zero if the input file doesn't exist or
the display filter syntax is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `tshark: "X" isn't a valid filter` | Invalid display filter syntax | Check the filter expression against Wireshark's display filter reference |
| No output despite a valid capture | Filter matches nothing in this specific capture | Confirm the filter logic against a broader, unfiltered read first |
| `Permission denied` on live capture | Capturing an interface requires elevated privileges | Run with `sudo`, same as `tcpdump` |

## Security Considerations

The same sensitivity concerns as [`tcpdump`](tcpdump.md) apply — captures can contain plaintext
sensitive data, and analysis should only happen on captures you're authorized to have and inspect.

## Performance Considerations

`-T fields`/`-T json` output is significantly more efficient for scripted, automated analysis of
large captures than the default human-readable format, which is optimized for interactive reading.

## Production Usage

Incident responders commonly use `tcpdump` to capture quickly and narrowly during a live incident,
then hand the resulting `.pcap` off to `tshark` (or full graphical Wireshark, when a GUI is
available) for the deeper protocol-level analysis that finds the actual indicator of compromise.

## Related Commands

- [`tcpdump`](tcpdump.md) — typically captures the `.pcap` file `tshark` then analyzes
- [`nmap`](nmap.md) — discovers what's listening; `tshark` analyzes what's actually being sent

## Related Concepts

- [Network Security Tools: nmap, tcpdump, Wireshark (CLI)](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
- [Log Analysis for Security Incidents](../docs/27-linux-for-cybersecurity/log-analysis-for-security-incidents.md)
