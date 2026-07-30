---
title: "wget — Download Files Non-Interactively"
description: "Download a file or a whole directory tree over HTTP(S)/FTP, including resuming interrupted downloads."
relatedConcepts: ["12-networking/curl-wget"]
relatedCommands: ["curl"]
careerRelevance: ["devops", "backend", "sre", "cloud"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["wget command examples", "wget recursive download", "wget resume download", "wget vs curl"]
canonicalUrl: "/commands/wget"
---

# wget

🟢 Must Know · Relevant for: DevOps · Backend · SRE · Cloud

> **TL;DR:** `wget url` downloads a file to disk. `wget -r url` downloads recursively. `wget -c
> url` resumes an interrupted download. Simpler and more download-focused than `curl`.

## Purpose

`wget` downloads files over HTTP(S)/FTP, saving directly to disk — see
[curl, wget](../docs/12-networking/curl-wget.md) for when to reach for it over `curl`.

## Syntax

```
wget [OPTIONS] URL
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `URL` | The address of the file to download | Yes |

## Options

| Flag | Meaning |
|---|---|
| `-O FILE` | Save with a specific filename instead of the URL's basename |
| `-c` | Continue/resume a partially downloaded file |
| `-r` | Recursive download (a whole directory tree) |
| `-q` | Quiet — suppress output |
| `-b` | Run in the background |
| `--limit-rate=RATE` | Cap download speed (e.g. `--limit-rate=200k`) |

## Examples

```
$ wget https://example.com/release-1.2.0.tar.gz
```
Download the file, saved under its own filename.

```
$ wget -O app.tar.gz https://example.com/download?id=42
```
Download and save under a specific filename (useful when the URL itself doesn't end in a clean
filename).

```
$ wget -c https://example.com/large-file.iso
```
Resume a previously interrupted download instead of starting over.

```
$ wget -r https://example.com/docs/
```
Recursively download an entire directory tree.

## Expected Output

```
$ wget https://example.com/release-1.2.0.tar.gz
Saving to: 'release-1.2.0.tar.gz'
release-1.2.0.tar.gz   100%[===================>]  4.2M  5.1MB/s    in 0.8s
```

## Exit Status

`0` on success, non-zero on a network failure, missing file (404), or invalid URL.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `404 Not Found` | URL path is wrong or the file has moved | Verify the URL, e.g. with `curl -I` first |
| Download stalls or fails partway | Unstable connection | Re-run with `-c` to resume instead of restarting from zero |
| Wrong or unexpected filename saved | URL doesn't end in a clean filename | Use `-O` to specify the filename explicitly |

## Security Considerations

Verify checksums (`sha256sum`, Module 06) of downloaded files, especially installer scripts or
binaries, before executing them — `wget` itself does not verify integrity or authenticity.

## Performance Considerations

`--limit-rate` avoids a large download saturating a shared or metered network link; `-c` avoids
wasting bandwidth by resuming rather than restarting a large interrupted download.

## Production Usage

`wget -c` is the standard way to reliably fetch large release artifacts (OS images, large
archives) over an unreliable connection without risking wasted bandwidth on repeated full
restarts.

## Related Commands

- [`curl`](curl.md) — more flexible for inspecting requests/responses, scripting, and non-GET
  methods

## Related Concepts

- [curl, wget](../docs/12-networking/curl-wget.md)
