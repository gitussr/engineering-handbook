---
title: "Linux Permissions Cheat Sheet"
description: "A single-page quick reference for Linux file permissions: the rwx model, chmod symbolic and octal notation, chown/chgrp, umask, SUID/SGID/sticky bit, and ACLs."
type: "cheatsheet"
module: "permissions"
updatedAt: "2026-07-30"
keywords: ["linux permissions cheat sheet", "chmod cheat sheet", "linux file permissions cheat sheet", "chmod octal cheat sheet"]
canonicalUrl: "/cheatsheets/permissions"
---

# Linux Permissions Cheat Sheet

Every file has an owner, a group, and a permission set for each. This page is the quick-lookup
version of the full permission model — see the linked topic pages for the reasoning and common
mistakes.

## The permission model

| Symbol | Class | Controls |
|---|---|---|
| Owner (`u`) | The user who owns the file | Independent read/write/execute |
| Group (`g`) | The file's associated group | Independent read/write/execute |
| Other (`o`) | Everyone else | Independent read/write/execute |

`r` = read (4), `w` = write (2), `x` = execute (1) — octal notation sums these per class.

Full explanation → [The Permission Model](../docs/07-permissions/permission-model.md) · [rwx Explained](../docs/07-permissions/rwx-explained.md)

## chmod — change permissions

| Command | Description |
|---|---|
| [`chmod`](../commands/chmod.md) `755 file` | rwx for owner, r-x for group and other (typical for scripts/directories) |
| `chmod 644 file` | rw for owner, r-only for group and other (typical for data files) |
| `chmod u+x file` | Symbolic form: add execute for the owner only |
| `chmod g-w file` | Symbolic form: remove write from the group |
| `chmod -R 755 dir/` | Apply recursively to a directory tree |

Full explanation → [chmod (Symbolic and Octal)](../docs/07-permissions/chmod-symbolic-and-octal.md)

## chown / chgrp — change ownership

| Command | Description |
|---|---|
| [`chown`](../commands/chown.md) `user file` | Change a file's owner |
| `chown user:group file` | Change owner and group in one command |
| [`chgrp`](../commands/chgrp.md) `group file` | Change only a file's group |
| `chown -R user:group dir/` | Apply recursively to a directory tree |

Full explanation → [chown, chgrp](../docs/07-permissions/chown-chgrp.md)

## umask — default permission mask

| Command | Description |
|---|---|
| [`umask`](../commands/umask.md) | Show the current mask |
| `umask 022` | Common default: new files → `644`, new directories → `755` |
| `umask 077` | Restrictive default: only the owner gets any access to new files |

Full explanation → [umask](../docs/07-permissions/umask.md)

## SUID, SGID, and the sticky bit

| Bit | Octal prefix | Effect |
|---|---|---|
| SUID | `4000` | Executable runs with the file owner's privileges, not the invoking user's |
| SGID | `2000` | Executable runs with the file's group privileges; on a directory, new files inherit its group |
| Sticky bit | `1000` | On a shared directory (e.g. `/tmp`), only a file's owner can delete it |

Full explanation → [SUID, SGID, and the Sticky Bit](../docs/07-permissions/suid-sgid-sticky-bit.md)

## ACLs — beyond owner/group/other

| Command | Description |
|---|---|
| [`setfacl`](../commands/setfacl.md) `-m u:alice:rw file` | Grant a specific user permissions beyond the standard owner/group/other model |
| [`getfacl`](../commands/getfacl.md) `file` | Show all ACL entries on a file |

Full explanation → [ACLs (setfacl, getfacl)](../docs/07-permissions/acls.md)

## Related topics

- [Linux Commands Cheat Sheet](linux-commands.md)
- [SSH Cheat Sheet](ssh.md)
- [Permissions Module](../docs/07-permissions/index.md)
- [Common Permission Errors](../docs/07-permissions/common-permission-errors.md)
- [Cheat Sheets Hub](index.md)
