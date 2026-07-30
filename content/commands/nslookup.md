---
title: "nslookup — Query DNS (Legacy)"
description: "A simpler, older DNS query tool — still available almost everywhere, but considered legacy for serious troubleshooting in favor of dig."
relatedConcepts: ["12-networking/dns-troubleshooting-dig-nslookup"]
relatedCommands: ["dig"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "good-to-know"
compatibility: [{"context": "General availability", "note": "Available on a wider range of systems by default (including Windows) than dig, useful when only a quick cross-platform check is needed."}]
updatedAt: "2026-07-27"
keywords: ["nslookup command examples", "nslookup vs dig", "nslookup linux", "dns lookup command"]
canonicalUrl: "/commands/nslookup"
---

# nslookup

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `nslookup hostname` performs a quick DNS lookup with simpler output than `dig`.
> Useful for a fast sanity check, but reach for `dig` when you need real troubleshooting detail
> (TTL, record type, which server answered).

## Purpose

`nslookup` performs a basic DNS query — see
[DNS Troubleshooting: dig, nslookup](../docs/12-networking/dns-troubleshooting-dig-nslookup.md)
for how it compares to `dig`.

## Syntax

```
nslookup [NAME] [SERVER]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `NAME` | Hostname (or IP, for a reverse lookup) to query | Yes |
| `SERVER` | A specific DNS server to query instead of the default | No |

## Options

`nslookup` is mostly argument-driven rather than flag-driven; it also has an interactive mode
(run with no arguments) not commonly used in scripts or quick checks.

## Examples

```
$ nslookup example.com
```
Basic lookup using the system's default resolver.

```
$ nslookup example.com 8.8.8.8
```
Query a specific DNS server directly.

```
$ nslookup 93.184.216.34
```
Reverse lookup — find the hostname associated with an IP address.

## Expected Output

```
$ nslookup example.com
Server:         192.168.1.1
Address:        192.168.1.1#53

Name:   example.com
Address: 93.184.216.34
```

## Exit Status

`0` on success (including a "no such record" result — that's still a successful query), non-zero
if the query couldn't be sent at all.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `** server can't find NAME: NXDOMAIN` | The record genuinely doesn't exist | Confirm the hostname is correct |
| `connection timed out; no servers could be reached` | The DNS server (default or specified) is unreachable | Check network connectivity or resolver configuration |
| Output lacks detail needed to diagnose an issue | `nslookup`'s output is intentionally minimal | Switch to [`dig`](dig.md) for TTL, record type, and full answer-section detail |

## Security Considerations

Same as any DNS query tool — don't treat a single unauthenticated response as fully trustworthy
on an untrusted network; corroborate against a known-good resolver if it matters.

## Performance Considerations

Negligible — a single lightweight query.

## Compatibility Notes

`nslookup` is available by default on a wider range of systems (including Windows) than `dig`,
making it useful for a quick, portable sanity check even outside Linux.

## Production Usage

`nslookup` remains useful as a fast, no-install-required first check, especially when working
across mixed operating systems — but `dig` is the standard choice once real troubleshooting detail
is needed.

## Related Commands

- [`dig`](dig.md) — more detailed, modern standard for DNS troubleshooting

## Related Concepts

- [DNS Troubleshooting: dig, nslookup](../docs/12-networking/dns-troubleshooting-dig-nslookup.md)
