---
title: "Files"
description: "Module 06 of the Linux roadmap — creating, viewing, editing, finding, archiving, comparing, and processing files. The core day-to-day toolkit."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
type: "module-index"
nextTopic: "06-files/file-types-in-linux"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/files"
---

# Files

Module 06 of 34 · Stage: Beginner · Previous: [05 File System](../05-file-system/index.md)

This is the largest command-heavy module so far. Module 05 explained where files live and how
the filesystem is structured; this module is the actual day-to-day toolkit for working with
files — creating, viewing, editing, finding, archiving, comparing, and processing text. The
roadmap's "Text processing" entry is split here into three separate topics (`grep`, `sed`/`awk`,
and `cut`/`sort`/`uniq`/`wc`) since each carries a different difficulty label.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [File Types in Linux](file-types-in-linux.md) | 🟢 Must Know |
| [Creating, Copying, Moving, Removing Files](creating-copying-moving-removing-files.md) | 🟢 Must Know |
| [Viewing Files](viewing-files.md) | 🟢 Must Know |
| [Editing Files: nano, vim Basics](editing-files.md) | 🟢 Must Know |
| [Finding Files](finding-files.md) | 🟢 Must Know |
| [Compression and Archiving](compression-and-archiving.md) | 🟢 Must Know |
| [Comparing Files](comparing-files.md) | 🟡 Good to Know |
| [grep](grep.md) | 🟢 Must Know |
| [sed and awk](sed-and-awk.md) | 🟡 Good to Know |
| [cut, sort, uniq, wc](cut-sort-uniq-wc.md) | 🟢 Must Know |
| [Regular Expressions Basics](regular-expressions-basics.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Identify a file's type and know when that matters.
- Create, copy, move, and delete files and directories confidently and safely.
- View file contents the right way for the situation — full dump, paged, or just the head/tail.
- Make a quick edit to a remote config file without leaving the terminal.
- Find a file by name, content, or metadata without knowing exactly where it is.
- Archive and compress files, and know the difference between the two.
- Compare two files and pinpoint exactly what changed.
- Search text with `grep`, transform it with `sed`/`awk`, and use basic regular expressions.
- Chain `cut`, `sort`, `uniq`, and `wc` into small text-processing pipelines.

## Known, intentional gaps in this module

- Canonical command pages exist for `touch`, `cp`, `mv`, `rm`, `cat`, `find`, `tar`, and `grep` —
  the highest-frequency commands this module centers on. `less`, `more`, `head`, `tail`, `nano`,
  `vim`, `locate`, `which`, `whereis`, `gzip`, `zip`, `diff`, `cmp`, `sed`, `awk`, `cut`, `sort`,
  `uniq`, and `wc` are taught in full within their topic pages but don't yet have separate
  canonical `/commands/` pages — same forward-reference policy as prior modules, flagged here
  because the volume in this module is larger than usual.

**Previous module:** [05 File System](../05-file-system/index.md)
**Next module:** [07 Permissions →](../07-permissions/index.md)
