---
title: "pwd, ls, cd"
description: "The three commands you'll run more than any other in Linux — print your location, list a directory, and change directory."
module: "03-terminal"
moduleTitle: "Terminal"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["03-terminal/navigating-the-terminal"]
relatedTopics: ["command-structure-and-syntax"]
relatedCommands: ["pwd", "ls", "cd"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#pwd-ls-cd"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "03-terminal/command-structure-and-syntax"
prevTopic: "03-terminal/navigating-the-terminal"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["pwd command", "ls command", "cd command", "linux navigation commands"]
canonicalUrl: "/docs/terminal/pwd-ls-cd"
---

# pwd, ls, cd

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `pwd` tells you where you are, `ls` tells you what's there, `cd` moves you somewhere
> else. These three commands, in that order, are the first thing to reach for in any unfamiliar
> terminal session.

## What is it?

The three foundational navigation commands introduced conceptually in
[Navigating the Terminal](navigating-the-terminal.md): `pwd` (print working directory), `ls`
(list directory contents), and `cd` (change directory). This page is the concept-level overview;
full syntax, options, and examples for each live on their own canonical command pages.

## Why does it exist?

You cannot do anything else in a terminal — read a file, run a script, check a log — without
first being able to answer "where am I" and "what's here" and "how do I get somewhere else."
These three commands are the prerequisite for every other command in this documentation.

## Where is it used?

Literally every terminal session. There is no Linux professional, at any level, who doesn't run
these dozens of times a day.

## How it works

> 📊 Diagram: a simple three-step loop — pwd (where am I?) → ls (what's here?) → cd (go
> somewhere else) → back to pwd — illustrating the natural rhythm of terminal navigation.

Each command answers one specific question:

| Command | Question it answers |
|---|---|
| `pwd` | "Where am I right now?" |
| `ls` | "What's in this directory?" |
| `cd` | "How do I get somewhere else?" |

They're almost always used together in a loop: check where you are, see what's around you, move,
repeat.

## Real-world example

An engineer SSHs into an unfamiliar server for the first time to investigate an issue. Before
running anything else, the instinctive first three commands are `pwd` (confirm starting point,
usually the home directory), `ls` (see what's immediately available), and `cd /var/log` (move
toward where the actual investigation will happen). This isn't a beginner habit that gets dropped
later — senior engineers do exactly this on every new server.

## Commands

- [`pwd`](../../commands/pwd.md) — full syntax and examples
- [`ls`](../../commands/ls.md) — full syntax, options (`-l`, `-a`, `-h`), and examples
- [`cd`](../../commands/cd.md) — full syntax and examples, including `cd ~`, `cd -`, `cd ..`

## Production example

```
$ pwd
/home/deploy
$ ls
app  logs  scripts
$ cd logs
$ ls -la
total 24
drwxr-xr-x 2 deploy deploy 4096 Jul 25 09:12 .
drwxr-xr-x 5 deploy deploy 4096 Jul 25 09:10 ..
-rw-r--r-- 1 deploy deploy 1820 Jul 25 09:12 app.log
```

`ls -la` (list all, long format) is the version experienced engineers reach for by default — it
shows hidden files and permissions in one command, not just names.

## Do / Don't

| Do | Don't |
|---|---|
| Default to `ls -la` for real inspection, not bare `ls` | Rely on bare `ls` when permissions/hidden files matter |
| Run `pwd` first on an unfamiliar system | Assume you know your location without checking |
| Use `cd -` to jump back to your previous directory | Retype a long path you were just in |

## Common mistakes

- Using bare `ls` and missing hidden files (anything starting with `.`, like `.bashrc`) — they
  only show with `-a`.
- Forgetting `cd -` exists — it jumps back to your previous directory, faster than retyping a
  path you just left.
- Running `cd` into a directory that doesn't exist and misreading the error as something more
  serious than a typo.

## Best practices

- Make `ls -la` your default inspection habit, not just `ls`.
- Use `cd ~` to get home instantly from anywhere, and `cd -` to toggle back to where you just
  were.
- Chain the pwd → ls → cd loop deliberately when landing on any unfamiliar system.

## Exercises

1. Run `pwd`, then `ls -la` in your home directory, and identify at least one hidden file.
2. Use `cd` to move into a subdirectory, then use `cd -` to jump back.
3. Use `cd ..` twice in a row and predict where you'll land before checking with `pwd`.

## Quiz

**Q: What does `cd -` do?**
<details><summary>Show answer</summary>
Changes to the previous directory you were in — a fast way to toggle between two locations.
</details>

**Q: Why does `ls` alone sometimes seem to "miss" files that are actually there?**
<details><summary>Show answer</summary>
Bare `ls` hides files starting with a dot (hidden/dotfiles) by default — use `ls -a` or `ls -la`
to see them.
</details>

## Interview questions

- What's the difference between `ls -l` and `ls -la`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `pwd` = where am I, `ls` = what's here, `cd` = go somewhere else — the three most-run commands
  in Linux.
- `ls -la` is the professional default, not bare `ls`.
- `cd -` and `cd ~` are the two shortcuts worth memorizing immediately.
- This three-command loop is the first move on any unfamiliar system, at any experience level.

## Related topics

- [Navigating the Terminal](navigating-the-terminal.md)
- [Command Structure and Syntax](command-structure-and-syntax.md)
