---
title: "Disk Usage: df, du"
description: "df shows how full each mounted filesystem is; du shows how much space a specific directory or file actually uses — different questions, often confused."
module: "15-storage"
moduleTitle: "Storage"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["15-storage/raid-basics"]
relatedTopics: ["swap-space"]
relatedCommands: ["df", "du"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#df-vs-du"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "15-storage/swap-space"
prevTopic: "15-storage/raid-basics"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["df -h explained", "du -sh explained", "df vs du", "find large directories linux"]
canonicalUrl: "/docs/storage/disk-usage-df-du"
---

# Disk Usage: df, du

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** `df -h` shows how full each mounted filesystem is — the first command to run when
> "disk full" is suspected. `du -sh path` shows how much space a specific directory actually
> uses — the tool for finding *what's* consuming the space `df` reported as full.

## What is it?

Two commands answering related but genuinely different questions: `df` ("disk free") reports
space usage per mounted filesystem as a whole; `du` ("disk usage") reports how much space a
specific file or directory tree actually consumes.

## Why does it exist?

Knowing a filesystem is 95% full (`df`) doesn't tell you *what's* using that space — `du` answers
that follow-up question by walking a directory tree and totaling actual file sizes. Together they
form the standard two-step process for any "disk full" investigation: confirm the problem with
`df`, then locate the cause with `du`.

## Where is it used?

The very first check in almost any "disk full" or "why is this server slow/failing" incident, and
routine capacity monitoring to catch a filling filesystem before it becomes an outage.

## How it works

> 📊 Diagram: `df -h` output shown listing every mounted filesystem with a percentage-full bar,
> one filesystem highlighted at 95% — next to `du -sh /var/log/*` output shown breaking that same
> filesystem's usage down directory by directory, revealing one specific directory responsible
> for most of the space.

| Command | Answers |
|---|---|
| `df -h` | How full is each mounted filesystem, in human-readable sizes? |
| `du -sh PATH` | How much space does this specific file/directory tree use, summarized? |
| `du -h --max-depth=1 PATH` | Break down usage one directory level at a time (find the big offender) |

**A subtlety `df` and `du` can disagree on**: if a process still has an open file handle to a
file that's been deleted, `df` still counts that space as used (the kernel hasn't actually freed
it while a process holds it open) while `du` — which only sees what's currently visible in the
directory tree — won't show it at all, since the file no longer has a name to walk to.

## Real-world example

A server's `/var` filesystem reports 98% full via `df -h`, but there's no obvious single huge
file when browsing normally. `du -h --max-depth=1 /var/log` reveals one application's log
directory consuming the overwhelming majority of the space — a runaway log file that grew far
larger than expected. Deleting or truncating it and confirming with `df -h` again closes the loop
on the investigation.

## Commands

- [`df`](../../commands/df.md) — full syntax and examples
- [`du`](../../commands/du.md) — full syntax and examples

## Production example

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        39G   37G  0.5G  98% /

$ du -h --max-depth=1 /var/log
2.1G    /var/log/myapp
120M    /var/log/nginx
2.3G    /var/log
```

`myapp`'s log directory is clearly the dominant consumer — the concrete next step for freeing
space.

## Do / Don't

| Do | Don't |
|---|---|
| Run `df -h` first to confirm and quantify a suspected disk-full issue | Jump straight to `du` on the whole filesystem without confirming the problem first |
| Use `du -h --max-depth=1` repeatedly, narrowing down one level at a time | Run an unscoped `du -h` across an entire large filesystem and wait on it |
| Consider open-but-deleted file handles when `df` and `du` disagree | Assume `df` and `du` must always report the same number for the same filesystem |

## Common mistakes

- Confusing `df` (filesystem-level) and `du` (specific-path-level) as interchangeable — they
  answer different questions and are used at different stages of an investigation.
- Running an unscoped `du -h` across a huge filesystem, which can take a very long time; narrowing
  with `--max-depth` first is much faster.
- Not accounting for the open-but-deleted-file-handle case when `df` reports space `du` can't
  account for — restarting the process holding that handle (or the culprit service) is usually
  what actually frees the space.

## Best practices

- Always confirm with `df -h` before diagnosing further — it's the fast, whole-filesystem view.
- Narrow down with `du -h --max-depth=1`, descending one level at a time, rather than running an
  unscoped `du` across everything at once.
- If `df` and `du` numbers don't reconcile, suspect an open-but-deleted file handle and check
  which process might be holding it (`lsof`, referenced ahead of its own dedicated coverage).

## Exercises

1. Run `df -h` on a system you have access to and identify the fullest mounted filesystem.
2. Run `du -h --max-depth=1` against a large directory and identify the biggest subdirectory.
3. Explain in one sentence why `df` and `du` can occasionally report different numbers for the
   same filesystem.

## Quiz

**Q: What's the practical difference between `df` and `du`?**
<details><summary>Show answer</summary>
`df` reports how full each mounted filesystem is as a whole; `du` reports how much space a
specific file or directory tree actually consumes — the tool for finding what's using the space
`df` reported.
</details>

**Q: Why might `df` show a filesystem as full when `du` on its visible contents doesn't add up to
the same total?**
<details><summary>Show answer</summary>
A process may still hold an open file handle to a file that's been deleted — the kernel hasn't
freed that space yet, so `df` still counts it, while `du` only sees what's currently visible in
the directory tree and can't see a nameless, deleted-but-open file.
</details>

## Interview questions

- A server reports a filesystem is 98% full — walk through how you'd find out what's using the
  space. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `df -h` shows overall filesystem usage; `du -h` shows usage for a specific file/directory tree.
- The standard disk-full investigation: confirm with `df`, locate the cause with `du`.
- `du -h --max-depth=1` narrows down usage one directory level at a time, much faster than an
  unscoped run.
- `df` and `du` can disagree when a process holds an open handle to a deleted file.

## Related topics

- [Swap Space](swap-space.md)
- [RAID Basics](raid-basics.md)
