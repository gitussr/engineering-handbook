---
title: "grep — Search Text by Pattern"
description: "Search files or stdin for lines matching a pattern, with case-insensitive, recursive, inverted, and counting modes."
relatedConcepts: ["06-files/grep", "06-files/regular-expressions-basics"]
relatedCommands: []
careerRelevance: ["devops", "sre", "cybersecurity", "linux-administrator"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["grep command linux", "grep recursive search", "grep case insensitive", "grep invert match"]
canonicalUrl: "/commands/grep"
---

# grep

🟢 Must Know · Relevant for: DevOps · SRE · Cybersecurity · Linux Administrator

> **TL;DR:** `grep pattern file` prints matching lines. `-i` case-insensitive, `-r` recursive,
> `-v` invert, `-c` count, `-n` show line numbers.

## Purpose

`grep` searches input (a file, multiple files, or stdin) for lines matching a pattern and prints
those lines — see [grep](../docs/06-files/grep.md) for full conceptual coverage.

## Syntax

```
grep [OPTIONS] PATTERN [FILE...]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATTERN` | The text or regex pattern to search for | Yes |
| `FILE` | File(s) to search | No (reads stdin if omitted) |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-i` | `--ignore-case` | Case-insensitive matching |
| `-r`, `-R` | `--recursive` | Search directories recursively |
| `-v` | `--invert-match` | Show non-matching lines |
| `-c` | `--count` | Print only a count of matching lines |
| `-n` | `--line-number` | Prefix matches with line numbers |
| `-l` | `--files-with-matches` | Print only filenames containing a match |
| `-E` | `--extended-regexp` | Enable extended regex syntax |
| `-w` | `--word-regexp` | Match whole words only |

## Examples

```
$ grep "error" app.log
```
Basic search.

```
$ grep -ri "error" /var/log/
```
Case-insensitive, recursive search across a directory.

```
$ grep -c "404" access.log
```
Count matching lines.

```
$ ps aux | grep nginx
```
Filter another command's output.

## Expected Output

```
$ grep -n "error" app.log
42:2026-07-25 09:12:03 error: connection timeout
118:2026-07-25 09:14:51 error: connection timeout
```

## Exit Status

`0` if at least one match was found, `1` if no matches, `2` if an error occurred (e.g. file not
found).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| No output, but you expected matches | Case mismatch, or pattern needs `-E` for the syntax used | Add `-i`; check whether the pattern needs extended regex |
| `grep: file: No such file or directory` | Typo or wrong path | Verify the path |
| Matching lines from every subdirectory unexpectedly | `-r` used when a single file was intended | Remove `-r` or scope the search path more narrowly |

## Security Considerations

`grep`'s exit status (`0` = found, `1` = not found) is commonly used in scripts to branch logic
(e.g. `if grep -q pattern file; then ...`) — a legitimate, common pattern worth knowing, not a
vulnerability itself.

## Performance Considerations

`grep -r` across a very large directory tree can be slow; combining with `find` (to scope files
first) or tools like `ripgrep` (a faster third-party alternative, not covered in this core
documentation) is common for very large codebases.

## Production Usage

`grep -c "pattern" logfile` is a fast, common first step during an incident — getting a rough
sense of how frequently something is occurring before diving into the actual matching lines.

## Related Commands

None documented yet — `sed`, `awk`, `cut`, `sort`, `uniq`, `wc` are covered in their own topic
pages but don't have separate canonical command pages yet.

## Related Concepts

- [grep](../docs/06-files/grep.md)
- [Regular Expressions Basics](../docs/06-files/regular-expressions-basics.md)
