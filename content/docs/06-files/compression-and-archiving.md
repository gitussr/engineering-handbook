---
title: "Compression and Archiving"
description: "tar, gzip, and zip — why archiving (bundling files together) and compression (shrinking them) are two separate steps, even when one command seems to do both."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/finding-files"]
relatedTopics: ["comparing-files"]
relatedCommands: ["tar"]
careerRelevance: ["devops", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#tar-vs-zip"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/comparing-files"
prevTopic: "06-files/finding-files"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["tar command linux", "tar czf", "gzip vs zip", "tar extract"]
canonicalUrl: "/docs/files/compression-and-archiving"
---

# Compression and Archiving

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cloud

> **TL;DR:** Archiving bundles multiple files into one; compression shrinks that bundle's size.
> `tar` archives (and, with `-z`, also compresses via gzip in the same step) — `tar czf
> archive.tar.gz files/` is the command to memorize. `zip` does both in one Windows-compatible
> format.

## What is it?

Archiving combines multiple files/directories into a single file, preserving structure.
Compression reduces that file's size using an algorithm. `tar` is Linux's standard archiving
tool (traditionally paired with `gzip` for compression); `zip` is a format that does both at
once and is more broadly cross-platform compatible.

## Why does it exist?

Transferring or backing up many files is easier as one file than as thousands of separate ones.
Compression on top of that saves space and transfer time. These are historically two separate
concerns — `tar` alone only archives, without compressing — because Unix tools favor doing one
job well and composing them, exactly the philosophy from Module 01.

## Where is it used?

- Bundling a project or deployment artifact for transfer
- Backups
- Downloading and extracting third-party software distributed as a `.tar.gz`
- Cross-platform file sharing where `.zip` is more universally expected (e.g. sending files to a
  Windows user)

## How it works

> 📊 Diagram: two-step pipeline shown as one visual — many separate files → "tar" (bundled into
> one `.tar` archive, same total size) → "gzip" (compressed into a smaller `.tar.gz`) — with a
> note that `tar -z` does both steps in one command invocation even though they're conceptually
> separate operations.

| Tool | Archives? | Compresses? | Common extension |
|---|---|---|---|
| `tar` alone | Yes | No | `.tar` |
| `tar -z` (uses gzip) | Yes | Yes | `.tar.gz` / `.tgz` |
| `gzip` alone | No (single file only) | Yes | `.gz` |
| `zip` | Yes | Yes | `.zip` |

`tar`'s most-used flag combination: `czf` (Create, gZip, File) to create, `xzf` (eXtract, gZip,
File) to extract. `gzip` alone only compresses a single file (and replaces it with the
compressed version) — it has no concept of bundling multiple files, which is exactly why it's
normally paired with `tar` for that part.

## Real-world example

A team packages a deployment artifact as `app-v2.4.1.tar.gz` — `tar` bundles the application's
files and directory structure, `-z` compresses the result, producing one small file to transfer
to a server and extract in place. The same team, when sharing a folder with a client who only
has Windows tools handy, uses `zip` instead, since it's more likely to open cleanly without any
extra software.

## Commands

- [`tar`](../../commands/tar.md) — full syntax and examples

`gzip` and `zip` are taught in full here; their own canonical command pages are a documented gap.

## Production example

```
$ tar czf app-v2.4.1.tar.gz app/
$ tar tzf app-v2.4.1.tar.gz | head -5
$ tar xzf app-v2.4.1.tar.gz -C /opt/releases/
```

Create, then peek at contents without fully extracting (`t` = list), then extract to a specific
location.

## Do / Don't

| Do | Don't |
|---|---|
| Use `tar tzf archive.tar.gz` to preview contents before extracting | Extract an unfamiliar archive directly without checking what's in it |
| Use `-C directory` to extract into a specific location | Extract into your current directory and lose track of where files landed |
| Use `.zip` for cross-platform sharing (especially with Windows users) | Assume `.tar.gz` opens easily everywhere without extra tools |

## Common mistakes

- Forgetting `-z` and creating an uncompressed `.tar` when compression was intended.
- Extracting an archive without checking its contents first (`tar tzf`), potentially scattering
  many files into the current directory unexpectedly.
- Mixing up `c` (create) and `x` (extract) flags — easy to do since they're one-letter apart and
  the consequence of using the wrong one against an existing archive can overwrite it.

## Best practices

- Memorize `tar czf` (create) and `tar xzf` (extract) as the two commands covering the vast
  majority of daily use.
- Preview an unfamiliar archive's contents (`tar tzf`) before extracting.
- Use `-C directory` to control exactly where extraction lands, rather than relying on your
  current directory.

## Exercises

1. Archive and compress a test directory with `tar czf`.
2. List the archive's contents without extracting, using `tar tzf`.
3. Extract the archive into a different directory using `-C`.

## Quiz

**Q: What's the difference between archiving and compression?**
<details><summary>Show answer</summary>
Archiving bundles multiple files into one, preserving structure; compression shrinks that
bundle's size using an algorithm. `tar -z` does both in one step, but they're conceptually
separate operations.
</details>

**Q: What does `tar tzf archive.tar.gz` do?**
<details><summary>Show answer</summary>
Lists the archive's contents without extracting them — useful for previewing an unfamiliar
archive before extracting it.
</details>

## Interview questions

- What's the difference between `tar`, `gzip`, and `zip`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Archiving (bundling) and compression (shrinking) are separate concerns, even when combined in
  one `tar -z` command.
- `tar czf` to create, `tar xzf` to extract, `tar tzf` to preview — the three flag combinations
  worth memorizing.
- `zip` is the more cross-platform-friendly choice when sharing with non-Linux users.

## Related topics

- [Comparing Files](comparing-files.md)
