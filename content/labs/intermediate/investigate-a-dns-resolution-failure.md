---
title: "Lab: Investigate a DNS Resolution Failure"
description: "An intermediate Linux lab, framed as a real ticket: an application intermittently can't resolve a hostname, and you have to isolate which resolver is at fault."
type: "lab"
tier: "intermediate"
careerRelevance: ["linux-administrator", "devops", "sre"]
updatedAt: "2026-07-30"
keywords: ["dns troubleshooting lab linux", "dig nslookup exercise", "resolv.conf lab", "intermediate linux lab networking"]
canonicalUrl: "/labs/intermediate/investigate-a-dns-resolution-failure"
---

# Lab: Investigate a DNS Resolution Failure

🟡 Intermediate · Relevant for: Linux Administrator, DevOps, SRE

## Scenario

**Ticket #LX-204:** "Our app server intermittently fails to reach `api.internal-service.example`
— sometimes it works fine, sometimes it times out resolving the hostname. It's not constant, so
it's been hard to pin down. Find out what's actually happening."

## Environment Setup

```bash
cat /etc/resolv.conf
# nameserver 8.8.8.8
# nameserver 203.0.113.99   <- a deliberately unreachable resolver, simulating the flaky one
```

(If your `/etc/resolv.conf` is managed automatically, note the resolvers it lists and reason
about the same investigation without necessarily editing the file.)

## Tasks

1. Confirm which DNS resolvers the system is configured to use.
2. Test each configured resolver individually, not just resolution as a single pass/fail check.
3. Identify which specific resolver is the flaky one.
4. Propose (and, if you edited `resolv.conf` for this lab, apply) a fix — reordering or removing
   the bad resolver.

## Hints

<details><summary>Show hint</summary>
`dig @{resolver} {hostname}` lets you query one specific resolver directly instead of whatever
the system would pick — that's how you isolate which one is actually failing instead of testing
resolution as a whole.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm configured resolvers
cat /etc/resolv.conf

# Test each individually
dig @8.8.8.8 example.com
# Fast response, ANSWER SECTION present — this one is healthy

dig @203.0.113.99 example.com
# ;; connection timed out; no servers could be reached — this one is the problem

# Fix: remove or deprioritize the bad resolver
sudo sed -i '/203.0.113.99/d' /etc/resolv.conf
cat /etc/resolv.conf

# Confirm resolution is now consistently fast
for i in 1 2 3 4 5; do dig +short example.com; done
```

The reason the original symptom was *intermittent* rather than constant: the system was likely
alternating between resolvers, or falling back to the second one only after the first timed out —
so some requests succeeded quickly via the healthy resolver and others hung waiting on the dead
one before eventually failing or falling through.
</details>

## What You Learned

- Why intermittent (not total) DNS failure points at one flaky resolver among several configured,
  not DNS being broken outright.
- How to query a specific resolver directly with `dig @{resolver}` to isolate the faulty one.
- That `/etc/resolv.conf` order matters — a bad resolver listed first can slow down or break
  resolution even when a healthy one is also configured.

## Related Modules

- [Networking](../../docs/12-networking/index.md)

## Related topics

- [Networking Cheat Sheet](../../cheatsheets/networking.md)
- [Scenario-Based Interview Questions: intermittent DNS resolution failures](../../interview-questions/scenario-based.md#intermittent-dns-resolution-failures)
- [Labs Hub](../index.md)
