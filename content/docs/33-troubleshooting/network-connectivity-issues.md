---
title: "Network Connectivity Issues"
description: "A layered order for diagnosing 'can't reach the server' problems — local interface, routing, DNS, firewall, and the application itself — instead of guessing which layer is at fault."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/ping-traceroute-mtr", "12-networking/dns-troubleshooting-dig-nslookup"]
relatedTopics: ["33-troubleshooting/ssh-connection-issues", "33-troubleshooting/service-wont-start"]
relatedCommands: ["ping", "dig", "ss"]
careerRelevance: ["linux-administrator", "devops", "sre"]
relatedLabs: ["intermediate/investigate-a-dns-resolution-failure"]
relatedInterviewQuestions: ["scenario-based#intermittent-dns-resolution-failures", "scenario-based#new-firewall-rule-blocks-legitimate-traffic"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "33-troubleshooting/ssh-connection-issues"
prevTopic: "33-troubleshooting/permission-denied-issues"
estimatedReadingTime: 8
updatedAt: "2026-07-30"
keywords: ["network connectivity troubleshooting linux", "cant reach server", "diagnose network issue linux", "dns firewall routing troubleshooting"]
canonicalUrl: "/docs/troubleshooting/network-connectivity-issues"
---

# Network Connectivity Issues

🟡 Good to Know · Relevant for: Linux Administrator · DevOps · SRE

> **TL;DR:** "Can't reach the server" has several distinct possible layers — local interface,
> routing, DNS, firewall, application — that all produce a similar-looking failure. Diagnose
> layer by layer, from closest to the machine outward, rather than guessing.

## What is it?

A layered diagnostic order for "can't connect" problems: confirm the local network interface is
up, then routing, then DNS resolution, then whether a firewall is blocking the specific port, then
finally whether the application itself is even listening — each layer ruled in or out before
moving to the next.

## Why does it exist?

A failed connection looks identical from the client's perspective whether the cause is a DNS
failure, a firewall rule, a routing problem, or the application simply not running — the error
message rarely tells you which. Diagnosing in a fixed, layered order (closest to the local machine
outward) avoids wasted effort chasing the wrong layer.

## Where is it used?

Any "can't reach the server," intermittent connection failures, or a service that's confirmed
running but still unreachable from elsewhere.

## How it works

> 📊 Diagram: a layered flowchart from bottom to top — "Local interface up? (`ip addr`)" →
> "Routing correct? (`ip route`, `traceroute`)" → "DNS resolving? (`dig`)" → "Firewall allowing the
> port? (`iptables`/`ufw`/`firewall-cmd`)" → "Application actually listening? (`ss -tlnp`)" — each
> layer a gate the request has to pass through.

1. **Confirm the local interface is up** with `ip addr` — a surprisingly common root cause on
   freshly provisioned or recently-reconfigured machines.
2. **Confirm basic reachability and routing** with `ping` and `traceroute`/`mtr` — isolates whether
   the problem is even network-layer reachability versus something higher up the stack.
3. **Confirm DNS resolves correctly**, and specifically which resolver is answering, with `dig`
   (see [DNS Troubleshooting](../12-networking/dns-troubleshooting-dig-nslookup.md) for the full
   per-resolver isolation technique) — a resolution failure looks identical to an unreachable host.
4. **Confirm the firewall allows the specific port** — a new or changed rule (`iptables`, `ufw`,
   `firewall-cmd`) can silently block legitimate traffic, especially rule-ordering mistakes.
5. **Confirm the application is actually listening** on the expected port with `ss -tlnp` — after
   ruling out every network layer, the simplest remaining explanation is that nothing is there to
   answer.

## Real-world example

Users report intermittent failures reaching an internal API. Working through the layers: the
interface and routing are fine, `dig` against one specific configured resolver times out while a
second resolver answers instantly — the actual cause is a single flaky DNS resolver in the
configured list, not the API server or the network path at all. A firewall or application-layer
investigation would have found nothing, because the fault was one layer earlier than expected.

## Commands

See [`ping`](../../commands/ping.md), [`dig`](../../commands/dig.md), and
[`ss`](../../commands/ss.md) for full flag references, and
[Firewalls](../12-networking/firewalls-iptables-firewalld-ufw.md) for firewall rule syntax.

## Production example

```
$ ip addr show eth0 | grep "inet "
    inet 10.0.1.15/24 brd 10.0.1.255 scope global eth0

$ ping -c 3 10.0.1.1
3 packets transmitted, 3 received, 0% packet loss

$ dig @8.8.8.8 api.internal-service.example +short
10.0.1.50
$ dig @203.0.113.99 api.internal-service.example +short
;; connection timed out; no servers could be reached

$ ss -tlnp | grep :443
LISTEN  0  128  10.0.1.50:443  0.0.0.0:*  users:(("nginx",pid=1234,fd=6))
```

Interface, routing, and one resolver check out fine — the second configured resolver
(`203.0.113.99`) is the actual fault, isolated by testing resolvers individually rather than
testing DNS as a single pass/fail check.

## Do / Don't

| Do | Don't |
|---|---|
| Diagnose layer by layer, closest to the machine outward | Jump straight to "the firewall must be blocking it" without checking |
| Test each configured DNS resolver individually | Test DNS as a single pass/fail check |
| Check `ss -tlnp` to confirm the app is actually listening | Assume the app is running because a deploy "succeeded" |
| Check firewall rule order, not just rule presence | Assume a rule works correctly just because it exists |

## Common mistakes

- Assuming a connectivity failure is a firewall problem before checking simpler, closer layers
  (interface, routing, DNS) first.
- Testing DNS resolution as a whole instead of testing each configured resolver individually,
  missing an intermittent single-resolver failure.
- Not checking whether the application is actually listening on the expected port — a deploy
  that "succeeded" doesn't guarantee the process is bound and listening.
- Adding a firewall rule without checking existing rule order, letting a broader earlier rule
  shadow the intended effect.

## Best practices

- Work outward in a fixed order: interface → routing → DNS → firewall → application — don't skip
  layers based on a guess.
- Test DNS resolvers individually with `dig @{resolver}` when failures are intermittent rather
  than constant.
- Confirm firewall rule *order*, not just presence, with `--line-numbers` or the equivalent for
  your firewall front-end.
- Confirm the application's actual listening state with `ss -tlnp` as the last, most specific check.

## Exercises

1. Run `dig @8.8.8.8 {any-domain}` and `dig @1.1.1.1 {any-domain}` and compare response times —
   practice reading the output for resolver health.
2. Explain in one sentence why testing DNS resolvers individually catches intermittent failures
   that testing resolution as a whole would miss.
3. Write the layered order (interface → ... → application) from memory.

## Quiz

**Q: Why might an application intermittently fail to resolve a hostname, but not constantly?**
<details><summary>Show answer</summary>
One of several configured DNS resolvers is likely flaky — some requests succeed via a healthy
resolver while others hit the failing one, producing intermittent rather than total failure.
</details>

**Q: What's the last layer to check in this module's network troubleshooting order, and why last?**
<details><summary>Show answer</summary>
Whether the application itself is actually listening on the expected port (`ss -tlnp`) — it's
checked last because every layer before it (interface, routing, DNS, firewall) needs to be ruled
out first, since a listening application still can't be reached if an earlier layer is broken.
</details>

## Interview questions

- An application intermittently fails to resolve a hostname it usually resolves fine. How do you
  approach this? →
  [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#intermittent-dns-resolution-failures)
- You added a new firewall rule to block a suspicious IP range, and now legitimate customer
  traffic is being dropped too — how do you find and fix this safely? →
  [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#new-firewall-rule-blocks-legitimate-traffic)

## Key Takeaways

- Diagnose network connectivity layer by layer: interface, routing, DNS, firewall, application.
- Test DNS resolvers individually to catch intermittent, single-resolver failures.
- Firewall rule order matters as much as rule presence.
- The application actually listening (`ss -tlnp`) is the last, most specific thing to confirm.

## Related topics

- [SSH Connection Issues](ssh-connection-issues.md)
- [Service Won't Start](service-wont-start.md)
- [Networking](../12-networking/index.md)
