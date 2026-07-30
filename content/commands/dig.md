---
title: "dig — Query DNS Directly"
description: "Query a DNS server directly for a specific record, showing full detail — TTL, record type, and which server answered."
relatedConcepts: ["12-networking/dns-troubleshooting-dig-nslookup"]
relatedCommands: ["nslookup"]
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
difficulty: "good-to-know"
compatibility: [{"context": "bind-utils / dnsutils", "note": "May need separate installation on minimal distro installs (package name differs: bind-utils on RHEL family, dnsutils on Debian/Ubuntu)."}]
updatedAt: "2026-07-27"
keywords: ["dig command examples", "dig @server", "dig mx record", "dns query linux"]
canonicalUrl: "/commands/dig"
---

# dig

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** `dig hostname` queries DNS and shows the full answer, including TTL and which server
> responded. `dig hostname @server` queries a specific DNS server directly, bypassing local
> resolver configuration — the key move for isolating a DNS record from local caching issues.

## Purpose

`dig` queries DNS directly and shows detailed results — see
[DNS Troubleshooting: dig, nslookup](../docs/12-networking/dns-troubleshooting-dig-nslookup.md)
for the full troubleshooting context.

## Syntax

```
dig [OPTIONS] NAME [TYPE] [@SERVER]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `NAME` | The hostname to query | Yes |
| `TYPE` | Record type (`A`, `MX`, `TXT`, `NS`, etc.) | No — defaults to `A` |
| `@SERVER` | A specific DNS server to query directly | No — defaults to the system's configured resolver |

## Options

| Flag | Meaning |
|---|---|
| `+short` | Print just the answer, no extra detail |
| `+trace` | Trace the full delegation path from the root nameservers down |
| `-x IP` | Reverse lookup — find the hostname for an IP address |

## Examples

```
$ dig example.com
```
Full query using the default resolver.

```
$ dig example.com @8.8.8.8
```
Query a specific public resolver directly, bypassing local resolver configuration.

```
$ dig example.com MX
```
Query mail exchange records specifically.

```
$ dig +short example.com
```
Print just the resolved IP address, no extra detail — useful in scripts.

## Expected Output

```
$ dig example.com

;; ANSWER SECTION:
example.com.        86400   IN      A       93.184.216.34

;; Query time: 24 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
```

`ANSWER SECTION` is the resolved record; `Query time` and `SERVER` show how long it took and which
resolver actually answered.

## Exit Status

`0` on success (even if the record doesn't exist — that's a valid, successful "no answer"
response), non-zero on a malformed query or unreachable server.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Empty `ANSWER SECTION` | The record genuinely doesn't exist, or the wrong record type was queried | Confirm the expected record type (`A`, `CNAME`, `MX`, etc.) |
| `connection timed out; no servers could be reached` | The queried DNS server (often via `@server`) is unreachable | Check network connectivity to that specific server, or the system's default resolver config |
| Different answer than expected | Local resolver caching an old value, or querying a different server than assumed | Query a known-good public resolver directly with `@8.8.8.8` to compare |

## Security Considerations

DNS responses can be spoofed on untrusted networks; for anything security-sensitive, corroborate a
`dig` result against a trusted resolver rather than assuming any single answer is authoritative.

## Performance Considerations

Negligible for individual queries; `+trace` performs multiple queries down the DNS hierarchy and
is noticeably slower — use it only when the full delegation path actually needs inspecting.

## Compatibility Notes

`dig` comes from `bind-utils` (RHEL family) or `dnsutils` (Debian/Ubuntu family) and may need
explicit installation on a minimal server image.

## Production Usage

`dig hostname @8.8.8.8` (querying a known public resolver directly) is the standard way to confirm
whether a DNS record is actually correct at the source, independent of whatever a specific
machine's local resolver has cached.

## Related Commands

- [`nslookup`](nslookup.md) — simpler, older alternative

## Related Concepts

- [DNS Troubleshooting: dig, nslookup](../docs/12-networking/dns-troubleshooting-dig-nslookup.md)
