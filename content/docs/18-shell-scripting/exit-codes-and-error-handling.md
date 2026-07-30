---
title: "Exit Codes and Error Handling"
description: "Every command has an exit status — 0 for success, non-zero for failure. Checking $?, using set -e, and trapping errors are how scripts respond to failure instead of ignoring it."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/script-arguments"]
relatedTopics: ["script-arguments", "debugging-scripts-set-x-shellcheck"]
relatedCommands: ["set"]
careerRelevance: ["devops", "sre", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#set-e-pitfalls"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/debugging-scripts-set-x-shellcheck"
prevTopic: "18-shell-scripting/script-arguments"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["bash exit code", "bash dollar question mark", "set -e bash", "bash trap error handling"]
canonicalUrl: "/docs/shell-scripting/exit-codes-and-error-handling"
---

# Exit Codes and Error Handling

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Software Engineering

> **TL;DR:** Every command exits with a status: `0` for success, `1-255` for failure. Check it
> with `$?`, exit your own script deliberately with `exit N`, and consider `set -e` to stop a
> script the instant any command fails — with its well-known caveats.

## What is it?

The mechanism every Linux command uses to report success or failure, and the script-level
practices (`$?`, `exit`, `set -e`, `trap`) for reacting to that signal instead of silently
continuing after a failure.

## Why does it exist?

A script that keeps running commands after an earlier one has already failed can cause real
damage — deploying to a server that a previous step failed to provision, or deleting files after
a backup step silently failed. Exit codes are how a command signals success or failure at all;
error handling is the discipline of actually checking that signal instead of ignoring it.

## Where is it used?

Every production script, but especially deploy scripts, backup scripts, and anything destructive
or hard to undo — exactly the [real automation scripts](real-automation-scripts.md) this module
builds toward, where an unnoticed failure partway through has real consequences.

## How it works

> 📊 Diagram: a sequence of three command boxes in a script, each with an exit-status output
> (0, 0, 1) flowing into a fourth box labeled "next command" — with a highlighted branch point
> showing what happens differently with and without `set -e`: without it, the script continues
> past the failed command; with it, the script stops immediately.

**Checking `$?` after any command:**

```bash
cp important.txt /backup/
if [[ $? -ne 0 ]]; then
    echo "Backup copy failed"
    exit 1
fi
```

More idiomatically, check the command directly in the condition rather than via `$?` separately:

```bash
if ! cp important.txt /backup/; then
    echo "Backup copy failed"
    exit 1
fi
```

**`exit N`** — ends the script immediately with exit status `N` (convention: `0` success, `1`
general error, other codes for specific meanings your script defines). Whatever called this
script — a human, `cron` ([Module 17](../17-cron/index.md)), a CI pipeline — can check that code.

**`set -e`** — makes the *entire script* exit immediately the moment any command fails (returns
non-zero), instead of continuing to the next line:

```bash
#!/bin/bash
set -e

cp important.txt /backup/    # if this fails, the script stops here automatically
echo "Backup complete"        # never reached if the cp above failed
```

**`set -e`'s well-known caveat:** it does *not* trigger inside conditions being tested (`if
some_command; then` — a failing `some_command` there is expected, not an error) or inside a
pipeline's non-final commands by default (fixed by also adding `set -o pipefail`, covered on the
[next page](debugging-scripts-set-x-shellcheck.md)). Understanding this caveat is what separates
using `set -e` correctly from being surprised when it doesn't catch a failure you expected it to.

**`trap`** — run a command when the script exits (for cleanup), regardless of whether it
succeeded or failed:

```bash
trap 'rm -f /tmp/mylock' EXIT
```

## Real-world example

A backup script runs `tar` to archive a directory, then `rm` to clean up the source afterward,
with no error checking between the two steps. When the disk fills up mid-`tar` and the archive
comes out truncated/incomplete, the script doesn't notice — it proceeds straight to `rm`, deleting
the only complete copy of the data and leaving behind only the broken archive. Checking `tar`'s
exit status (or using `set -e`) before running the destructive `rm` step would have stopped the
script the moment the archive step failed, preventing data loss.

## Syntax

```bash
command
if [[ $? -ne 0 ]]; then ... fi
exit N
set -e
trap 'cleanup_command' EXIT
```

## Commands

See [`set`](../../commands/set.md) for the full reference on `set -e`, `-u`, `-x`, and
`-o pipefail`.

## Production example

```
$ cat safe-backup.sh
#!/bin/bash
set -e

echo "Archiving..."
tar -czf /backup/data.tar.gz /data

echo "Cleaning up old backups..."
find /backup -name "*.tar.gz" -mtime +30 -delete

echo "Backup complete"

$ ./safe-backup.sh
Archiving...
Cleaning up old backups...
Backup complete
```

With `set -e`, if `tar` had failed (bad path, full disk), the script would have stopped
immediately, before ever reaching the cleanup step — never risking deleting old backups when a
new one hadn't actually succeeded.

## Do / Don't

| Do | Don't |
|---|---|
| Check exit status before a destructive step depends on a prior step succeeding | Chain destructive commands with no error checking in between |
| Use `set -e` (with awareness of its caveats) in production scripts | Assume `set -e` alone catches every possible failure mode, including inside pipelines |
| Use `exit N` with meaningful codes so callers can distinguish failure types | Let a script exit with whatever code the last command happened to produce, unexamined |

## Common mistakes

- Chaining a destructive command (`rm`, `mv`, overwrite) directly after another command with no
  check that the first one actually succeeded.
- Assuming `set -e` catches every failure, without knowing it doesn't trigger inside `if`
  conditions or non-final pipeline commands (fixed by `set -o pipefail`).
- Not using `exit N` with a meaningful, documented code — a caller (a human, `cron`, a CI system)
  can't distinguish "failed because X" from "failed because Y" if every failure just exits `1`.

## Best practices

- Start production scripts with `set -e` (and `set -o pipefail`, covered next) as a baseline safety
  net, understanding exactly what it does and doesn't catch.
- Check the exit status explicitly before any step that depends on a previous one succeeding,
  especially before destructive operations.
- Use `trap ... EXIT` for cleanup that must always run (removing a lock file, a temp directory)
  regardless of whether the script succeeded or failed partway through.

## Exercises

1. Write a script with `set -e` that stops immediately after a deliberately-failing command
   (e.g. `ls /nonexistent`), and confirm the following line never runs.
2. Demonstrate a case where `set -e` does *not* stop the script (a failing command inside an `if`
   condition), and explain why.
3. Add a `trap` to a script that always prints "Cleaning up..." on exit, whether it succeeded or
   failed.

## Quiz

**Q: What exit status does a successful command return, by convention?**
<details><summary>Show answer</summary>
`0` — any non-zero value (typically `1`-`255`) signals failure, with the specific non-zero value
often carrying additional meaning defined by that command or script.
</details>

**Q: Does set -e catch a failing command inside an if condition?**
<details><summary>Show answer</summary>
No — a command being tested in an `if` (or `while`/`until`) condition is expected to potentially
fail, so `set -e` deliberately doesn't trigger there.
</details>

**Q: What does trap 'command' EXIT do?**
<details><summary>Show answer</summary>
Runs `command` whenever the script exits, whether it succeeded or failed — commonly used for
cleanup like removing a temporary lock file.
</details>

## Interview questions

- What are the caveats of using `set -e` in a production script? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Every command reports success (`0`) or failure (non-zero) via its exit status, checkable via
  `$?` or directly in a condition.
- `set -e` stops a script immediately on any failing command, with important caveats around `if`
  conditions and pipelines.
- `exit N` with a meaningful code lets callers (humans, cron, CI) distinguish failure types.
- `trap ... EXIT` guarantees cleanup logic runs regardless of how the script exits.

## Related topics

- [Script Arguments](script-arguments.md)
- [Debugging Scripts: set -x, shellcheck](debugging-scripts-set-x-shellcheck.md)
