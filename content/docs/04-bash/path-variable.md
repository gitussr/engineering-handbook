---
title: "The PATH Variable"
description: "Why command not found almost always means the shell couldn't find the executable in PATH, and how to fix it."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/aliases-and-functions"]
relatedTopics: ["environment-variables"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#path-variable"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/io-redirection"
prevTopic: "04-bash/aliases-and-functions"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["path environment variable", "command not found linux", "which command"]
canonicalUrl: "/docs/bash/path-variable"
---

# The PATH Variable

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Software Engineering

> **TL;DR:** `PATH` is an ordered list of directories the shell searches for an executable when
> you type a command name. `bash: command not found` almost always means the program either isn't
> installed, or isn't in a directory listed in `PATH`.

## What is it?

`PATH` is a special environment variable (see [Environment Variables](environment-variables.md))
containing a colon-separated list of directories. When you type a command name, the shell
searches those directories in order for a matching executable.

## Why does it exist?

Without `PATH`, you'd have to type the full path to every executable, every time
(`/usr/bin/ls` instead of just `ls`). `PATH` lets the shell find commands by name alone, searching
a known, ordered set of locations automatically.

## Where is it used?

Every single command you type that isn't a shell built-in relies on `PATH` resolution. It's also
the mechanism behind installing new CLI tools — many installation guides' final step is "add this
directory to your `PATH`" specifically so the new tool can be run by name from anywhere.

## How it works

> 📊 Diagram: a horizontal sequence of directory boxes labeled `/usr/local/sbin`,
> `/usr/local/bin`, `/usr/sbin`, `/usr/bin` (a typical `PATH` order), with an arrow showing the
> shell checking each in order until it finds a matching executable, stopping at the first match.

```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

The shell searches left to right and uses the **first** match — this is why directory order in
`PATH` matters: if two directories both contain a program with the same name, whichever comes
first in `PATH` wins.

## Real-world example

An engineer installs a newer version of a tool into `/usr/local/bin`, but an older version still
exists in `/usr/bin`. If `/usr/local/bin` isn't listed before `/usr/bin` in `PATH`, the shell
keeps running the old version despite the new one being installed — a genuinely common source of
"I updated it, why is it still using the old version" confusion.

## Commands

No new canonical command page on this page — [`which`](../../commands/which.md) (shows which
`PATH` entry resolves a command) is referenced as a forward reference; `echo $PATH` is a preview
of environment variable reading already covered in
[Environment Variables](environment-variables.md).

## Production example

```
$ command -v python3
/usr/bin/python3
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

`command -v` (a POSIX-portable alternative to `which`) confirms exactly which executable a command
name resolves to — the first match found while searching `PATH` in order.

## Do / Don't

| Do | Don't |
|---|---|
| Add new tool directories to the front of `PATH` when you want them to take priority | Assume installing a tool automatically makes it runnable by name |
| Use `command -v name` or `which name` to debug which executable actually runs | Guess which version of a duplicated command name is running |
| Set `PATH` changes in `.bashrc` to persist | Set `PATH` only at the prompt and lose it next session |

## Common mistakes

- Installing a new tool and being confused why `command_name` still runs the old version — almost
  always a `PATH` ordering issue, not a broken installation.
- Editing `PATH` incorrectly (overwriting it entirely instead of appending) and losing access to
  standard commands like `ls` until it's fixed.
- Not persisting a `PATH` change in a config file, losing it the next time a new shell opens.

## Best practices

- When appending to `PATH`, always include the existing value:
  `export PATH="$HOME/bin:$PATH"` — never overwrite it outright.
- Use `command -v` or `which` as the first debugging step whenever "wrong version is running"
  comes up.
- Put permanent `PATH` changes in `.bashrc` (see [Shell Config Files](shell-config-files.md)), not
  just at an interactive prompt.

## Exercises

1. Run `echo $PATH` and count how many directories are listed.
2. Run `command -v bash` and confirm which directory it resolves from.
3. Explain in one sentence why directory order in `PATH` matters when two directories contain a
   program with the same name.

## Quiz

**Q: If two directories in PATH both contain a program named `foo`, which one runs?**
<details><summary>Show answer</summary>
Whichever directory appears first in `PATH` — the shell searches left to right and uses the first
match.
</details>

**Q: What does `bash: command not found` almost always mean?**
<details><summary>Show answer</summary>
The program either isn't installed, or is installed in a directory that isn't listed in `PATH`.
</details>

## Interview questions

- Why might a newly installed CLI tool still run an old version after an upgrade? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `PATH` is an ordered, colon-separated list of directories searched for executables by name.
- The shell uses the first match found — order matters when names collide.
- `command not found` is almost always a missing installation or a `PATH` problem, not something
  more exotic.
- Always append to `PATH` (`$HOME/bin:$PATH`), never overwrite it outright.

## Related topics

- [Environment Variables](environment-variables.md)
- [Shell Config Files](shell-config-files.md)
