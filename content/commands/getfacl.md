---
title: "getfacl — Display File Access Control Lists"
description: "View all ACL entries on a file or directory, beyond what ls -l's permission string shows."
relatedConcepts: ["07-permissions/acls"]
relatedCommands: ["setfacl"]
careerRelevance: ["linux-administrator", "cybersecurity", "sre"]
difficulty: "expert"
updatedAt: "2026-07-25"
keywords: ["getfacl example", "view acl linux", "getfacl output explained"]
canonicalUrl: "/commands/getfacl"
---

# getfacl

🔴 Expert · Relevant for: Linux Administrator · Cybersecurity · SRE

> **TL;DR:** `getfacl file` shows every ACL entry on a file, including the standard owner/group/
> other permissions and any additional user/group grants set via `setfacl`.

## Purpose

`getfacl` displays a file or directory's full Access Control List — the inspection counterpart to
[`setfacl`](setfacl.md), covered together in
[ACLs: setfacl, getfacl](../docs/07-permissions/acls.md).

## Syntax

```
getfacl [OPTIONS] FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `FILE` | One or more files/directories to inspect | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-R` | `--recursive` | Show ACLs for a directory and everything inside it |

## Examples

```
$ getfacl report.csv
```
Show all ACL entries for a single file.

## Expected Output

```
$ getfacl report.csv
# file: report.csv
# owner: finance-admin
# group: finance
user::rw-
user:auditor:r--
group::rw-
mask::rw-
other::---
```

Reading this: standard owner (`user::`) and group (`group::`) permissions, an additional
specific-user grant (`user:auditor:r--`), a `mask` entry (the ceiling on effective ACL
permissions), and standard `other` permissions.

## Exit Status

`0` on success, `1` if the file doesn't exist or isn't accessible.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `getfacl: file: No such file or directory` | Typo or wrong path | Verify with `ls` |
| Output shows no extra entries despite `+` in `ls -l` | Rare inconsistency — usually a stale `ls` cache or a different file was checked | Re-run both commands against the confirmed same path |

## Security Considerations

`getfacl` output is the authoritative way to audit exactly who has access to a file beyond
standard permissions — should be part of any access review process on systems using ACLs.

## Performance Considerations

Negligible.

## Production Usage

Running `getfacl` as part of a periodic access audit (especially in regulated environments) is
the direct way to catch stale, forgotten ACL grants that `ls -l` alone would never reveal.

## Related Commands

- [`setfacl`](setfacl.md) — modify ACL entries

## Related Concepts

- [ACLs: setfacl, getfacl](../docs/07-permissions/acls.md)
