---
title: "Swap Space"
description: "Disk space used as overflow when physical RAM fills up — what it actually protects against, and why heavy swap usage is a performance warning sign, not a fix."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/disk-usage-df-du"]
relatedTopics: ["disk-usage-df-du"]
relatedCommands: ["free"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#swap-explained"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/linux-logging-overview"
prevTopic: "15-storage/disk-usage-df-du"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["swap space explained", "swappiness explained", "free -h swap", "heavy swap usage performance"]
canonicalUrl: "/docs/storage/swap-space"
---

# Swap Space

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** Swap is disk space the kernel uses as overflow when physical RAM fills up. It
> prevents an out-of-memory crash, but disk is orders of magnitude slower than RAM — heavy,
> sustained swap usage is a performance problem to investigate, not a capacity solution to rely
> on.

## What is it?

A dedicated area of disk (a partition or a swap file) that the kernel can use as overflow storage
for memory pages when physical RAM is full, letting the system continue running instead of
immediately failing when memory runs out.

## Why does it exist?

Physical RAM is finite, and running out of it entirely, with nowhere to put the overflow, means
the kernel has to start forcibly killing processes to free memory (Module 10's OOM killer
territory). Swap exists to give the system somewhere to temporarily put less-actively-used memory
pages instead, buying breathing room — though at a real performance cost, since disk access is
orders of magnitude slower than RAM.

## Where is it used?

Every general-purpose Linux server typically has some swap configured as a safety margin against
memory spikes, though the *right* amount and the philosophy around it varies — some
high-performance or memory-sensitive workloads deliberately minimize reliance on swap rather than
using it as a buffer.

## How it works

> 📊 Diagram: physical RAM shown filling up with active process memory, with the kernel moving
> the least-recently-used memory pages out to a swap partition on disk to make room — contrasted
> with an out-of-memory crash/OOM-kill scenario if no swap exists at all.

| Concept | Meaning |
|---|---|
| Swap partition/file | Dedicated disk space configured for swap use |
| Swapping | The kernel moving memory pages to/from swap as RAM fills or frees up |
| Swappiness | A tunable (0–100) controlling how aggressively the kernel prefers to swap versus reclaim memory another way |

**The performance warning**: a small amount of swap usage is normal and not concerning. Heavy,
*sustained* swapping (visible as high, persistent disk I/O and sluggish response times) means the
system is genuinely memory-constrained and actively paying a severe performance penalty moving
data to and from disk — the correct response is investigating memory usage or adding RAM, not
treating swap as free extra capacity.

## Real-world example

An application server becomes extremely slow under load, though it isn't crashing. `free -h`
shows swap usage climbing steadily, and disk I/O is unusually high — the system is thrashing,
constantly swapping memory pages in and out because physical RAM is genuinely insufficient for
the current workload. Adding RAM (or reducing the workload's memory footprint) resolves the actual
problem; the swap space itself was doing exactly its job of preventing a hard crash, but was never
meant to be a substitute for adequate RAM under sustained load.

## Commands

See [`free`](../../commands/free.md) for inspecting swap usage. `mkswap`/`swapon`/`swapoff` (the
commands that create and activate swap) don't get separate canonical pages — this module scopes
swap as a concept rather than a set of individually documented commands (see this module's
intentional gaps).

## Production example

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           7.8Gi       6.9Gi       200Mi        50Mi       700Mi       650Mi
Swap:          2.0Gi       1.8Gi       200Mi
```

Swap at 1.8Gi used out of 2.0Gi, alongside very low free memory, is exactly the "genuinely
memory-constrained" pattern worth investigating rather than ignoring.

## Do / Don't

| Do | Don't |
|---|---|
| Treat heavy, sustained swap usage as a performance problem to investigate | Treat swap as free extra RAM with no performance cost |
| Check `free -h` as part of investigating a slow, non-crashing server | Assume a non-crashing server has no memory problem |
| Size swap deliberately based on the workload, not by copying a default blindly | Assume the same swap size is correct for every workload |

## Common mistakes

- Treating swap as a substitute for adequate RAM rather than an emergency overflow buffer,
  leading to a system that "works" but performs terribly under load.
- Not checking swap usage (`free -h`) when investigating a slow, non-crashing server, missing an
  obvious memory-pressure signal.
- Assuming any swap usage at all indicates a problem — a small, stable amount is normal; it's
  heavy, sustained usage that's the actual warning sign.

## Best practices

- Investigate sustained, heavy swap usage as a genuine memory-pressure problem, not something to
  work around by simply adding more swap.
- Check `free -h` early when a server is slow but not crashing.
- Size and configure swap deliberately for the actual workload rather than assuming a default is
  always appropriate.

## Exercises

1. Run `free -h` on a system you have access to and check current swap usage.
2. Explain in one sentence why heavy, sustained swap usage is a performance problem rather than a
   free capacity win.
3. Describe what swap actually protects against, and what it doesn't solve.

## Quiz

**Q: What does swap actually protect against?**
<details><summary>Show answer</summary>
It gives the kernel overflow space when physical RAM fills up, preventing an immediate
out-of-memory crash — though at a real performance cost, since disk is far slower than RAM.
</details>

**Q: Is heavy, sustained swap usage something to fix by adding more swap?**
<details><summary>Show answer</summary>
No — it's a sign of genuine memory pressure that should be addressed by investigating memory
usage or adding RAM, not by simply providing more swap space.
</details>

## Interview questions

- What's the difference between a small amount of swap usage and heavy, sustained swap usage, in
  terms of what each indicates? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Swap is disk space used as overflow when physical RAM fills up, preventing an immediate
  out-of-memory crash.
- Disk is far slower than RAM — heavy, sustained swap usage is a real performance penalty.
- A small, stable amount of swap usage is normal; sustained heavy usage signals genuine memory
  pressure.
- `free -h` is the standard first check when investigating a slow but non-crashing server.

## Related topics

- [Disk Usage: df, du](disk-usage-df-du.md)
- [Module 16: Logs](../16-logs/index.md)
