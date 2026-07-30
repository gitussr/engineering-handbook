---
title: "Permissions"
description: "Module 07 of the Linux roadmap — the owner/group/other model, chmod and chown, umask, SUID/SGID/sticky bit, ACLs, and diagnosing permission errors."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
type: "module-index"
nextTopic: "07-permissions/permission-model"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/permissions"
---

# Permissions

Module 07 of 34 · Stage: Beginner · Previous: [06 Files](../06-files/index.md)

Nearly every "permission denied" error a beginner hits traces back to this module. Permissions
are one of the highest-leverage topics in the entire roadmap — used constantly, misunderstood
often, and a frequent interview subject at every level from beginner to expert.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [The Permission Model: Owner, Group, Other](permission-model.md) | 🟢 Must Know |
| [rwx Explained](rwx-explained.md) | 🟢 Must Know |
| [chmod: Symbolic and Octal](chmod-symbolic-and-octal.md) | 🟢 Must Know |
| [chown and chgrp](chown-chgrp.md) | 🟢 Must Know |
| [umask](umask.md) | 🟡 Good to Know |
| [SUID, SGID, Sticky Bit](suid-sgid-sticky-bit.md) | 🔴 Expert |
| [ACLs: setfacl, getfacl](acls.md) | 🔴 Expert |
| [Common Permission Errors](common-permission-errors.md) | 🟢 Must Know |

## What you should be able to do after this module

- Explain the owner/group/other model and read any `rwx` permission string on sight.
- Use `chmod` in both symbolic and octal notation, confidently and correctly.
- Change ownership and group with `chown`/`chgrp`.
- Explain what `umask` controls and predict a new file's default permissions from it.
- Explain SUID, SGID, and the sticky bit, and recognize each in `ls -l` output.
- Know when a permission problem calls for an ACL instead of standard owner/group/other bits.
- Diagnose a "permission denied" error systematically instead of guessing.

## Known, intentional gaps in this module

- Canonical command pages exist for `chmod`, `chown`, `chgrp`, `umask`, `setfacl`, and `getfacl` —
  every command this module centers on gets full treatment, reflecting how central permissions
  are to daily Linux work.

**Previous module:** [06 Files](../06-files/index.md)
**Next module:** [08 Users →](../08-users/index.md)
