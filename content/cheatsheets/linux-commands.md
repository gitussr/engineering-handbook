---
title: "Linux Commands Cheat Sheet"
description: "A single-page quick reference for the core Linux commands every user needs: navigation, viewing and finding files, text processing, compression, and file comparison."
type: "cheatsheet"
module: "terminal"
updatedAt: "2026-07-30"
keywords: ["linux commands cheat sheet", "linux command line cheat sheet", "basic linux commands", "linux commands pdf"]
canonicalUrl: "/cheatsheets/linux-commands"
---

# Linux Commands Cheat Sheet

The everyday commands you'll type more than anything else — navigating, viewing, creating, and
searching files. Full walkthroughs and examples live on the linked topic pages; this page is the
quick-lookup version.

## Navigation

| Command | Description |
|---|---|
| [`pwd`](../commands/pwd.md) | Print the current working directory |
| [`ls`](../commands/ls.md) | List directory contents (`-l` long format, `-a` show hidden, `-h` human-readable sizes) |
| [`cd`](../commands/cd.md) | Change directory (`cd -` returns to the previous directory, `cd ~` goes home) |

Full explanation → [pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md)

## Getting help

| Command | Description |
|---|---|
| [`man`](../commands/man.md) `{command}` | Full manual page for a command |
| `{command} --help` | Quick flag summary without leaving the terminal |
| `tldr {command}` | Community-maintained example-first cheat sheet (separate install) |

Full explanation → [man, help, tldr](../docs/03-terminal/man-help-tldr.md)

## Viewing files

| Command | Description |
|---|---|
| [`cat`](../commands/cat.md) `file` | Print an entire file to the terminal |
| `less file` | Page through a file (searchable, doesn't load the whole file into memory) |
| `head -n 20 file` | Show the first 20 lines |
| `tail -n 20 file` | Show the last 20 lines |
| `tail -f file` | Follow a file as it grows — the standard way to watch a live log |

Full explanation → [Viewing Files](../docs/06-files/viewing-files.md)

## Creating, copying, moving, removing

| Command | Description |
|---|---|
| `touch file` | Create an empty file, or update its modified timestamp if it exists |
| `mkdir dir` | Create a directory (`-p` creates parent directories as needed) |
| [`cp`](../commands/cp.md) `src dst` | Copy a file (`-r` for directories) |
| [`mv`](../commands/mv.md) `src dst` | Move or rename a file |
| [`rm`](../commands/rm.md) `file` | Delete a file (`-r` for directories, `-i` to confirm each deletion) |

Full explanation → [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)

## Finding files

| Command | Description |
|---|---|
| [`find`](../commands/find.md) `path -name "*.log"` | Search the live filesystem by name, type, size, or age |
| `locate {name}` | Search a prebuilt index — fast, but can miss very recent changes |
| `which {command}` | Show the full path of the executable that would run |
| `whereis {command}` | Show binary, source, and man page locations for a command |

Full explanation → [Finding Files](../docs/06-files/finding-files.md)

## Text processing

| Command | Description |
|---|---|
| [`grep`](../commands/grep.md) `"pattern" file` | Search for lines matching a pattern |
| `cut -d',' -f1 file` | Extract a column from delimited text |
| `sort file` | Sort lines (`-n` numeric, `-r` reverse, `-u` unique) |
| `uniq` | Remove adjacent duplicate lines (usually piped after `sort`) |
| `wc -l file` | Count lines (`-w` words, `-c` bytes) |
| `sed 's/old/new/g' file` | Stream-edit text with find-and-replace |
| `awk '{print $1}' file` | Extract and process columns of structured text |

Full explanation → [grep](../docs/06-files/grep.md) · [cut, sort, uniq, wc](../docs/06-files/cut-sort-uniq-wc.md) · [sed and awk](../docs/06-files/sed-and-awk.md)

## Compression and archiving

| Command | Description |
|---|---|
| [`tar`](../commands/tar.md) `-czvf archive.tar.gz dir/` | Create a compressed archive |
| `tar -xzvf archive.tar.gz` | Extract a compressed archive |
| `gzip file` / `gunzip file.gz` | Compress/decompress a single file |
| `zip -r archive.zip dir/` / `unzip archive.zip` | Create/extract a zip archive |

Full explanation → [Compression and Archiving](../docs/06-files/compression-and-archiving.md)

## Comparing files

| Command | Description |
|---|---|
| `diff file1 file2` | Show line-by-line differences between two files |
| `cmp file1 file2` | Report the first byte where two files differ |

Full explanation → [Comparing Files](../docs/06-files/comparing-files.md)

## Related topics

- [Regex Cheat Sheet](regex.md)
- [Permissions Cheat Sheet](permissions.md)
- [Terminal Module](../docs/03-terminal/index.md)
- [Files Module](../docs/06-files/index.md)
- [Cheat Sheets Hub](index.md)
