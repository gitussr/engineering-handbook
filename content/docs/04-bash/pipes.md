---
title: "Pipes"
description: "Connecting one command's output directly into another command's input with | — the core of the Unix small-tools philosophy in practice."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/io-redirection"]
relatedTopics: ["command-substitution"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#pipes"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/command-substitution"
prevTopic: "04-bash/io-redirection"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["bash pipe operator", "chaining commands linux", "grep pipe example"]
canonicalUrl: "/docs/bash/pipes"
---

# Pipes

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · SRE

> **TL;DR:** `|` connects one command's stdout directly to the next command's stdin, with no
> intermediate file. This is the practical realization of the Unix philosophy from Module 01:
> small tools, composed together, instead of one giant command that does everything.

## What is it?

A pipe (`|`) takes the stdout of the command on its left and feeds it as stdin to the command on
its right. Multiple pipes can be chained together, building a pipeline where data flows through
several small commands in sequence.

## Why does it exist?

Rather than one command doing everything (find files, filter them, count them, sort the count),
Unix favors small, single-purpose tools connected together. Pipes are the connector — the reason
`grep`, `sort`, `wc`, and dozens of other small tools can each stay simple while still solving
complex problems in combination, exactly the principle introduced in
[Linux Philosophy and the FHS](../01-linux-basics/linux-philosophy-and-fhs.md).

## Where is it used?

Constantly — searching logs, counting results, filtering process lists, formatting output. Pipe
chains are one of the most common patterns in daily Linux command-line work, from a quick
one-off investigation to a line inside a production script.

## How it works

> 📊 Diagram: three boxes connected by arrows — "Command A (produces output)" → pipe → "Command B
> (filters/transforms)" → pipe → "Command C (produces final result)" — with a note that no
> intermediate file is ever created; data streams directly from one command's stdout to the
> next's stdin.

Unlike redirection (`>`), which connects a command to a *file*, a pipe connects one command
directly to *another command*. No file is created at any point — data streams through memory from
one process to the next.

```
command1 | command2 | command3
```

Each command in the chain does one job; the pipeline as a whole does something none of them could
do alone.

## Real-world example

An engineer needs to know how many Nginx error log lines mention "timeout" today. Rather than
writing a custom program, they chain three small tools: `cat error.log | grep timeout | wc -l` —
read the file, filter for the matching lines, count them. Each piece is simple and independently
understandable; together they answer the question in one line, typed in seconds.

## Commands

No new canonical command page on this page — pipes are shell syntax, not a command themselves.
The example below previews [`grep`](../../commands/grep.md) and `wc`, both fully covered in
[Module 06: Files](../06-files/index.md).

## Production example

```
$ cat /var/log/nginx/error.log | grep timeout | wc -l
17
```

Read the file, keep only lines containing "timeout," count the remaining lines — three tools,
one pipeline, zero temporary files.

## Do / Don't

| Do | Don't |
|---|---|
| Build a pipeline incrementally, checking output at each stage | Write a long pipe chain all at once and debug it blind |
| Use pipes to combine small, focused tools | Reach for one giant command when a pipeline would be clearer |
| Prefer `grep pattern file` over `cat file \| grep pattern` when there's only one file | Habitually pipe `cat` into everything out of habit rather than necessity |

## Common mistakes

- Writing a long pipe chain in one go and struggling to debug which stage is wrong — build and
  test incrementally instead.
- Using `cat file | grep pattern` when `grep pattern file` alone does the same thing more directly
  — a common (harmless but telling) habit; understanding when `cat` is actually needed vs. just a
  reflex is a sign of real fluency.
- Forgetting that each command in a pipeline runs as a separate process — a `cd` inside one stage
  of a pipeline doesn't affect the shell you're typing in.

## Best practices

- Build pipelines one stage at a time, checking intermediate output before adding the next `|`.
- Keep each stage doing one clear job — that's the entire point of the small-tools philosophy.
- Learn to recognize (and drop) unnecessary `cat` at the start of a pipeline once a command can
  read a file directly.

## Exercises

1. Chain `ls -la`, `grep`, and `wc -l` to count how many files in a directory contain a specific
   letter in their name.
2. Build a three-stage pipeline one stage at a time, checking output after each addition.
3. Explain in one sentence why pipes don't create temporary files.

## Quiz

**Q: What does a pipe connect, exactly?**
<details><summary>Show answer</summary>
The stdout of the command on the left to the stdin of the command on the right — no file is
created in between.
</details>

**Q: Why is `grep pattern file` often preferred over `cat file | grep pattern`?**
<details><summary>Show answer</summary>
`grep` can read a file directly without needing `cat` to feed it in — the pipe version works but
is an unnecessary extra step for a single file.
</details>

## Interview questions

- Explain what happens when you run `command1 | command2 | command3`. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Pipes connect one command's stdout directly to the next command's stdin — no intermediate file.
- This is the Unix small-tools philosophy in daily practice.
- Build and debug pipelines incrementally, one stage at a time.
- Redirection connects a command to a file; pipes connect a command to another command.

## Related topics

- [Command Substitution](command-substitution.md)
- [Module 06: Files](../06-files/index.md)
