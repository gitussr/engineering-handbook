---
title: "DNS Troubleshooting: dig, nslookup"
description: "Querying DNS directly to isolate resolution problems from routing problems, and to see exactly what a specific DNS server returns."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/firewalls-iptables-firewalld-ufw"]
relatedTopics: ["networking-fundamentals", "etc-hosts-resolv-conf"]
relatedCommands: ["dig", "nslookup"]
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#dig-vs-nslookup"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/network-interfaces-and-bonding"
prevTopic: "12-networking/firewalls-iptables-firewalld-ufw"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["dig command examples", "nslookup command examples", "dns troubleshooting linux", "check dns record"]
canonicalUrl: "/docs/networking/dns-troubleshooting-dig-nslookup"
---

# DNS Troubleshooting: dig, nslookup

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** `dig` queries DNS directly and shows the full, detailed response — the standard tool
> for DNS troubleshooting. `nslookup` does a simpler version of the same job and is more
> universally available across older systems, but is considered legacy for serious
> troubleshooting.

## What is it?

Two command-line tools for querying DNS directly, bypassing `/etc/hosts` entirely, to see exactly
what a DNS server returns for a given name — the direct troubleshooting counterpart to the DNS
concept introduced in [Networking Fundamentals](networking-fundamentals.md).

## Why does it exist?

[Networking Fundamentals](networking-fundamentals.md) established that testing by raw IP address
isolates DNS as the suspect. `dig` and `nslookup` are how you go a level deeper once DNS itself is
the suspect: querying a specific DNS server directly, checking record types beyond the basic A
record, and confirming whether a problem is in DNS itself versus in local resolution configuration
([`/etc/hosts`/`/etc/resolv.conf`](etc-hosts-resolv-conf.md)).

## Where is it used?

Confirming a DNS record actually exists and points where it's expected to, checking whether a
change to a DNS record has propagated, querying a specific DNS server directly instead of relying
on the system's configured resolver, and diagnosing whether a resolution failure is DNS-side or
local-configuration-side.

## How it works

> 📊 Diagram: a `dig example.com` query shown traveling to a specific DNS server and returning an
> annotated response section (ANSWER SECTION with the resolved IP, TTL, and record type) —
> contrasted with a simpler `nslookup` output showing the same underlying answer with far less
> detail.

| Command | Behavior |
|---|---|
| `dig example.com` | Full, detailed query — shows the answer section, TTL, query time, which server answered |
| `dig example.com @8.8.8.8` | Query a specific DNS server directly, bypassing local resolver config |
| `dig example.com MX` | Query a specific record type (mail exchange, in this example) |
| `nslookup example.com` | Simpler equivalent query, less detail, considered legacy for serious troubleshooting |

Querying a specific server directly with `dig ... @server` is the key troubleshooting move: it
tells you whether the DNS record itself is correct at the source, independent of whatever
resolver your own machine happens to be configured to use.

## Real-world example

A DNS record was just updated to point a hostname at a new server, but a specific machine still
resolves it to the old IP. `dig hostname @8.8.8.8` (querying a public resolver directly) shows the
new, correct IP — proving the DNS record itself is right. `dig hostname` (using the machine's own
configured resolver) still shows the old IP, isolating the problem to local resolver caching or
configuration on that one machine, not the DNS record itself.

## Commands

- [`dig`](../../commands/dig.md) — full syntax and examples

`nslookup` is covered as a canonical command page too, but treated as the legacy, simpler
alternative — see [`nslookup`](../../commands/nslookup.md).

## Production example

```
$ dig example.com

;; ANSWER SECTION:
example.com.        86400   IN      A       93.184.216.34

;; Query time: 24 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
```

The `SERVER` line at the bottom shows which resolver actually answered — useful for confirming
whether your query went where you expected.

## Do / Don't

| Do | Don't |
|---|---|
| Query a specific DNS server directly (`dig ... @server`) to isolate the record from local config | Assume a wrong resolution is the DNS record's fault before checking a direct query |
| Use `dig` as the default troubleshooting tool | Rely on `nslookup` for anything beyond a quick, simple check |
| Check the `SERVER` line to confirm which resolver actually answered | Assume every query on a machine uses the same resolver by default |

## Common mistakes

- Concluding a DNS record itself is wrong without querying a specific external server directly to
  rule out local caching or configuration.
- Treating `nslookup`'s simpler output as equivalent to `dig`'s — it omits detail (TTL, which
  server answered, full record data) that's often exactly what's needed.
- Not checking `dig`'s `SERVER` line, missing that a query went to an unexpected resolver.

## Best practices

- Use `dig hostname @8.8.8.8` (or another known-good public resolver) as the standard way to check
  a DNS record independent of local configuration.
- Default to `dig` over `nslookup` for anything beyond the quickest sanity check.
- Cross-reference a `dig` finding against [`/etc/hosts`](etc-hosts-resolv-conf.md) before
  concluding the problem is DNS-side.

## Exercises

1. Run `dig` against any hostname and identify the ANSWER SECTION's resolved IP and TTL.
2. Run the same query against a specific public resolver with `@8.8.8.8` and compare results.
3. Explain in one sentence how querying a specific DNS server directly helps isolate a resolution
   problem.

## Quiz

**Q: What does adding `@8.8.8.8` to a `dig` command do?**
<details><summary>Show answer</summary>
Queries that specific DNS server directly, bypassing the machine's own configured resolver —
useful for isolating whether a DNS record itself is correct at the source.
</details>

**Q: Why is `dig` generally preferred over `nslookup` for serious troubleshooting?**
<details><summary>Show answer</summary>
`dig` shows significantly more detail (TTL, which server answered, full record data) that
`nslookup`'s simpler output omits.
</details>

## Interview questions

- How would you determine whether a DNS record itself is wrong, versus a local resolver caching
  an old value? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `dig` is the standard, detailed DNS query tool; `nslookup` is a simpler legacy alternative.
- Querying a specific DNS server directly (`dig ... @server`) isolates the record itself from
  local resolver configuration or caching.
- The `SERVER` line in `dig` output confirms which resolver actually answered.
- This directly extends the "test by IP first" isolation technique from this module's opening
  topic.

## Related topics

- [Networking Fundamentals](networking-fundamentals.md)
- [/etc/hosts, /etc/resolv.conf](etc-hosts-resolv-conf.md)
