---
title: "Debugging Scripts: set -x, shellcheck"
description: "Tracing exactly what a script executes with set -x, and catching common mistakes before running it at all with shellcheck."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/exit-codes-and-error-handling"]
relatedTopics: ["exit-codes-and-error-handling", "real-automation-scripts"]
relatedCommands: ["set", "shellcheck"]
careerRelevance: ["devops", "sre", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#debugging-bash-scripts"]
relatedCheatsheet: ""
furtherReading: [{"label": "ShellCheck wiki", "url": "https://www.shellcheck.net/wiki/"}]
nextTopic: "18-shell-scripting/real-automation-scripts"
prevTopic: "18-shell-scripting/exit-codes-and-error-handling"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["bash set -x debug", "shellcheck bash", "bash script debugging", "trace bash script execution"]
canonicalUrl: "/docs/shell-scripting/debugging-scripts-set-x-shellcheck"
---

# Debugging Scripts: set -x, shellcheck

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator · Software Engineering

> **TL;DR:** `set -x` (or `bash -x script.sh`) prints every command as it actually executes, with
> variables already expanded — invaluable when a script behaves unexpectedly. `shellcheck` finds
> common mistakes (unquoted variables, wrong test operators) before you even run the script.

## What is it?

Two complementary debugging approaches: `set -x` traces execution as it happens (runtime
debugging), while `shellcheck` statically analyzes a script for known mistake patterns before it
ever runs (static analysis).

## Why does it exist?

Shell scripts fail in ways that are often invisible from the script's source alone — a variable
expanding differently than expected, a condition evaluating unexpectedly. `set -x` makes execution
visible in real time. `shellcheck` catches an enormous number of common mistakes (many covered
earlier in this module — unquoted variables, `[ ]` pitfalls) automatically, before a bug ever
reaches production.

## Where is it used?

Any time a script doesn't behave as expected and the reason isn't obvious from reading the source
— `set -x` shows exactly what ran; `shellcheck` is commonly run in CI pipelines or as a pre-commit
check to catch mistakes before a script is even merged.

## How it works

> 📊 Diagram: two panels side by side — left panel: a terminal running `bash -x script.sh` with
> each line prefixed by `+`, showing variables already expanded to their actual values; right
> panel: `shellcheck script.sh` output showing a line number, an `SC####` code, and a plain-English
> explanation of the problem, with no script execution involved at all.

**`set -x`** — prints each command to stderr as it's about to run, with variables already
expanded, prefixed with `+`:

```bash
#!/bin/bash
set -x
name="production"
echo "Deploying to $name"
```

```
+ name=production
+ echo 'Deploying to production'
Deploying to production
```

Turn it off partway through a script with `set +x` if only part of it needs tracing. Or trace an
entire script without editing it: `bash -x script.sh`.

**`shellcheck`** — a static analysis tool that reads a script without running it and flags known
mistake patterns:

```
$ shellcheck deploy.sh

In deploy.sh line 4:
cp $file /backup/
   ^-- SC2086 (info): Double quote to prevent globbing and word splitting.
```

Each finding includes an `SC####` code linking to a detailed explanation
([shellcheck.net/wiki](https://www.shellcheck.net/wiki/)) — including exactly the unquoted
variable problem from [Variables and Data Types](variables-and-data-types.md).

## Real-world example

A script works fine in every manual test an engineer runs, but fails intermittently in production
with a cryptic error. Adding `set -x` and re-running against the same input that failed shows the
exact expanded command that ran — revealing that a variable expected to hold a single filename
actually contained two filenames separated by a space (from unquoted word-splitting), something
invisible just from reading the script's source. `shellcheck` run against the same script would
have flagged the unquoted variable as `SC2086` before the bug ever shipped.

## Syntax

```bash
set -x    # start tracing
set +x    # stop tracing
```
```
shellcheck SCRIPT.sh
```

## Commands

See [`set`](../../commands/set.md) and [`shellcheck`](../../commands/shellcheck.md) for full
references.

## Production example

```
$ shellcheck deploy.sh

In deploy.sh line 6:
if [ $count > 5 ]; then
   ^-- SC2071 (error): > is for string comparisons. Use -gt instead.

$ bash -x deploy.sh
+ count=10
+ '[' 10 '>' 5 ']'
+ echo 'small'
small
```

`shellcheck` catches the wrong comparison operator (`>` instead of `-gt` for numeric comparison,
covered in [Conditionals](conditionals-if-else-case.md)) before running anything; `set -x`
independently shows the actual (wrong) comparison being evaluated at runtime.

## Do / Don't

| Do | Don't |
|---|---|
| Run `shellcheck` on every script before considering it done | Only discover mistakes shellcheck would have caught after a production failure |
| Use `set -x` (or `bash -x`) when a script's behavior doesn't match its source | Add print-statement debugging by hand when `set -x` already shows exact execution |
| Turn off `set -x` (`set +x`) once done tracing | Leave verbose tracing on permanently in production, cluttering logs |

## Common mistakes

- Never running `shellcheck` on scripts before deploying them, missing an entire class of known,
  automatically-detectable mistakes.
- Manually adding `echo` statements everywhere to debug instead of using `set -x`, which shows
  exact expanded commands with far less manual effort.
- Leaving `set -x` enabled in a production script permanently, generating excessive log noise
  instead of enabling it only when actively debugging.

## Best practices

- Run `shellcheck` as a standard step before considering any script finished — ideally wired into
  CI so it's automatic, not a manual habit that gets skipped under time pressure.
- Reach for `set -x` (or `bash -x script.sh` without modifying the file) as the first debugging
  step whenever a script's behavior is confusing — it's almost always faster than manual
  inspection.
- Treat `shellcheck` findings as a checklist to work through, not noise to ignore — most flagged
  issues correspond directly to real bug classes covered in this module (unquoted variables, wrong
  test operators).

## Exercises

1. Run `shellcheck` against a script you've written earlier in this module and address any
   findings.
2. Add `set -x` to a script and observe how variable expansion is shown in the traced output.
3. Deliberately introduce an unquoted variable into a test script and confirm `shellcheck` flags
   it.

## Quiz

**Q: What's the key difference between set -x and shellcheck?**
<details><summary>Show answer</summary>
`set -x` traces a script's actual execution in real time, showing expanded commands as they run;
`shellcheck` statically analyzes the script's source for known mistake patterns without running it
at all.
</details>

**Q: What does an SC#### code in shellcheck output represent?**
<details><summary>Show answer</summary>
A specific, documented mistake pattern (like SC2086 for unquoted variables) — each code links to a
detailed explanation on the ShellCheck wiki.
</details>

**Q: How do you trace an entire script's execution without editing the script file itself?**
<details><summary>Show answer</summary>
Run it as `bash -x script.sh` — this enables tracing for that run without needing `set -x` inside
the script's source.
</details>

## Interview questions

- How would you debug a Bash script that behaves unexpectedly in production? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `set -x` (or `bash -x script.sh`) traces real execution with variables already expanded.
- `shellcheck` statically catches common mistakes — many covered earlier in this module — before a
  script ever runs.
- Use both together: `shellcheck` before running, `set -x` when runtime behavior still doesn't
  match expectations.
- Wiring `shellcheck` into CI catches an entire class of bugs automatically, before they ever
  reach production.

## Related topics

- [Exit Codes and Error Handling](exit-codes-and-error-handling.md)
- [Real Automation Scripts](real-automation-scripts.md)
