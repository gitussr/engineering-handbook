---
title: "scp, sftp, rsync"
description: "Three ways to move files over SSH — a simple one-shot copy, an interactive file-browsing session, and an efficient sync tool that only transfers what's changed."
module: "13-ssh"
moduleTitle: "SSH"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["13-ssh/ssh-key-generation-and-key-based-auth"]
relatedTopics: ["ssh-key-generation-and-key-based-auth", "ssh-config-file"]
relatedCommands: ["scp", "sftp", "rsync"]
careerRelevance: ["devops", "cloud", "sre", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#scp-vs-rsync"]
relatedCheatsheet: "ssh"
furtherReading: []
nextTopic: "13-ssh/ssh-config-file"
prevTopic: "13-ssh/ssh-key-generation-and-key-based-auth"
estimatedReadingTime: 7
updatedAt: "2026-07-27"
keywords: ["scp command examples", "sftp command examples", "rsync over ssh", "scp vs rsync"]
canonicalUrl: "/docs/ssh/scp-sftp-rsync"
---

# scp, sftp, rsync

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Backend

> **TL;DR:** `scp` copies files over SSH in one shot — simple, but always transfers everything.
> `sftp` gives an interactive file-browsing session. `rsync` compares source and destination first
> and transfers only what's actually changed — the standard choice for large or repeated
> transfers.

## What is it?

Three tools for moving files over an SSH-encrypted connection, each suited to a different
situation: `scp` (simple one-shot copy), `sftp` (interactive file browsing), and `rsync`
(efficient, incremental synchronization).

## Why does it exist?

Once SSH provides an encrypted, authenticated channel, moving files over that same channel is a
natural next need — and different situations call for different tools. A single quick copy needs
different ergonomics than browsing an unfamiliar remote directory structure, which is different
again from repeatedly syncing a large directory where re-transferring everything every time would
be wasteful.

## Where is it used?

Copying a config file or build artifact to a server (`scp`), exploring an unfamiliar remote
filesystem interactively before deciding what to grab (`sftp`), and deploying or backing up large
directories repeatedly where only changed files should actually be re-transferred (`rsync`).

## How it works

> 📊 Diagram: three panels — `scp` shown as a single arrow copying a whole file every time
> regardless of prior transfers; `sftp` shown as an interactive prompt browsing a remote directory
> tree; `rsync` shown comparing file checksums/timestamps between source and destination first,
> then transferring only the files that actually differ.

| Tool | Best for | Key behavior |
|---|---|---|
| `scp` | A quick, simple one-shot copy | Always transfers the full file(s), every time |
| `sftp` | Interactively browsing and picking files from a remote system | Command-driven session (`ls`, `cd`, `get`, `put`) |
| `rsync` | Large or repeated transfers, backups, deployments | Compares source/destination, transfers only differences |

`rsync`'s "only transfer what changed" behavior is what makes it dramatically faster than
re-running `scp` on a large directory where most files haven't changed since the last transfer.

## Real-world example

A nightly backup job needs to copy a multi-gigabyte directory to a remote server every night, but
only a small fraction of files actually change day to day. Using `scp` would re-transfer the
entire directory every single night regardless. Using `rsync` instead, the job compares source and
destination first and transfers only the handful of files that actually changed, cutting nightly
transfer time and bandwidth dramatically.

## Commands

- [`scp`](../../commands/scp.md) — full syntax and examples
- [`sftp`](../../commands/sftp.md) — full syntax and examples
- [`rsync`](../../commands/rsync.md) — full syntax and examples

## Production example

```
$ scp app.tar.gz deploy@web-01.internal:/opt/releases/

$ rsync -avz --delete /var/www/site/ deploy@web-01.internal:/var/www/site/

$ sftp deploy@web-01.internal
sftp> ls
sftp> get error.log
sftp> exit
```

`rsync -avz --delete` (archive mode, verbose, compressed, deleting files at the destination that
no longer exist at the source) is the standard pattern for keeping two directory trees in sync.

## Do / Don't

| Do | Don't |
|---|---|
| Use `rsync` for large or repeated directory transfers | Use `scp` repeatedly on a large, mostly-unchanged directory |
| Use `sftp` when you need to browse before deciding what to transfer | Guess at remote file paths and repeatedly retry `scp` |
| Understand `--delete` in `rsync` removes destination files not present at the source | Use `--delete` without understanding it can remove files at the destination |

## Common mistakes

- Using `scp` for a large, repeatedly-synced directory, re-transferring everything every time
  instead of reaching for `rsync`.
- Using `rsync --delete` without realizing it will remove files present at the destination but not
  the source — a legitimate, powerful feature that can also delete something unintended if the
  source path is wrong.
- Forgetting `sftp` is its own command-driven session (`get`/`put`, not `cp`/`mv`) and trying to
  use regular shell commands inside it.

## Best practices

- Default to `rsync -avz` for anything beyond a single quick file copy.
- Double-check source and destination paths carefully before running `rsync --delete` — it's
  destructive at the destination by design.
- Use `sftp` for exploratory, interactive work; use `scp`/`rsync` once you already know exactly
  what needs to move.

## Exercises

1. Copy a single file to a remote host with `scp`.
2. Sync a directory to a remote host with `rsync -avz` and note how it reports skipped
   (unchanged) files on a second run.
3. Explain in one sentence why `rsync` is preferred over `scp` for large, repeated transfers.

## Quiz

**Q: Why is `rsync` typically faster than `scp` for repeated transfers of the same directory?**
<details><summary>Show answer</summary>
`rsync` compares source and destination first and transfers only files that have actually
changed, while `scp` always transfers everything regardless of what changed.
</details>

**Q: What does `rsync --delete` do?**
<details><summary>Show answer</summary>
Removes files at the destination that no longer exist at the source, keeping the destination an
exact mirror — a powerful but genuinely destructive option if the source path is wrong.
</details>

## Interview questions

- Why would you choose `rsync` over `scp` for a deployment or backup job? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `scp` is simple but always transfers everything; `rsync` transfers only what changed.
- `sftp` provides an interactive, browsable file transfer session.
- `rsync -avz` is the standard pattern for efficient directory synchronization.
- `rsync --delete` is destructive at the destination — verify paths carefully before using it.

## Related topics

- [SSH Key Generation and Key-Based Auth](ssh-key-generation-and-key-based-auth.md)
- [SSH Config File](ssh-config-file.md)
