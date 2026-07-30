---
title: "Finding Files"
description: "find, locate, which, and whereis — searching by content and criteria versus searching a fast pre-built index versus locating an executable."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/editing-files"]
relatedTopics: ["compression-and-archiving"]
relatedCommands: ["find"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#find-vs-locate"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/compression-and-archiving"
prevTopic: "06-files/editing-files"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["find command linux", "locate command linux", "which vs whereis", "find by name"]
canonicalUrl: "/docs/files/finding-files"
---

# Finding Files

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `find` searches the live filesystem by name, type, size, or age — slower but always
> current. `locate` searches a pre-built index — instant but can be stale. `which` and `whereis`
> answer a narrower question: where does a specific command live.

## What is it?

Four related but distinct tools for answering "where is this": `find` (flexible, live search),
`locate` (fast, indexed search), and `which`/`whereis` (specifically for locating commands/
executables, related to [The PATH Variable](../04-bash/path-variable.md)).

## Why does it exist?

Different "where is it" questions need different tools. Searching an entire filesystem live
(`find`) is thorough but can be slow on a large filesystem; `locate` trades live accuracy for
speed by searching a pre-built database instead; `which`/`whereis` are narrower, purpose-built for
the specific and extremely common question of "which executable will actually run for this
command name."

## Where is it used?

- `find` — searching by name pattern, file type, size, modification time, permissions — the
  general-purpose tool
- `locate` — a near-instant name search when the index is reasonably fresh (updated periodically,
  usually via a scheduled job)
- `which` — confirming which executable `PATH` resolves for a command (echoes back to
  [The PATH Variable](../04-bash/path-variable.md))
- `whereis` — locating a command's binary, source, and man page together

## How it works

> 📊 Diagram: two search approaches side by side — `find` shown walking the live filesystem tree
> directly, `locate` shown querying a small pre-built database file instead of touching the disk
> tree at all — making the speed-vs-freshness tradeoff visible.

| Command | Searches | Speed | Freshness |
|---|---|---|---|
| `find PATH -name pattern` | Live filesystem, from PATH down | Slower on large trees | Always current |
| `locate pattern` | A pre-built index (`updatedb`) | Near-instant | Can be stale until the index refreshes |
| `which command` | `PATH` directories, for an executable | Instant | Always current |
| `whereis command` | Standard binary/man/source locations | Instant | Always current |

`find` supports far more than name matching — `-type f` (files only), `-size +100M` (larger than
100MB), `-mtime -1` (modified in the last day), and combinations of these, making it genuinely
powerful for targeted searches, not just "find this filename."

## Real-world example

An SRE needs to find every log file larger than 500MB across a server to free up disk space
during an incident. `locate` can't answer this — it only does name matching against a static
index. `find /var/log -type f -size +500M` answers it directly, live, with the exact current
state of the disk, which is precisely why `find`'s extra flexibility matters here.

## Commands

- [`find`](../../commands/find.md) — full syntax and examples

`locate`, `which`, and `whereis` are taught in full here; their own canonical command pages are a
documented gap.

## Production example

```
$ find /var/log -type f -size +500M
/var/log/nginx/access.log
$ which python3
/usr/bin/python3
$ locate nginx.conf
/etc/nginx/nginx.conf
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `find` when you need current, criteria-based results (size, age, type) | Rely on `locate` when the answer must reflect the current filesystem exactly |
| Use `which` to debug which executable a command name actually resolves to | Guess which version of a duplicated command name is running |
| Narrow `find`'s search path when you know roughly where to look | Search from `/` when you already know the general location |

## Common mistakes

- Using `locate` and getting a stale result for a very recently created or deleted file — the
  index hasn't caught up yet.
- Running `find /` for something you know is under a specific directory, making the search far
  slower than necessary.
- Confusing `which` (finds an executable via `PATH`) with `find`/`locate` (find arbitrary files by
  name) — they answer different kinds of questions.

## Best practices

- Reach for `find` whenever the search needs to be criteria-based (size, type, modification time)
  or must reflect the current filesystem exactly.
- Use `locate` for fast, simple name lookups where slight staleness doesn't matter.
- Scope `find`'s search path as narrowly as you reasonably can — it's both faster and produces
  less noise in the results.

## Exercises

1. Use `find` to locate all files modified in the last 24 hours under your home directory.
2. Use `which` to confirm which executable a common command (e.g. `python3`, `bash`) resolves to.
3. Explain in one sentence why `locate` might miss a file created seconds ago.

## Quiz

**Q: Why might `locate` fail to find a file that `find` would find immediately?**
<details><summary>Show answer</summary>
`locate` searches a pre-built index that's only updated periodically, not the live filesystem —
a very recently created file may not be in the index yet.
</details>

**Q: What question does `which` specifically answer?**
<details><summary>Show answer</summary>
Which executable, from the directories listed in `PATH`, will actually run for a given command
name.
</details>

## Interview questions

- When would you use `find` instead of `locate`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `find` searches live and supports rich criteria (size, type, age); `locate` is faster but can be
  stale.
- `which`/`whereis` are specifically for locating commands/executables, not general files.
- Scope `find`'s search path narrowly for speed; use it whenever accuracy matters more than raw
  speed.

## Related topics

- [The PATH Variable](../04-bash/path-variable.md)
- [Compression and Archiving](compression-and-archiving.md)
