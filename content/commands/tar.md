---
title: "tar — Archive and Compress Files"
description: "Create, list, and extract tar archives, with gzip compression via the -z flag."
relatedConcepts: ["06-files/compression-and-archiving"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "cloud"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["tar command linux", "tar czf example", "tar extract to directory", "tar list contents"]
canonicalUrl: "/commands/tar"
---

# tar

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Cloud

> **TL;DR:** `tar czf archive.tar.gz files/` creates a compressed archive. `tar xzf
> archive.tar.gz` extracts it. `tar tzf archive.tar.gz` lists contents without extracting.

## Purpose

`tar` bundles multiple files/directories into a single archive, optionally compressing them via
gzip in the same step — the command behind
[Compression and Archiving](../docs/06-files/compression-and-archiving.md).

## Syntax

```
tar [OPTIONS] -f ARCHIVE [FILES...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `ARCHIVE` | The archive file to create, read, or extract | Yes |
| `FILES` | Files/directories to include (when creating) | Yes, when creating |

## Options

| Flag | Meaning |
|---|---|
| `-c` | Create a new archive |
| `-x` | Extract an archive |
| `-t` | List (table of) contents without extracting |
| `-z` | Compress/decompress with gzip |
| `-f ARCHIVE` | Specify the archive filename (almost always needed) |
| `-C DIR` | Change to DIR before extracting/creating (controls where files land) |
| `-v` | Verbose — list each file as it's processed |

## Examples

```
$ tar czf app.tar.gz app/
```
Create a compressed archive from a directory.

```
$ tar xzf app.tar.gz
```
Extract into the current directory.

```
$ tar xzf app.tar.gz -C /opt/releases/
```
Extract into a specific directory.

```
$ tar tzf app.tar.gz
```
List contents without extracting.

## Expected Output

```
$ tar tzf app.tar.gz
app/
app/main.py
app/config.yaml
```

## Exit Status

`0` on success, `2` on most errors (missing file, corrupt archive, invalid options).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `tar: Error opening archive` | Wrong filename, or file isn't actually a tar archive | Verify the path and that `-z` matches whether it's actually gzip-compressed |
| Files extracted into the wrong location | No `-C` used, extracted into current directory | Use `-C directory` to control extraction location |
| `gzip: stdin: not in gzip format` | Used `-z` on a non-gzip-compressed `.tar` file | Drop `-z` for a plain uncompressed `.tar` |

## Security Considerations

Extracting an untrusted archive can, in principle, write files outside the intended directory if
crafted maliciously (a "path traversal" style risk) — preview contents (`tar tzf`) before
extracting anything from an untrusted source.

## Performance Considerations

Compression (`-z`) trades CPU time for smaller output size — usually a good trade for network
transfer or storage, though for very large archives on CPU-constrained systems it's worth being
aware of.

## Production Usage

`tar czf` is the standard way deployment artifacts are packaged for transfer to servers; the
`-C` flag is what lets a CI/CD pipeline extract an artifact into a precise, predictable location
every time.

## Related Commands

None documented yet — `gzip` and `zip` are covered in
[Compression and Archiving](../docs/06-files/compression-and-archiving.md) but don't have
separate canonical pages yet.

## Related Concepts

- [Compression and Archiving](../docs/06-files/compression-and-archiving.md)
