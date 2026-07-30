---
title: "ps, top, htop"
description: "A snapshot versus a live view versus a friendlier live view — the three standard ways to see what's actually running."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/pid-ppid-and-process-tree"]
relatedTopics: ["kill-killall-pkill-and-signals"]
relatedCommands: ["ps", "top"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#ps-aux-vs-ps-ef"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "10-processes/kill-killall-pkill-and-signals"
prevTopic: "10-processes/pid-ppid-and-process-tree"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["ps aux vs ps -ef", "top command linux", "htop vs top", "ps command examples"]
canonicalUrl: "/docs/processes/ps-top-htop"
---

# ps, top, htop

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `ps aux` gives a one-time snapshot of every process — good for scripting and
> piping. `top` gives a live, auto-refreshing view — good for watching CPU/memory in real time.
> `htop` is a friendlier, colorized, interactive version of `top` — not installed by default
> everywhere, but usually worth installing.

## What is it?

Three tools for viewing running processes, each suited to a different situation: `ps` (a
point-in-time snapshot), `top` (a live, continuously updating view), and `htop` (a more
user-friendly, interactive alternative to `top`, distributed separately).

## Why does it exist?

Sometimes you need a static list you can filter, sort, or pipe into `grep` (`ps`); sometimes you
need to watch resource usage change in real time to catch a spike (`top`); and `htop` exists
because `top`'s default interface, while functional, is genuinely harder to read and navigate
than it needs to be.

## Where is it used?

- `ps aux | grep processname` — one of the most common command combinations in daily Linux work,
  finding a specific process by name
- `top` — watching CPU/memory usage live during a performance investigation
- `htop` — the same live-monitoring use case as `top`, with a friendlier interface, when
  available

## How it works

> 📊 Diagram: three panels — `ps` shown as a single static printout, `top` shown as the same
> printout continuously refreshing with a clock icon, `htop` shown as the same live data but with
> color-coded bars for CPU/memory usage — visually reinforcing "snapshot vs. live vs. live +
> friendlier."

**`ps`** — two syntax styles exist and both work, but produce different-looking output:

```
ps aux      # BSD-style: all processes, user-oriented columns
ps -ef      # UNIX-style: all processes, includes PPID clearly
```

`ps aux` is more common in casual use; `ps -ef` is preferred when you specifically need the PPID
column front and center for tracing process lineage (Module 10's previous topic).

**`top`** — live view, refreshing every few seconds by default. Key interactive commands:
`q` to quit, `k` to kill a process by PID, `M` to sort by memory usage, `P` to sort by CPU usage.

**`htop`** — same live-monitoring purpose as `top`, with mouse support, color, and easier process
tree viewing, but requires separate installation (`apt install htop` / `dnf install htop`) on
many distros.

## Real-world example

An engineer suspects a runaway process is consuming excessive CPU. `top`, sorted by CPU (`P`),
immediately shows which process is the culprit and its current usage live, updating in real time
as they consider whether to kill it. Once identified, `ps aux | grep processname` gets the exact
PID and full command line for a definitive kill command — snapshot and live view used together,
each for what it's actually good at.

## Commands

- [`ps`](../../commands/ps.md) — full syntax and examples
- [`top`](../../commands/top.md) — full syntax and examples

`htop` is covered here conceptually; its interactive UI doesn't fit this documentation's
flag-driven command-page format as cleanly, so it's a documented gap.

## Production example

```
$ ps aux | grep nginx
root      1234  0.0  0.1  55432  8192 ?        Ss   09:00   0:00 nginx: master process
www-data  1235  0.1  0.5  56789 12288 ?        S    09:00   0:02 nginx: worker process
$ top
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `ps aux \| grep` to find a specific process's PID | Manually scroll through `top`'s live view to find one process by name |
| Use `top`/`htop` to watch resource usage change in real time | Repeatedly re-run `ps` to approximate what `top` does natively |
| Sort `top` by CPU (`P`) or memory (`M`) when hunting for a resource hog | Eyeball unsorted output trying to spot the highest usage manually |

## Common mistakes

- Using `top`'s static snapshot mentally instead of recognizing it's continuously live — a value
  seen once may already be stale a few seconds later.
- Not knowing `top`'s sort shortcuts (`P` for CPU, `M` for memory) and scanning unsorted output
  manually instead.
- Assuming `htop` is always installed — it's a separate package on many distros, unlike `ps`/`top`
  which are effectively always present.

## Best practices

- Use `ps aux | grep` for finding a specific, known process quickly.
- Use `top`/`htop` for open-ended "what's consuming resources right now" investigation.
- Install `htop` early in any environment you'll be working in regularly — the readability
  improvement over `top` is genuinely significant for daily use.

## Exercises

1. Run `ps aux` and `ps -ef` and compare their column layouts.
2. Open `top`, sort by CPU usage (`P`), then by memory usage (`M`).
3. If available, compare `htop`'s interface to `top`'s for the same live data.

## Quiz

**Q: What's the key difference between `ps` and `top`?**
<details><summary>Show answer</summary>
`ps` gives a one-time snapshot of processes at the moment it's run; `top` gives a continuously
refreshing live view.
</details>

**Q: In `top`, what does pressing `P` do?**
<details><summary>Show answer</summary>
Sorts the process list by CPU usage, highest first — useful for quickly spotting a resource hog.
</details>

## Interview questions

- How would you find the exact PID of a specific running process from the command line? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `ps` is a snapshot, good for scripting/filtering; `top`/`htop` are live views, good for
  real-time investigation.
- `ps aux | grep name` is one of the most common command combinations in daily Linux work.
- `top`'s sort shortcuts (`P`, `M`) beat manually scanning unsorted output.
- `htop` isn't installed by default everywhere but is usually worth adding.

## Related topics

- [kill, killall, pkill, and Signals](kill-killall-pkill-and-signals.md)
