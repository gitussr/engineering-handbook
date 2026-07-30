---
title: "Shells Overview: sh, bash, zsh, fish"
description: "The major shell programs, how they differ, and why bash is the safe default for scripts even if your interactive shell is something else."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["03-terminal/terminal-vs-shell-vs-console"]
relatedTopics: ["environment-variables", "shell-config-files"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#bash-vs-sh"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "GNU Bash Reference Manual", "url": "https://www.gnu.org/software/bash/manual/"}]
nextTopic: "04-bash/environment-variables"
prevTopic: "03-terminal/customizing-shell-prompt"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["bash vs zsh", "sh vs bash", "fish shell", "which shell am i using"]
canonicalUrl: "/docs/bash/shells-overview"
---

# Shells Overview: sh, bash, zsh, fish

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Software Engineering

> **TL;DR:** bash is the default, most-supported shell on virtually every Linux server — write
> scripts for bash unless you have a specific reason not to. zsh and fish are popular interactive
> alternatives with nicer UX; `sh` is the POSIX baseline scripts fall back to for portability.

## What is it?

A shell is the program that reads and executes your commands, as defined in
[Terminal vs Shell vs Console](../03-terminal/terminal-vs-shell-vs-console.md). Several different
shell programs exist, each with its own syntax quirks, interactive features, and scripting
behavior. This page compares the ones you'll actually encounter.

## Why does it exist?

Different shells optimize for different things: `sh` for strict, portable POSIX compliance;
`bash` for a balance of features and near-universal availability; `zsh` and `fish` for a nicer
interactive experience (better completion, plugins, prompts) at some cost to portability. Knowing
which is which prevents two common problems: writing a script that breaks on another engineer's
machine, and being confused when your daily shell (zsh) behaves differently from a script you
wrote assuming bash.

## Where is it used?

- **bash** — the default login shell on the vast majority of Linux servers and Docker images;
  this documentation's scripting default from [Module 18](../18-shell-scripting/index.md) onward.
- **sh** — often a symlink to a minimal POSIX-compliant shell (like `dash` on Ubuntu); scripts
  starting with `#!/bin/sh` are opting into stricter portability, giving up bash-only features.
- **zsh** — a popular interactive shell (macOS's default since Catalina); mostly bash-compatible
  for scripting but with extra interactive features.
- **fish** — a shell designed around interactive friendliness (syntax highlighting, smart
  suggestions out of the box) but intentionally not POSIX-compatible — fish scripts are not
  portable to bash/sh.

## How it works

> 📊 Diagram: a simple comparison table rendered as a visual — four columns (sh, bash, zsh, fish)
> each with rows for "POSIX compliant," "Interactive features," "Script portability," "Common use
> case" — to make the tradeoffs scannable at a glance.

| Shell | POSIX compliant | Interactive UX | Script portability | Typical use |
|---|---|---|---|---|
| `sh` | Yes (strict) | Minimal | Highest | Portable scripts, minimal containers |
| `bash` | Mostly | Good | High (near-universal) | Default server shell, this documentation's script default |
| `zsh` | Mostly | Very good | Moderate | Interactive daily driver (macOS default) |
| `fish` | No | Best out of the box | Low (not POSIX) | Interactive daily driver, not for portable scripts |

Your login shell is set per-user and can be checked with `echo $SHELL`. It's independent of which
shell a script declares via its shebang line (`#!/bin/bash`) — you can run a bash script from a
zsh interactive session without issue, since the shebang determines what actually executes the
script.

## Real-world example

An engineer writes and tests a deployment script on their local machine, where their interactive
shell is zsh. The script works fine locally but fails on the production server, which only has
`bash` and `sh` installed, no zsh — because the script used a zsh-only feature without realizing
it. Declaring `#!/bin/bash` explicitly and testing under bash specifically would have caught this
before it reached production.

## Commands

No new canonical command page on this page — `echo $SHELL` is a preview of environment variable
usage, taught fully in [Environment Variables](environment-variables.md).

## Production example

```
$ echo $SHELL
/bin/bash
$ bash --version
GNU bash, version 5.1.16(1)-release
```

## Do / Don't

| Do | Don't |
|---|---|
| Write scripts assuming bash unless you need strict POSIX portability | Assume your interactive shell (zsh, fish) matches what a script will run under |
| Declare `#!/bin/bash` explicitly in scripts | Rely on the default shebang without checking it matches your target environment |

## Common mistakes

- Writing and testing a script only in a non-bash interactive shell (zsh, fish), then having it
  fail in production where only bash/sh are available.
- Assuming `sh` and `bash` are identical — `sh` is often a stricter, more limited shell even when
  it's technically the same binary as bash in "POSIX mode."
- Forgetting the shebang line (`#!/bin/bash`) determines what actually runs a script, independent
  of your current interactive shell.

## Best practices

- Default to bash for anything you intend to run on a server, regardless of what your personal
  interactive shell is.
- Always declare an explicit shebang (`#!/bin/bash`) rather than relying on defaults.
- Use zsh or fish locally for interactive comfort if you like, but test scripts under bash before
  considering them done.

## Exercises

1. Run `echo $SHELL` to see your current login shell.
2. Run `bash --version` to confirm bash is available even if it's not your default shell.
3. Explain in one sentence why a script's shebang line matters more than your interactive shell.

## Quiz

**Q: If your interactive shell is zsh, does a script with `#!/bin/bash` run under zsh or bash?**
<details><summary>Show answer</summary>
Bash — the shebang line determines which interpreter actually runs the script, independent of
your current interactive shell.
</details>

**Q: Why default to bash for scripts even if you prefer zsh interactively?**
<details><summary>Show answer</summary>
Bash is near-universally available on servers and containers, while zsh often isn't — a bash
script is far more portable across the environments it will actually run in.
</details>

## Interview questions

- Why might a script work locally but fail on a production server with the same commands? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- bash is the safe default for scripts; it's nearly universally available on servers.
- zsh and fish are popular interactive alternatives but shouldn't be assumed available for scripts.
- `sh` is the strict POSIX baseline used when maximum portability matters.
- A script's shebang line, not your interactive shell, determines what actually executes it.

## Further Reading

- [GNU Bash Reference Manual](https://www.gnu.org/software/bash/manual/)

## Related topics

- [Environment Variables](environment-variables.md)
- [Shell Config Files](shell-config-files.md)
