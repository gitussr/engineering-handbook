---
title: "sha256sum — Compute and Verify SHA-256 Checksums"
description: "Compute a file's SHA-256 checksum, or verify it against a published value — the standard way to confirm a downloaded ISO or package hasn't been corrupted or tampered with."
relatedConcepts: ["02-installing-linux/installing-ubuntu-debian", "02-installing-linux/installing-rhel-centos-rocky"]
relatedCommands: []
careerRelevance: ["linux-administrator", "devops", "cybersecurity"]
difficulty: "must-know"
compatibility: []
updatedAt: "2026-07-30"
keywords: ["sha256sum command linux", "verify iso checksum", "sha256sum verify", "check file integrity linux"]
canonicalUrl: "/commands/sha256sum"
---

# sha256sum

🟢 Must Know · Relevant for: Linux Administrator · DevOps · Cybersecurity

> **TL;DR:** `sha256sum file` prints a file's SHA-256 hash. Compare it against the value the
> publisher provides — a mismatch means the download is corrupted or was tampered with, and you
> should not use it.

## Purpose

`sha256sum` computes the SHA-256 cryptographic hash of a file, producing a fixed-length value
unique to that exact file's contents. Comparing this against a publisher's published checksum is
the standard way to confirm a downloaded file (an ISO, a package, an installer) is byte-for-byte
what it claims to be — see
[Installing Ubuntu/Debian](../docs/02-installing-linux/installing-ubuntu-debian.md) and
[Installing RHEL/CentOS/Rocky](../docs/02-installing-linux/installing-rhel-centos-rocky.md) for
where this fits into a real install workflow.

## Syntax

```
sha256sum [OPTIONS] [FILE...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files to hash | No — reads from standard input if omitted |

## Options

| Flag | Meaning |
|---|---|
| `-c` | Check mode — verify files against a list of checksums instead of computing new ones |
| `--quiet` | With `-c`, suppress "OK" lines and only print failures |

## Examples

```
$ sha256sum ubuntu-22.04.4-live-server-amd64.iso
```
Compute the checksum of a downloaded ISO to compare manually against the published value.

```
$ echo "a1b2c3... ubuntu-22.04.4-live-server-amd64.iso" | sha256sum -c
```
Verify a file against a specific expected checksum, printing OK or FAILED.

```
$ sha256sum -c SHA256SUMS
```
Verify every file listed in a downloaded checksums file at once — the common real-world pattern,
since most distros publish a `SHA256SUMS` file alongside their ISOs.

## Expected Output

```
$ sha256sum ubuntu-22.04.4-live-server-amd64.iso
a1b2c3d4e5f6...  ubuntu-22.04.4-live-server-amd64.iso

$ sha256sum -c SHA256SUMS
ubuntu-22.04.4-live-server-amd64.iso: OK
```

## Exit Status

| Code | Meaning |
|---|---|
| `0` | Success — in check mode, every file matched its expected checksum |
| `1` | In check mode, at least one file failed to match — treat this as "do not use this file" |

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `FAILED` on a file you expect to be fine | The download was corrupted or interrupted partway | Re-download the file completely and check again before using it |
| `sha256sum: WARNING: 1 computed checksum did NOT match` | Same as above — the mismatch is real, not a formatting issue | Do not proceed with an installer/ISO that fails this check |
| No output at all with `-c` | Checksum file format doesn't match what `sha256sum` expects (wrong hash algorithm, wrong line format) | Confirm the checksums file is genuinely SHA-256 (not MD5/SHA-1) and in the standard `hash  filename` format |

## Security Considerations

This is fundamentally a security-relevant command: verifying a checksum confirms a file wasn't
corrupted *or* tampered with in transit, assuming the published checksum itself came from a
trusted source (the official project site over HTTPS, not the same untrusted mirror serving the
file). A matching checksum does not by itself prove authenticity if the checksum's source is
compromised — see [Package Signing and Verification](../docs/14-package-managers/package-signing-and-verification.md)
for the stronger, signature-based guarantee.

## Performance Considerations

Hashing a large ISO (several GB) takes a few seconds to a couple of minutes depending on disk
speed — effectively bounded by how fast the file can be read from disk, not CPU.

## Production Usage

Verifying checksums before using a downloaded installer or ISO is standard practice, especially
over untrusted networks or when downloading from a mirror rather than the primary source. In
automated pipelines, `sha256sum -c` against a pinned checksums file is a common integrity gate
before a downloaded artifact is used further.

## Related Commands

No closely related canonical command page — see
[Package Signing and Verification](../docs/14-package-managers/package-signing-and-verification.md)
for the GPG-based signature verification that provides authenticity, which a checksum alone does not.

## Related Concepts

- [Installing Ubuntu/Debian](../docs/02-installing-linux/installing-ubuntu-debian.md)
- [Installing RHEL/CentOS/Rocky](../docs/02-installing-linux/installing-rhel-centos-rocky.md)
