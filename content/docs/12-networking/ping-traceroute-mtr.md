---
title: "ping, traceroute, mtr"
description: "Testing reachability with ping, mapping the hop-by-hop path with traceroute, and combining both continuously with mtr."
module: "12-networking"
moduleTitle: "Networking"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["12-networking/ip-addr-ip-route-ifconfig"]
relatedTopics: ["networking-fundamentals"]
relatedCommands: ["ping", "traceroute"]
careerRelevance: ["devops", "cloud", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ping-vs-traceroute"]
relatedCheatsheet: "networking"
furtherReading: []
nextTopic: "12-networking/netstat-ss"
prevTopic: "12-networking/ip-addr-ip-route-ifconfig"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["ping command linux", "traceroute explained", "mtr command", "network reachability test"]
canonicalUrl: "/docs/networking/ping-traceroute-mtr"
---

# ping, traceroute, mtr

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Linux Administrator

> **TL;DR:** `ping` answers "is it reachable, and how fast?" `traceroute` answers "which hops does
> traffic pass through to get there, and where does it stop?" `mtr` combines both, continuously,
> in one live view — the tool to reach for once `ping` and `traceroute` alone aren't enough.

## What is it?

Three complementary tools for testing network reachability at different levels of detail: `ping`
(basic reachability and latency), `traceroute` (the hop-by-hop path to a destination), and `mtr`
(a live, continuously-updating combination of both).

## Why does it exist?

"Can I reach X?" and "where exactly does it stop working if I can't?" are two different
questions. `ping` answers the first quickly. When `ping` fails or is slow, `traceroute` shows
*where* along the path the problem is, instead of just confirming that a problem exists. `mtr`
exists because a single `traceroute` snapshot can miss an intermittent issue that only shows up
over many samples.

## Where is it used?

The first two tools reached for in almost any "can't reach a host" investigation:
confirming a server is up and responsive after a deploy or reboot, isolating whether a
connectivity problem is local, at an intermediate network hop, or at the destination itself, and
diagnosing intermittent packet loss that a single `ping` or `traceroute` might not catch.

## How it works

> 📊 Diagram: a request traveling through three routers to reach a destination server, with
> `ping` shown testing only the final destination's response, `traceroute` shown revealing each
> intermediate router along the way with its own response time, and `mtr` shown repeating that
> same hop-by-hop test continuously and aggregating loss/latency per hop.

| Tool | Answers | Output style |
|---|---|---|
| `ping` | Is the destination reachable? How fast? | A few round-trip time samples |
| `traceroute` | What path does traffic take, and where does it stop? | One line per hop, one-time snapshot |
| `mtr` | Same as `traceroute`, but continuously | Live, updating table, one row per hop |

`traceroute` works by sending packets with an increasing "time to live" (TTL) value, one more hop
each time, so each router along the path is forced to respond exactly once as the packet expires
at it — that's how it maps the path hop by hop.

## Real-world example

A deployed application intermittently times out talking to an external API. A single `ping` shows
normal latency and zero loss — the problem isn't constant, so a single test doesn't catch it.
Running `mtr` continuously over several minutes reveals one specific intermediate hop with 15%
packet loss that doesn't show up in every single `ping`, isolating the issue to a specific point
in the network path rather than the destination server itself.

## Commands

- [`ping`](../../commands/ping.md) — full syntax and examples
- [`traceroute`](../../commands/traceroute.md) — full syntax and examples

`mtr` is often not installed by default (`apt install mtr` / `dnf install mtr`) and combines the
above two tools' behavior into one continuously-updating view; it doesn't get a separate
canonical command page here since its flags largely mirror the two above.

## Production example

```
$ ping -c 4 example.com
64 bytes from example.com (93.184.216.34): icmp_seq=1 ttl=56 time=11.2 ms
64 bytes from example.com (93.184.216.34): icmp_seq=2 ttl=56 time=10.8 ms
--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss

$ traceroute example.com
 1  192.168.1.1 (192.168.1.1)  1.123 ms
 2  10.10.0.1 (10.10.0.1)  8.456 ms
 3  * * *
 4  93.184.216.34 (93.184.216.34)  11.203 ms
```

Hop 3 showing `* * *` means that specific router didn't respond to the probe — not necessarily a
problem, since some routers are configured not to respond at all while still forwarding traffic
correctly.

## Do / Don't

| Do | Don't |
|---|---|
| Reach for `mtr` when a single `ping`/`traceroute` snapshot doesn't show an intermittent issue | Conclude "no problem" from one clean `ping` when the reported issue is intermittent |
| Treat `* * *` hops in `traceroute` as inconclusive, not proof of a broken hop | Assume a `* * *` hop is definitely where the problem is |
| Use `ping`'s packet loss percentage as a first, quick reachability signal | Use `traceroute` as your first check when you just need a yes/no reachability answer |

## Common mistakes

- Concluding "everything's fine" from a single clean `ping` when the actual reported problem is
  intermittent — one sample can easily miss it.
- Treating a `* * *` line in `traceroute` output as definitive proof that hop is broken, when it
  often just means that router doesn't respond to probes by policy.
- Reaching for `traceroute` first when a simple reachability check (`ping`) would answer the
  question faster.

## Best practices

- Start with `ping` for a quick yes/no reachability check before reaching for `traceroute`.
- Use `traceroute` specifically to localize *where* a path breaks down, not to diagnose whether
  reachability exists at all.
- Use `mtr` for intermittent or hard-to-reproduce connectivity issues, since it samples
  continuously instead of once.

## Exercises

1. Run `ping -c 4` against a hostname you know is reachable and read the packet loss percentage.
2. Run `traceroute` against the same hostname and identify how many hops it takes.
3. Explain in one sentence why `mtr` can catch problems a single `traceroute` run might miss.

## Quiz

**Q: What does a `* * *` line in `traceroute` output mean?**
<details><summary>Show answer</summary>
That specific hop didn't respond to the probe — often because that router is configured not to
respond, not necessarily because it's broken.
</details>

**Q: Why would you reach for `mtr` instead of a single `ping` or `traceroute`?**
<details><summary>Show answer</summary>
`mtr` samples continuously over time, which can reveal intermittent packet loss or latency at a
specific hop that a single one-time `ping` or `traceroute` snapshot would miss.
</details>

## Interview questions

- What's the practical difference between `ping` and `traceroute`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `ping` tests basic reachability and latency; `traceroute` maps the hop-by-hop path.
- `traceroute` uses increasing TTL values to force each router along the path to respond once.
- A `* * *` hop in `traceroute` output is inconclusive, not proof of failure.
- `mtr` combines both tools continuously, useful for intermittent connectivity issues.

## Related topics

- [Networking Fundamentals](networking-fundamentals.md)
- [netstat, ss](netstat-ss.md)
