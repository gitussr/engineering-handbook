---
title: "sftp — Interactive File Transfer Over SSH"
description: "A command-driven, interactive session for browsing a remote filesystem and transferring files, over an encrypted SSH connection."
relatedConcepts: ["13-ssh/scp-sftp-rsync"]
relatedCommands: ["scp", "rsync"]
careerRelevance: ["devops", "cloud", "sre", "backend"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["sftp command examples", "sftp get put", "sftp interactive session", "sftp vs scp"]
canonicalUrl: "/commands/sftp"
---

# sftp

🟢 Must Know · Relevant for: DevOps · Cloud · SRE · Backend

> **TL;DR:** `sftp user@host` opens an interactive session with its own commands (`ls`, `cd`,
> `get`, `put`) for browsing and transferring files — useful when you need to look around before
> deciding what to move, unlike `scp`'s one-shot copy.

## Purpose

`sftp` opens an interactive, encrypted file transfer session — see
[scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md) for how it compares to the other two.

## Syntax

```
sftp [OPTIONS] [user@]host
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `[user@]host` | The remote host to connect to | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-P PORT` | Non-default SSH port |
| `-i FILE` | Use a specific private key |

## Examples

```
$ sftp deploy@web-01.internal
```
Open an interactive session.

Inside the session:

```
sftp> ls
sftp> cd /var/log
sftp> get app.log
sftp> put localfile.txt /tmp/
sftp> exit
```

List, navigate, download (`get`), upload (`put`), and exit — all within one session.

## Expected Output

```
$ sftp deploy@web-01.internal
Connected to web-01.internal.
sftp> ls
app.log  releases  config
sftp>
```

## Exit Status

`0` on a clean session exit, non-zero on connection or authentication failure.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Permission denied` on connect | Authentication failed | Verify key or password access the same way you would for `ssh` |
| `get`/`put` fails for a specific file | No read/write permission at that path | Check remote-side file permissions (Module 07) |
| Regular shell commands don't work inside the session | `sftp` has its own command set, not a shell | Use `sftp`'s own commands (`ls`, `cd`, `get`, `put`, `mkdir`, `rm`) |

## Security Considerations

Same encryption and authentication guarantees as SSH itself, since `sftp` runs over the same
connection type.

## Performance Considerations

Reasonable for interactive, exploratory work; for large or repeated bulk transfers, [`rsync`](rsync.md)
remains more efficient since `sftp` (like `scp`) transfers full files without comparing against
the destination first.

## Production Usage

`sftp` is most useful when you genuinely don't know the exact remote path or filename in advance
and need to look around first — once you know exactly what to transfer, `scp`/`rsync` are more
direct.

## Related Commands

- [`scp`](scp.md) — simpler, non-interactive alternative when you already know the exact path
- [`rsync`](rsync.md) — efficient alternative for large or repeated transfers

## Related Concepts

- [scp, sftp, rsync](../docs/13-ssh/scp-sftp-rsync.md)
