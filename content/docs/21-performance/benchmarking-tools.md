---
title: "Benchmarking Tools"
description: "Producing a reproducible before/after number to validate a tuning change actually helped — the closing discipline of this module's measure-first approach."
module: "21-performance"
moduleTitle: "Performance"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["21-performance/kernel-tuning-sysctl"]
relatedTopics: ["kernel-tuning-sysctl", "performance-tuning-overview"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#validate-performance-change"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "22-containers/what-is-containerization"
prevTopic: "21-performance/kernel-tuning-sysctl"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["linux benchmarking tools", "sysbench fio examples", "validate performance tuning change", "before after benchmark"]
canonicalUrl: "/docs/performance/benchmarking-tools"
---

# Benchmarking Tools

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator · Backend

> **TL;DR:** A tuning change ([Kernel Tuning](kernel-tuning-sysctl.md)) isn't confirmed to have
> helped until it's measured with a reproducible before/after benchmark. Tools like `fio` (disk),
> `sysbench` (CPU/database), and `iperf3` (already covered, network) each generate controlled,
> repeatable load for exactly this purpose.

## What is it?

Generating controlled, repeatable synthetic load to produce a comparable before/after number —
the closing step that validates whether a change from
[Kernel Tuning](kernel-tuning-sysctl.md) (or any other tuning action) actually improved anything,
rather than assuming it did.

## Why does it exist?

This module opened with "measure before you tune" — benchmarking is the equally important
"measure after you tune" half of that discipline. Without a reproducible before/after comparison,
there's no way to actually confirm a change helped, made no difference, or made things worse.

## Where is it used?

Validating any tuning change before rolling it out broadly, comparing hardware/configuration
options during a capacity-planning decision, and establishing a baseline number worth comparing
future measurements against.

## How it works

> 📊 Diagram: a before/after bar chart — "baseline" and "after change" bars for the same benchmark
> metric (e.g. IOPS, or requests/sec), with a labeled arrow showing the specific tuning action
> taken between the two measurements — reinforcing that the comparison, not either number alone,
> is what confirms the change actually helped.

**Disk — `fio` (Flexible I/O Tester):**

```bash
fio --name=randwrite --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=60
```

Generates a controlled, reproducible I/O workload (random 4KB writes, in this example) and reports
IOPS and throughput — the same shape of question [Disk I/O Performance](disk-io-performance-iostat-iotop.md)
investigated with live tools, but now as a controlled, repeatable test rather than passive
observation.

**CPU/database — `sysbench`:**

```bash
sysbench cpu run
sysbench oltp_read_write --db-driver=mysql run
```

Generates synthetic CPU load or a simulated database workload, producing a comparable throughput
number for before/after comparison around a tuning change.

**Network — `iperf3`** (already covered in
[Network Performance](network-performance-iperf-sar.md)): the same active-throughput-measurement
tool doubles as the network benchmarking tool for this closing discipline.

**The comparison, not the number alone, is what matters:** a single `fio` run producing "50,000
IOPS" is meaningless without a baseline to compare it against — always run the identical benchmark
before and after a change, under conditions as close to identical as possible.

## Real-world example

After lowering `vm.swappiness` (the previous page's example) to reduce unwanted swapping, the team
wants to confirm the change actually improved database performance rather than just assuming it
did based on theory. Running the same `sysbench oltp_read_write` benchmark before and after the
change, under equivalent load, produces a concrete, comparable number — confirming a genuine
throughput improvement (or, just as usefully, revealing no meaningful difference, saving the
change from being kept based on an untested assumption).

## Syntax

```
fio --name=NAME --rw=MODE [OPTIONS]
sysbench TEST run
```

## Commands

No canonical command pages exist for `fio` or `sysbench` — specialized, situational benchmarking
tools referenced illustratively rather than given full command-page treatment (see this module's
[intentional gaps](index.md)); [`iperf`](../../commands/iperf.md) (already covered) serves the
same benchmarking role for network throughput.

## Production example

```
$ fio --name=baseline --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=30 --group_reporting
  write: IOPS=12.4k, BW=48.4MiB/s

# ... apply a tuning change ...

$ fio --name=after_tuning --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=30 --group_reporting
  write: IOPS=18.9k, BW=73.8MiB/s
```

The identical benchmark run before and after, producing directly comparable IOPS numbers that
confirm the tuning change delivered a real, measured improvement.

## Do / Don't

| Do | Don't |
|---|---|
| Run the identical benchmark before and after a change | Compare numbers from benchmarks run under different conditions |
| Treat a single benchmark number as meaningless without a baseline | Cite one benchmark run as proof of improvement with nothing to compare it against |
| Use a benchmark tool matched to the resource being tuned | Use a CPU benchmark to validate a disk I/O tuning change |

## Common mistakes

- Running a benchmark only after a change, with no baseline captured beforehand to compare it
  against.
- Comparing benchmark runs performed under different conditions (different load, different time of
  day, different concurrent activity), making the comparison meaningless.
- Assuming a tuning change helped based on theory alone, without ever actually benchmarking before
  and after.

## Best practices

- Always capture a baseline benchmark before making a tuning change, not just after.
- Keep conditions as consistent as possible between before/after runs — same tool, same
  parameters, same time of day if system load varies by time.
- Match the benchmark tool to the resource actually being tuned — `fio` for disk changes,
  `sysbench` for CPU/database changes, `iperf3` for network changes.

## Exercises

1. Explain why a single benchmark number, with no baseline, doesn't confirm anything about a
   tuning change.
2. Design a before/after benchmarking plan for validating a hypothetical disk-related tuning
   change.
3. Describe why conditions should be kept as consistent as possible between a baseline and a
   follow-up benchmark run.

## Quiz

**Q: Why is a single benchmark number, without a baseline, not useful for validating a tuning change?**
<details><summary>Show answer</summary>
There's nothing to compare it against — only a before/after comparison under consistent conditions
can confirm whether a change actually helped, made no difference, or made things worse.
</details>

**Q: What tool would you use to benchmark disk I/O specifically?**
<details><summary>Show answer</summary>
`fio` (Flexible I/O Tester) — it generates controlled, reproducible I/O workloads and reports IOPS
and throughput.
</details>

**Q: Why should benchmark conditions be kept consistent between a baseline and a follow-up run?**
<details><summary>Show answer</summary>
Differing conditions (different load, different time of day) make the comparison meaningless —
any observed difference could be due to the changed conditions rather than the tuning change being
evaluated.
</details>

## Interview questions

- How would you prove that a performance tuning change actually improved things, rather than just
  assuming it did? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Benchmarking produces the reproducible before/after comparison that confirms a tuning change
  actually helped.
- `fio` (disk), `sysbench` (CPU/database), and `iperf3` (network, already covered) each serve this
  role for their respective resource.
- A single benchmark number without a baseline proves nothing — the comparison is what matters.
- This page closes the module's measure-first discipline: measure before tuning, tune
  deliberately, then measure again to confirm.

## Related topics

- [Kernel Tuning: sysctl](kernel-tuning-sysctl.md)
- [Module 22: Containers](../22-containers/index.md)
