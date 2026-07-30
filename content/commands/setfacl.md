---
title: "setfacl — Set File Access Control Lists"
description: "Grant or revoke fine-grained permission entries for a specific user or group beyond standard owner/group/other permissions."
relatedConcepts: ["07-permissions/acls"]
relatedCommands: ["getfacl", "chmod"]
careerRelevance: ["linux-administrator", "cybersecurity", "sre"]
difficulty: "expert"
updatedAt: "2026-07-25"
keywords: ["setfacl example", "setfacl -m", "setfacl recursive", "acl grant user"]
canonicalUrl: "/commands/setfacl"
---

# setfacl

🔴 Expert · Relevant for: Linux Administrator · Cybersecurity · SRE

> **TL;DR:** `setfacl -m u:user:rwx file` grants a specific user permissions beyond the standard
> owner/group/other model. `-x` removes an entry instead of adding one.

## Purpose

`setfacl` modifies a file or directory's Access Control List — see
[ACLs: setfacl, getfacl](../docs/07-permissions/acls.md) for when to reach for this over standard
permissions.

## Syntax

```
setfacl [OPTIONS] -m ENTRY FILE...
setfacl [OPTIONS] -x ENTRY FILE...
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `ENTRY` | `u:user:perms` or `g:group:perms` | Yes |
| `FILE` | One or more files/directories | Yes |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-m` | `--modify` | Add or modify an ACL entry |
| `-x` | `--remove` | Remove a specific ACL entry |
| `-R` | `--recursive` | Apply recursively to a directory |
| `-b` | | Remove all extended ACL entries |

## Examples

```
$ setfacl -m u:auditor:r-- report.csv
```
Grant a specific user read-only access.

```
$ setfacl -m g:contractors:r-x /shared/project
```
Grant a specific group read+execute access.

```
$ setfacl -x u:auditor report.csv
```
Remove a previously granted entry.

## Expected Output

`setfacl` produces no output on success. Use `getfacl` to verify the result.

## Exit Status

`0` on success, non-zero if the filesystem doesn't support ACLs or the operation isn't permitted.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `setfacl: file: Operation not supported` | The filesystem wasn't mounted with ACL support | Remount with the `acl` option, or check filesystem type support |
| Entry doesn't seem to take effect | The ACL "mask" entry limits effective permissions below what was granted | Check `getfacl` output for the `mask::` line, which caps effective ACL permissions |

## Security Considerations

ACL grants are easy to leave in place after they're no longer needed — treat them with the same
access-review discipline as any other permission grant, and periodically audit with `getfacl`.

## Performance Considerations

Negligible for individual files; `-R` on a very large tree takes proportionally longer, same as
`chmod -R`.

## Production Usage

`setfacl` is the standard tool for granting narrow, auditable, temporary access exceptions in
regulated environments — e.g. an external auditor needing read-only access to specific files
without broader group membership changes.

## Related Commands

- [`getfacl`](getfacl.md) — view current ACL entries
- [`chmod`](chmod.md) — standard permission bits, not ACL entries

## Related Concepts

- [ACLs: setfacl, getfacl](../docs/07-permissions/acls.md)
