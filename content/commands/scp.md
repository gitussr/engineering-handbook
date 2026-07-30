---
title: "scp — Copy Files Over SSH"
description: "A simple, one-shot file copy between local and remote hosts (or between two remote hosts), encrypted over SSH."
relatedConcepts: ["13-ssh/scp-sftp-rsync"]
relatedCommands: ["rsync", "sftp"]
careerRelevance: ["devops", "cloud", "sre", "backend"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["scp command examples", "scp copy directory", "scp between two remote hosts", "scp vs rsync"]
canonicalUrl: "/commands/scp"
---

# scp

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Backend

> **TL;DR:** `scp file.txt user@host:/path/` copies a local file to a remote server. `scp -r`
> copies a directory. Always transfers everything, every time — for large or repeated transfers,
> reach for `rsync` instead.

## Purpose

`scp` copies files over an encrypted SSH connection — see
[scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md) for how it compares to the other two.

## Syntax

```
scp [OPTIONS] SOURCE DESTINATION
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `SOURCE` | Local or `user@host:path` remote source | Yes |
| `DESTINATION` | Local or `user@host:path` remote destination | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-r` | Copy directories recursively |
| `-P PORT` | Non-default SSH port (capital `P`, unlike `ssh`'s lowercase `-p`) |
| `-i FILE` | Use a specific private key |
| `-C` | Compress data during transfer |

## Examples

```
$ scp app.tar.gz deploy@web-01.internal:/opt/releases/
```
Copy a local file to a remote server.

```
$ scp deploy@web-01.internal:/var/log/app.log ./
```
Copy a remote file to the local machine.

```
$ scp -r ./dist deploy@web-01.internal:/var/www/site/
```
Copy a local directory recursively.

```
$ scp -P 2222 app.tar.gz deploy@web-01.internal:/opt/
```
Copy to a server using a non-default SSH port.

## Expected Output

```
$ scp app.tar.gz deploy@web-01.internal:/opt/releases/
app.tar.gz                    100%   42MB  15.2MB/s   00:02
```

## Exit Status

`0` on success, non-zero on connection failure, authentication failure, or if the destination
path is invalid.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No such file or directory` | Source path wrong, or destination directory doesn't exist | Verify both paths; create the destination directory first if needed |
| `Permission denied` | No write access at the destination, or authentication failed | Check destination permissions, or SSH access itself |
| Copying a directory fails silently or copies nothing | Forgot `-r` | Add `-r` for directories |

## Security Considerations

Inherits all of SSH's encryption and authentication — the transfer itself is as secure as the
underlying SSH connection.

## Performance Considerations

Always transfers the full file(s) regardless of whether the destination already has an identical
or similar version — for large or repeated transfers, [`rsync`](rsync.md) is significantly more
efficient since it only transfers differences.

## Production Usage

`scp` remains the fastest option for a genuinely one-off, single-file copy where `rsync`'s
comparison overhead isn't worth it — but reach for `rsync` the moment the same transfer will
happen more than once.

## Related Commands

- [`rsync`](rsync.md) — efficient alternative for large or repeated transfers
- [`sftp`](sftp.md) — interactive alternative for browsing before transferring

## Related Concepts

- [scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md)
