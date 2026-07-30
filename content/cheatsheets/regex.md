---
title: "Regex Cheat Sheet"
description: "A single-page quick reference for regular expression syntax as used by grep, sed, and awk on Linux — anchors, character classes, quantifiers, groups, and common patterns."
type: "cheatsheet"
module: "files"
updatedAt: "2026-07-30"
keywords: ["regex cheat sheet", "regular expressions cheat sheet linux", "grep regex cheat sheet", "regex syntax reference"]
canonicalUrl: "/cheatsheets/regex"
---

# Regex Cheat Sheet

The regex syntax used by `grep`, `sed`, and `awk` — the pattern language you'll reach for
constantly once you're past basic file searching.

## Anchors

| Pattern | Matches |
|---|---|
| `^` | Start of line |
| `$` | End of line |
| `\b` | A word boundary |

## Character classes

| Pattern | Matches |
|---|---|
| `.` | Any single character |
| `[abc]` | Any one of `a`, `b`, or `c` |
| `[^abc]` | Any character except `a`, `b`, or `c` |
| `[a-z]` | Any lowercase letter (range) |
| `[0-9]` | Any digit (range) |
| `\d` | A digit (GNU extension / ERE with `-P`) |
| `\w` | A word character (letters, digits, underscore) |
| `\s` | Whitespace |

## Quantifiers

| Pattern | Matches |
|---|---|
| `*` | Zero or more of the preceding element |
| `+` | One or more (requires extended regex, `grep -E`) |
| `?` | Zero or one (requires extended regex, `grep -E`) |
| `{n}` | Exactly n repetitions |
| `{n,m}` | Between n and m repetitions |

## Groups and alternation

| Pattern | Matches |
|---|---|
| `(abc)` | A capturing group (requires extended regex or escaped parens in basic regex) |
| `a\|b` | Either `a` or `b` (requires extended regex, `grep -E`) |

## Common patterns

| Pattern | Matches |
|---|---|
| `^#` | Lines starting with `#` (e.g. comments) |
| `^$` | Blank lines |
| `[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}` | A rough IPv4 address (basic regex) |
| `\.log$` | Lines/filenames ending in `.log` |
| `^ERROR\|^WARN` | Lines starting with `ERROR` or `WARN` (extended regex) |

## Basic vs extended regex

| Tool / flag | Regex flavor |
|---|---|
| `grep` (default) | Basic Regular Expressions (BRE) — `+`, `?`, `\|`, `()` need backslash-escaping |
| `grep -E` / `egrep` | Extended Regular Expressions (ERE) — no escaping needed for `+ ? \| ( )` |
| `sed` (default) | BRE, same escaping rules as plain `grep` |
| `awk` | ERE by default |

Full explanation → [Regular Expressions Basics](../docs/06-files/regular-expressions-basics.md)

## Related topics

- [Linux Commands Cheat Sheet](linux-commands.md)
- [grep](../docs/06-files/grep.md)
- [sed and awk](../docs/06-files/sed-and-awk.md)
- [Files Module](../docs/06-files/index.md)
- [Cheat Sheets Hub](index.md)
