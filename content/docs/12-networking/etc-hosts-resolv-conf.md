---
title: "/etc/hosts, /etc/resolv.conf"
description: "The two local configuration files that control name resolution before any DNS server is even queried — static hostname mappings and which resolvers to ask."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/curl-wget"]
relatedTopics: ["networking-fundamentals", "dns-troubleshooting-dig-nslookup"]
relatedCommands: ["cat"]
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#etc-hosts-precedence"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/firewalls-iptables-firewalld-ufw"
prevTopic: "12-networking/curl-wget"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["etc hosts file explained", "etc resolv.conf explained", "static hostname mapping linux", "dns resolver config"]
canonicalUrl: "/docs/networking/etc-hosts-resolv-conf"
---

# /etc/hosts, /etc/resolv.conf

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** `/etc/hosts` is a static, local list of hostname-to-IP mappings, checked *before* any
> DNS server is queried. `/etc/resolv.conf` lists which DNS resolvers to actually query when a
> name isn't in `/etc/hosts`. Both are plain text files, both are common places to look when name
> resolution behaves unexpectedly.

## What is it?

Two plain-text configuration files that control name resolution on a Linux machine, independent
of the DNS concept introduced in [Networking Fundamentals](networking-fundamentals.md):
`/etc/hosts` (static, local overrides) and `/etc/resolv.conf` (which DNS servers to query).

## Why does it exist?

Not every name lookup should have to go out to a DNS server — a machine needs a fast, local,
always-available way to resolve at least itself (`localhost`) and any manually-defined names
without depending on network reachability or an external resolver at all. `/etc/hosts` is that
mechanism. Separately, a machine needs to know *which* DNS servers to actually ask when a name
does need external resolution — that's `/etc/resolv.conf`'s job.

## Where is it used?

Local development (mapping a custom hostname to `127.0.0.1` or a specific test server),
temporarily overriding a hostname to point at a different server without touching real DNS
records, and diagnosing name resolution issues by checking which resolvers a machine is actually
configured to query.

## How it works

> 📊 Diagram: a name lookup flow — application requests resolution of a hostname, the resolver
> checks `/etc/hosts` first (a match returns immediately, no network involved), and only on a
> miss does it query the DNS servers listed in `/etc/resolv.conf`.

**`/etc/hosts`** — one mapping per line, plain text:

```
127.0.0.1   localhost
192.168.1.10  db-server.internal
```

Checked *before* DNS is queried at all — a match here short-circuits the lookup entirely, which is
exactly why it's a common local-override tool and also a common source of confusing "why is this
resolving to the wrong place" bugs.

**`/etc/resolv.conf`** — lists the DNS servers to query when `/etc/hosts` doesn't have a match:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

On many modern distros this file is managed automatically (by `systemd-resolved` or DHCP) and
shouldn't be hand-edited directly — manual edits can be silently overwritten.

## Real-world example

A developer adds `192.168.1.10 db-server.internal` to `/etc/hosts` to test against a specific
database server. Weeks later, that same server is decommissioned and its IP is reassigned to a
different machine — but the stale `/etc/hosts` entry silently keeps pointing the old hostname at
the new (wrong) server, because `/etc/hosts` entries never expire and are checked before any real
DNS record could correct the mistake.

## Commands

No new canonical command — both files are read with `cat`, already covered in
[Module 06](../06-files/viewing-files.md); the content and precedence rules are what's new here.

## Production example

```
$ cat /etc/hosts
127.0.0.1   localhost
192.168.1.10  db-server.internal

$ cat /etc/resolv.conf
nameserver 8.8.8.8
```

If `db-server.internal` resolves unexpectedly, checking `/etc/hosts` first (before assuming a DNS
record is wrong) is the correct order of investigation, since `/etc/hosts` wins if both exist.

## Do / Don't

| Do | Don't |
|---|---|
| Check `/etc/hosts` first when a hostname resolves somewhere unexpected | Assume a wrong resolution is always a DNS server problem |
| Remove stale `/etc/hosts` entries once their purpose is done | Leave a temporary override in place indefinitely |
| Check whether `/etc/resolv.conf` is managed automatically before hand-editing it | Hand-edit `/etc/resolv.conf` on a system where `systemd-resolved`/DHCP will silently overwrite it |

## Common mistakes

- Leaving a temporary `/etc/hosts` override in place long after its purpose is done, causing
  confusing resolution behavior much later — exactly the scenario in the real-world example above.
- Assuming unexpected name resolution is a DNS server issue without first checking `/etc/hosts`,
  which takes precedence and is checked first.
- Manually editing `/etc/resolv.conf` on a system where it's automatically regenerated, then being
  confused when changes don't persist.

## Best practices

- Treat `/etc/hosts` entries as temporary by default — remove them once their diagnostic or
  testing purpose is served.
- Check `/etc/hosts` before escalating to a DNS investigation whenever a hostname resolves
  somewhere unexpected.
- Confirm how `/etc/resolv.conf` is managed on a given system (manual vs. `systemd-resolved`/DHCP)
  before attempting to edit it directly.

## Exercises

1. Read `/etc/hosts` on a system you have access to and identify the `localhost` entry.
2. Read `/etc/resolv.conf` and identify which nameserver(s) are configured.
3. Explain in one sentence why `/etc/hosts` entries can cause confusing bugs months after they're
   added.

## Quiz

**Q: If a hostname has both an `/etc/hosts` entry and a real DNS record, which one wins?**
<details><summary>Show answer</summary>
`/etc/hosts` — it's checked first and short-circuits the lookup before DNS is ever queried.
</details>

**Q: Why might hand-editing `/etc/resolv.conf` not work as expected on some systems?**
<details><summary>Show answer</summary>
On many modern distros it's managed automatically by `systemd-resolved` or DHCP, which can
silently overwrite manual edits.
</details>

## Interview questions

- Why would a hostname resolve to the wrong IP even though its DNS record is correct? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/etc/hosts` provides static, local hostname mappings, checked before any DNS query.
- `/etc/resolv.conf` lists which DNS servers to query when `/etc/hosts` doesn't resolve a name.
- `/etc/hosts` entries never expire and can cause confusing, stale resolution bugs.
- `/etc/resolv.conf` is often auto-managed — check before hand-editing it.

## Related topics

- [Networking Fundamentals](networking-fundamentals.md)
- [DNS Troubleshooting: dig, nslookup](dns-troubleshooting-dig-nslookup.md)
