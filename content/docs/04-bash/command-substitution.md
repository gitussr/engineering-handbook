---
title: "Command Substitution"
description: "Using a command's output as part of another command with $(...) — and why it's preferred over the older backtick syntax."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/pipes"]
relatedTopics: ["job-control"]
relatedCommands: []
careerRelevance: ["devops", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#command-substitution"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/job-control"
prevTopic: "04-bash/pipes"
estimatedReadingTime: 5
updatedAt: "2026-07-25"
keywords: ["command substitution bash", "dollar parentheses syntax", "backticks vs dollar parens"]
canonicalUrl: "/docs/bash/command-substitution"
---

# Command Substitution

🟡 Good to Know · Relevant for: DevOps · Backend · Software Engineering

> **TL;DR:** `$(command)` runs a command and substitutes its output directly into another command
> or variable assignment. It's the modern syntax; backticks (`` `command` ``) do the same thing
> but don't nest cleanly — prefer `$()` in all new scripts.

## What is it?

Command substitution runs a command and replaces the substitution expression with that command's
output, so it can be used as part of another command, a variable assignment, or a string.

## Why does it exist?

Many tasks need a value that only exists at runtime — the current date, a file count, the output
of another command — rather than a value you can hardcode ahead of time. Command substitution
lets you compute that value inline, immediately, as part of the command you're building.

## Where is it used?

- Assigning a command's output to a variable (`count=$(ls | wc -l)`)
- Building a filename or message that includes dynamic data (`backup-$(date +%F).tar.gz`)
- Shell scripts that need to branch or act based on another command's output

## How it works

> 📊 Diagram: a single expression `$(date +%F)` shown expanding inline into a surrounding command
> — `echo "Backup: backup-$(date +%F).tar.gz"` — with an arrow showing the substitution happening
> before the outer command runs, resulting in the actual date appearing in the final string.

```
$(command)
```

The shell runs `command`, captures its stdout, strips a trailing newline, and substitutes that
text exactly where the `$(...)` expression appears — before the outer command even runs.

The older syntax, backticks (`` `command` ``), does the same thing but doesn't nest well (nesting
requires escaping the inner backticks) — `$(...)` nests cleanly (`$(command1 $(command2))`) and
is the preferred modern syntax in virtually all style guides.

## Real-world example

A backup script names its output file using the current date so each run produces a uniquely
named file instead of overwriting the previous backup: `tar -czf backup-$(date +%F).tar.gz
/data`. The date is computed fresh every time the script runs, with no hardcoding and no separate
step to compute it first.

## Commands

No new canonical command page on this page — `date` (used in the example) is a forward reference
to its own future canonical page.

## Production example

```
$ count=$(ls /var/log | wc -l)
$ echo "There are $count files in /var/log"
There are 42 files in /var/log
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `$(command)` for new scripts | Use backticks in new scripts — they nest poorly |
| Quote a substitution when the output might contain spaces: `"$(command)"` | Leave substitutions unquoted where word-splitting could break things |

## Common mistakes

- Using backticks out of habit in new scripts instead of `$()` — functionally similar for simple
  cases, but `$()` nests cleanly and is universally preferred in current style guides.
- Forgetting to quote a substitution whose output might contain spaces or special characters,
  leading to unexpected word-splitting (full detail on quoting in
  [Module 18: Shell Scripting](../18-shell-scripting/index.md)).
- Assuming a trailing newline is preserved — command substitution strips one trailing newline from
  the captured output.

## Best practices

- Default to `$(command)` over backticks in every new script.
- Quote substitutions (`"$(command)"`) unless you specifically want word-splitting behavior.
- Keep substituted commands simple and readable — nested, complex substitutions hurt readability
  even when they technically work.

## Exercises

1. Store the output of `whoami` in a variable using command substitution and print it.
2. Build a filename string that includes the current date using `$(date +%F)`.
3. Explain in one sentence why `$()` is preferred over backticks in modern scripts.

## Quiz

**Q: What does `$(command)` do?**
<details><summary>Show answer</summary>
Runs the command and substitutes its captured stdout output directly into the surrounding
command, string, or variable assignment.
</details>

**Q: Why is `$()` preferred over backticks?**
<details><summary>Show answer</summary>
`$()` nests cleanly for complex substitutions, while backticks require awkward escaping to nest —
`$()` is the modern, universally preferred syntax.
</details>

## Interview questions

- How would you store the output of a command in a variable? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `$(command)` substitutes a command's output inline — preferred over backticks.
- Substitution happens before the outer command runs.
- Quote substitutions when the output might contain spaces to avoid word-splitting issues.
- Backticks still work but don't nest cleanly — avoid them in new scripts.

## Related topics

- [Job Control](job-control.md)
- [Module 18: Shell Scripting](../18-shell-scripting/index.md)
