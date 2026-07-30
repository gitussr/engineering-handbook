---
title: "I/O Redirection"
description: "Sending a command's output to a file instead of the screen, or feeding a file in as input — the >, >>, <, and 2> operators."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/path-variable"]
relatedTopics: ["pipes"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#redirection-operators"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/pipes"
prevTopic: "04-bash/path-variable"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["bash redirection", "stdout stderr stdin", "append vs overwrite redirect linux"]
canonicalUrl: "/docs/bash/io-redirection"
---

# I/O Redirection

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · SRE

> **TL;DR:** `>` overwrites a file with a command's output, `>>` appends to it, `<` feeds a file
> in as input, and `2>` redirects error output specifically. Mixing up `>` and `>>` is the classic
> mistake — one destroys existing file content, the other doesn't.

## What is it?

I/O redirection changes where a command's input comes from or where its output goes, instead of
the default (input from the keyboard, output to the screen). Every Linux process has three
standard streams: stdin (input), stdout (normal output), and stderr (error output) — redirection
operators control where each one is connected.

## Why does it exist?

Commands need to interact with files, logs, and each other, not just a human at a keyboard.
Redirection is the mechanism that lets a command's output become a saved file, a log, or another
command's input, without the command itself needing to know anything about files at all.

## Where is it used?

- Saving command output to a log file
- Appending to an existing log without erasing history
- Separating normal output from error output when debugging
- Feeding a file's contents into a command that expects input from stdin

## How it works

> 📊 Diagram: a process box with three labeled arrows — "stdin ←" (input coming in), "stdout →"
> (normal output going out), "stderr →" (error output going out, drawn separately from stdout) —
> with each redirection operator labeled next to the stream it controls.

| Operator | Effect |
|---|---|
| `>` | Redirect stdout to a file, **overwriting** it |
| `>>` | Redirect stdout to a file, **appending** to it |
| `<` | Redirect a file's contents in as stdin |
| `2>` | Redirect stderr specifically (file descriptor 2) |
| `2>&1` | Redirect stderr to wherever stdout is currently going |
| `&>` | Redirect both stdout and stderr to the same place |

stdout and stderr are separate streams even though both normally display on your screen — that
separation is exactly what lets you redirect one without the other.

## Real-world example

A deployment script redirects its normal output to a log file (`>> deploy.log`) so there's a
persistent record, while letting errors still print to the screen unredirected — so a human
watching the deployment sees failures immediately, while the full run history is preserved for
later review. Getting this split right (stdout to file, stderr to screen, or vice versa depending
on the need) is a routine but important scripting decision.

## Commands

No new canonical command page on this page — redirection is shell syntax, not a command itself;
it's demonstrated here using [`ls`](../../commands/ls.md), already covered in Module 03.

## Production example

```
$ ls /var/log > files.txt        # overwrite files.txt with the listing
$ ls /var/log >> files.txt       # append another listing to the same file
$ ls /nonexistent 2> errors.txt  # save only the error message
$ ls /var/log > out.txt 2>&1     # both stdout and stderr into the same file
```

## Do / Don't

| Do | Don't |
|---|---|
| Use `>>` when you want to preserve existing file content | Use `>` on a log file you meant to append to |
| Redirect stderr separately (`2>`) when debugging | Assume error messages are captured by a plain `>` redirect |
| Double-check the target filename before using `>` | Redirect output to a file you haven't verified is safe to overwrite |

## Common mistakes

- Using `>` when `>>` was intended, silently destroying an existing file's contents — this is the
  single most consequential redirection mistake, especially against a log file.
- Assuming `> file.txt` captures error messages too — it only captures stdout; errors need `2>`
  or `2>&1` explicitly.
- Redirecting into a file that's also being read by the same command, causing unpredictable
  results.

## Best practices

- Default to `>>` for logs specifically because overwriting a log is rarely what you want.
- When debugging a script, redirect stdout and stderr separately at first (`> out.txt 2>
  err.txt`) so you can see exactly which stream a message came from.
- Pause before any `>` redirect targeting an existing, important file — it's silent and
  irreversible.

## Exercises

1. Run a command with `>` into a new file, then run it again with `>` into the same file and
   observe the content was replaced, not appended.
2. Repeat using `>>` and observe the difference.
3. Run a command that will fail (e.g. `ls /nonexistent`) and redirect only its error output to a
   file using `2>`.

## Quiz

**Q: What's the difference between `>` and `>>`?**
<details><summary>Show answer</summary>
`>` overwrites the target file's existing content; `>>` appends to it without erasing what's
already there.
</details>

**Q: Does `command > output.txt` capture error messages too?**
<details><summary>Show answer</summary>
No — it only captures stdout. Error messages go to stderr and need `2>` or `2>&1` to be
redirected as well.
</details>

## Interview questions

- What's the difference between stdout and stderr, and how do you redirect each separately? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `>` overwrites, `>>` appends — mixing these up is the classic, costly redirection mistake.
- stdout and stderr are separate streams; `2>` redirects errors specifically.
- `2>&1` merges stderr into wherever stdout is currently going.
- Redirection is what lets commands interact with files and logs without any file-handling logic
  of their own.

## Related topics

- [Pipes](pipes.md)
