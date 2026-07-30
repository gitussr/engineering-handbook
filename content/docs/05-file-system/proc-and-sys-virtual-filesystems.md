---
title: "/proc and /sys Virtual Filesystems"
description: "Live kernel and process data exposed as files — how to read it, and why nothing under /proc or /sys is a real file on disk."
module: "05-file-system"
moduleTitle: "File System"
stage: "beginner"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["05-file-system/mounting-and-unmounting"]
relatedTopics: ["inodes-explained"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#proc-vs-sys"]
relatedCheatsheet: ""
furtherReading: [{"label": "proc(5) man page", "url": "https://man7.org/linux/man-pages/man5/proc.5.html"}]
nextTopic: "05-file-system/inodes-explained"
prevTopic: "05-file-system/mounting-and-unmounting"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["proc filesystem linux", "sys filesystem linux", "proc cpuinfo", "proc meminfo", "sysctl proc"]
canonicalUrl: "/docs/file-system/proc-and-sys-virtual-filesystems"
---

# /proc and /sys Virtual Filesystems

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `/proc` and `/sys` aren't real files on disk — they're a live, in-memory view into
> the kernel and running processes, generated on the fly every time you read them. `cat
> /proc/cpuinfo` doesn't read a file; it asks the kernel a question and gets the current answer.

## What is it?

`/proc` and `/sys` are virtual (pseudo) filesystems — they appear as normal directories and files
under the "everything is a file" principle from
[Linux Philosophy and the FHS](../01-linux-basics/linux-philosophy-and-fhs.md), but nothing in
them is stored on disk. Reading a file under `/proc` or `/sys` triggers the kernel to generate
that data live, at the moment you read it.

## Why does it exist?

The kernel needs a way to expose live system and process state — CPU info, memory usage, running
processes, hardware/driver details — to userspace tools, without inventing a separate API for
every tool that needs it. Exposing this as ordinary-looking files means every existing text tool
(`cat`, `grep`, `awk`) already knows how to read kernel state, with zero special-purpose tooling
needed.

## Where is it used?

- Monitoring tools (`top`, `htop`, and virtually every metrics agent) read `/proc` under the hood
  for CPU, memory, and process data
- Debugging a specific process via `/proc/{pid}/` — its open files, memory maps, environment
- Kernel parameter tuning via `/proc/sys` and `sysctl` (previewed here, full depth in
  [Module 21: Performance](../21-performance/index.md))
- Hardware and driver information exposed via `/sys/class`, `/sys/block`, etc.

## How it works

> 📊 Diagram: a kernel box with an arrow labeled "live query" pointing to a `/proc/cpuinfo` file
> icon, contrasted with a normal disk file with an arrow labeled "read from disk" — emphasizing
> that `/proc` reads trigger kernel computation, not disk I/O.

- **`/proc`** — primarily process and system information. `/proc/cpuinfo`, `/proc/meminfo` for
  system-wide data; `/proc/{pid}/` (a directory per running process) for per-process detail —
  open file descriptors, memory maps, command-line arguments, environment variables.
- **`/sys`** — primarily hardware, driver, and kernel subsystem information, organized around the
  kernel's internal device model. `/sys/class/net/` for network interfaces, `/sys/block/` for
  block devices.
- Both support **writing**, not just reading, in specific locations — writing to certain files
  under `/proc/sys` changes live kernel behavior immediately (the mechanism behind `sysctl`,
  covered fully in Module 21). This is a real, if advanced, way to tune a running kernel without
  a reboot.

## Real-world example

An SRE investigating high memory usage on a server reads `/proc/meminfo` for a system-wide
snapshot, then checks `/proc/{pid}/status` for a specific suspicious process to see exactly how
much memory it's using and what state it's in — all without installing any additional tooling,
because this information is already exposed directly by the kernel through the filesystem.

## Commands

No new canonical command page on this page — `cat` (already covered informally in Modules 01/02)
is used throughout; `sysctl` is a forward reference to
[Module 21: Performance](../21-performance/index.md).

## Production example

```
$ cat /proc/cpuinfo | grep "model name" | head -1
model name  : Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz
$ cat /proc/meminfo | head -3
MemTotal:       16374572 kB
MemFree:         2103456 kB
MemAvailable:    9871234 kB
$ ls /proc/1234/
cmdline  environ  fd  status  ...
```

## Do / Don't

| Do | Don't |
|---|---|
| Read `/proc` and `/sys` directly when debugging without extra tooling | Assume you need a special monitoring tool for basic kernel/process data |
| Understand a write to `/proc/sys` changes live kernel behavior immediately | Write to `/proc/sys` files without understanding the consequence first |
| Use `/proc/{pid}/` to inspect a specific process in detail | Guess at a process's state when `/proc/{pid}/status` has the real answer |

## Common mistakes

- Treating `/proc`/`/sys` files as if they were regular files you could back up or restore — they
  don't exist on disk at all; there's nothing to back up.
- Writing to a `/proc/sys` kernel parameter without understanding the consequence — this takes
  effect immediately on a live, running system, unlike a config file that needs a restart.
- Assuming `/proc` file sizes are meaningful — many show `0` bytes even though `cat`-ing them
  produces real output, since the "size" isn't a real disk fact.

## Best practices

- Use `/proc` and `/sys` directly for quick, tooling-free debugging — it's faster than reaching
  for a separate utility for basic system state.
- Treat any write to `/proc/sys` as a live production change, not a safe experiment — understand
  the parameter fully first (full depth in Module 21).
- Use `/proc/{pid}/` when investigating a specific misbehaving process rather than only relying on
  `ps`/`top` aggregate views.

## Exercises

1. Read `/proc/cpuinfo` and `/proc/meminfo` and identify one piece of information from each.
2. Find your current shell's own PID (`echo $$`) and explore `/proc/{that pid}/`.
3. Explain in one sentence why `/proc` files don't behave like normal disk files when it comes to
   backups.

## Quiz

**Q: Does reading `/proc/cpuinfo` read a file from disk?**
<details><summary>Show answer</summary>
No — it's generated live by the kernel at the moment you read it; nothing under `/proc` is stored
on disk.
</details>

**Q: What's the practical difference between `/proc` and `/sys`?**
<details><summary>Show answer</summary>
`/proc` is primarily process and general system information; `/sys` is primarily hardware, driver,
and kernel subsystem information organized around the kernel's device model.
</details>

## Interview questions

- Why can monitoring tools like `top` get live system data without querying the kernel through a
  special API? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `/proc` and `/sys` are virtual filesystems — nothing in them is stored on disk; reads trigger
  live kernel computation.
- `/proc` focuses on process/system info; `/sys` focuses on hardware/driver/kernel subsystem info.
- Writing to certain `/proc/sys` files changes live kernel behavior immediately — the mechanism
  behind `sysctl`, fully covered in Module 21.
- `/proc/{pid}/` is a genuinely useful, tooling-free way to inspect a specific process in depth.

## Further Reading

- [proc(5) man page](https://man7.org/linux/man-pages/man5/proc.5.html)

## Related topics

- [Module 21: Performance](../21-performance/index.md)
- [Inodes Explained](inodes-explained.md)
