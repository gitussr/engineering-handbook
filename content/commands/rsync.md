---
title: "rsync — Synchronize Files Efficiently"
description: "Compare source and destination first, then transfer only what changed — the standard tool for large or repeated file transfers, over SSH or locally."
relatedConcepts: ["13-ssh/scp-sftp-rsync"]
relatedCommands: ["scp", "sftp"]
careerRelevance: ["devops", "cloud", "sre", "backend"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["rsync command examples", "rsync -avz", "rsync delete flag", "rsync over ssh"]
canonicalUrl: "/commands/rsync"
---

# rsync

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Backend

> **TL;DR:** `rsync -avz source/ user@host:destination/` syncs a directory, transferring only
> files that changed since the last run. `--delete` also removes destination files no longer
> present at the source — powerful, but genuinely destructive if the source path is wrong.

## Purpose

`rsync` synchronizes files and directories efficiently, locally or over SSH — see
[scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md) for how it compares to the other two.

## Syntax

```
rsync [OPTIONS] SOURCE DESTINATION
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SOURCE` | Local path or `user@host:path` | Yes |
| `DESTINATION` | Local path or `user@host:path` | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-a` | Archive mode — preserves permissions, timestamps, symlinks; implies recursive |
| `-v` | Verbose — show what's being transferred |
| `-z` | Compress data during transfer |
| `--delete` | Remove files at the destination that no longer exist at the source |
| `-n` / `--dry-run` | Show what would be transferred without actually doing it |
| `-e "ssh -p PORT"` | Use a non-default SSH port |

## Examples

```
$ rsync -avz ./dist/ deploy@web-01.internal:/var/www/site/
```
Sync a local directory to a remote destination, compressed.

```
$ rsync -avz --delete ./dist/ deploy@web-01.internal:/var/www/site/
```
Same, but also remove destination files no longer present locally — an exact mirror.

```
$ rsync -avzn ./dist/ deploy@web-01.internal:/var/www/site/
```
Dry run — preview exactly what would transfer, without actually transferring anything.

**Trailing slash matters**: `source/` (with a trailing slash) copies the *contents* of `source`
into the destination; `source` (without it) copies the `source` directory itself into the
destination, one level deeper than intended if that's not what you meant.

## Expected Output

```
$ rsync -avz ./dist/ deploy@web-01.internal:/var/www/site/
sending incremental file list
index.html
assets/style.css

sent 4,213 bytes  received 89 bytes  2,868.00 bytes/sec
total size is 12,450  speedup is 2.89
```

Only changed files are listed — unchanged files are silently skipped.

## Exit Status

`0` on success, non-zero on connection failure or if any file transfer fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Destination has an unexpected extra nested directory | Missing trailing slash on the source path | Add the trailing slash (`source/`) to copy contents, not the directory itself |
| Unexpected files removed at destination | `--delete` used with a wrong or overly broad source path | Always run with `-n` (dry run) first before using `--delete` for real |
| Slow on a huge, mostly-unchanged tree the first time | Initial run has nothing to compare against yet, so everything transfers | Expected on the first sync — subsequent runs will be fast |

## Security Considerations

`--delete` is genuinely destructive at the destination — always verify with a dry run (`-n`)
before running it for real, especially in a backup or deployment script.

## Performance Considerations

Dramatically more efficient than `scp` for repeated transfers of mostly-unchanged directories,
since only differences are sent — the entire reason it's the standard choice for backups and
deployments.

## Compatibility Notes

`rsync` works both locally (no `user@host:` prefix) and over SSH — the same tool, same flags,
whether syncing between two local directories or across the network.

## Production Usage

`rsync -avz --delete` (after verifying with `-n` first) is the standard pattern for deployment and
backup jobs that need the destination to be an exact, efficiently-updated mirror of the source.

## Related Commands

- [`scp`](scp.md) — simpler, but always transfers everything
- [`sftp`](sftp.md) — interactive alternative for exploratory transfers

## Related Concepts

- [scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md)
