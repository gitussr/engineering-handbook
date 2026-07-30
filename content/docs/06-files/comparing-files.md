---
title: "Comparing Files"
description: "diff and cmp — pinpointing exactly what changed between two files, line by line or byte by byte."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["06-files/compression-and-archiving"]
relatedTopics: ["grep"]
relatedCommands: []
careerRelevance: ["devops", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#diff-vs-cmp"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "06-files/grep"
prevTopic: "06-files/compression-and-archiving"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["diff command linux", "cmp command linux", "diff -u unified format"]
canonicalUrl: "/docs/files/comparing-files"
---

# Comparing Files

🟡 Good to Know · Relevant for: DevOps · Backend · Software Engineering

> **TL;DR:** `diff file1 file2` shows line-by-line differences — the tool behind every code diff
> you've ever seen. `cmp file1 file2` just confirms whether two files are byte-identical or not,
> without showing what differs.

## What is it?

Two tools for comparing files: `diff` shows exactly which lines differ between two text files
(the mechanism underlying version control diffs and patches), while `cmp` does a simpler
byte-by-byte comparison, mainly useful for confirming whether two files are identical at all.

## Why does it exist?

Confirming a config file matches what's expected, or seeing exactly what changed between two
versions of a file, is a routine and important task — `diff` and `cmp` answer this without
needing to eyeball two files side by side.

## Where is it used?

- Comparing a local config against a backup or a known-good version before deploying
- Verifying two files are identical (checksums are another approach; `cmp` is the direct
  comparison)
- Understanding `diff`'s unified format, since it's the same format used by `git diff` and patch
  files

## How it works

> 📊 Diagram: two file columns with `diff`'s unified output format annotated — lines prefixed
> `-` (removed, only in file1), `+` (added, only in file2), and unprefixed context lines shown
> unchanged — mapping directly to what a `git diff` looks like.

```
$ diff -u old.conf new.conf
--- old.conf
+++ new.conf
@@ -1,3 +1,3 @@
 server {
-    listen 80;
+    listen 443 ssl;
 }
```

`-u` (unified format) is the most commonly used mode — `-` marks removed lines, `+` marks added
lines, unmarked lines are unchanged context. `cmp` is much simpler: it just reports the first
byte position where two files differ, or nothing if they're identical.

## Real-world example

Before restarting a service after a config change, an engineer runs `diff` between the new config
and a saved backup of the previous working version, confirming exactly what changed and that no
unintended edits crept in — a quick, low-effort check that catches a real class of "I thought I
only changed one thing" mistakes before they cause an outage.

## Commands

No new canonical command pages on this page — `diff` and `cmp` are taught in full here as a
documented gap, consistent with this module's other lighter-weight commands.

## Production example

```
$ diff -u /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
$ cmp file1.bin file2.bin
file1.bin file2.bin differ: byte 42, line 3
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `diff -u` before deploying a config change, against a known-good backup | Deploy a config edit without confirming exactly what changed |
| Use `cmp` for a quick identical-or-not check | Use `cmp` when you actually need to see what differs (use `diff` instead) |

## Common mistakes

- Reaching for `cmp` when the actual need is to see *what* changed, not just *whether* something
  changed — `cmp` doesn't show line content, only a byte offset.
- Not using `-u` (unified format) with `diff`, making output harder to read than the format most
  engineers are already used to from `git diff`.
- Skipping a `diff` check before a config deploy and being surprised by unintended changes that
  crept in.

## Best practices

- Default to `diff -u` for readable, git-diff-style output.
- Use `diff` as a routine pre-deploy sanity check against a known-good backup, not just when
  something's already suspected to be wrong.

## Exercises

1. Create two slightly different versions of a text file and compare them with `diff -u`.
2. Compare the same two files with `cmp` and note how the output differs from `diff`.
3. Explain in one sentence when you'd reach for `cmp` instead of `diff`.

## Quiz

**Q: What does `cmp` tell you that `diff` doesn't, and vice versa?**
<details><summary>Show answer</summary>
`cmp` only reports whether files are identical (and the first byte where they differ); `diff`
shows the actual line-by-line content differences. `diff` is far more commonly useful for text
files.
</details>

**Q: What does a `+` prefix mean in `diff -u` output?**
<details><summary>Show answer</summary>
The line was added — present in the second file but not the first.
</details>

## Interview questions

- What's the difference between `diff` and `cmp`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `diff` shows line-by-line content differences; `cmp` only confirms identical-or-not.
- `diff -u` (unified format) matches the format used by `git diff` and patch files.
- A pre-deploy `diff` against a known-good backup is a cheap, effective sanity check.

## Related topics

- [grep](grep.md)
