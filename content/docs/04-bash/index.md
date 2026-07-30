---
title: "Bash"
description: "Module 04 of the Linux roadmap — shell types, environment variables, config files, redirection, pipes, and job control. The mechanics behind everything typed into a terminal."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
type: "module-index"
nextTopic: "04-bash/shells-overview"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/bash"
---

# Bash

Module 04 of 34 · Stage: Beginner · Previous: [03 Terminal](../03-terminal/index.md)

Module 03 taught you to navigate and read commands. This module explains the shell itself — the
program running underneath everything you've typed so far — its configuration, its variables, and
the composition tools (redirection, pipes) that make the command line more than a list of
isolated commands.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Shells Overview: sh, bash, zsh, fish](shells-overview.md) | 🟢 Must Know |
| [Environment Variables](environment-variables.md) | 🟢 Must Know |
| [Shell Config Files](shell-config-files.md) | 🟢 Must Know |
| [Aliases and Functions](aliases-and-functions.md) | 🟢 Must Know |
| [The PATH Variable](path-variable.md) | 🟢 Must Know |
| [I/O Redirection](io-redirection.md) | 🟢 Must Know |
| [Pipes](pipes.md) | 🟢 Must Know |
| [Command Substitution](command-substitution.md) | 🟡 Good to Know |
| [Job Control](job-control.md) | 🟡 Good to Know |
| [Bash History Tricks](bash-history-tricks.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain what distinguishes bash, zsh, and fish, and why bash is this documentation's default.
- Read, set, and export environment variables, and know which config file controls what.
- Write your own aliases and simple functions, and customize your prompt (`PS1`) as previewed in
  Module 03.
- Understand why `command not found` is usually a `PATH` problem.
- Redirect output, chain commands with pipes, and use command substitution.
- Manage background/foreground jobs and use history recall efficiently.

## Known, intentional gaps in this module

- Canonical command pages exist for `export`, `alias`, `history`, and `jobs` — the four commands
  this module teaches in the most depth. `bg`, `fg`, `nohup`, `env`, `echo`, and `source` are
  referenced but not yet given full canonical pages — same forward-reference policy as prior
  modules (see [Module 03's note](../03-terminal/index.md)).
- This module closes the `PS1` forward reference opened in
  [Customizing the Shell Prompt](../03-terminal/customizing-shell-prompt.md) — see
  [Shell Config Files](shell-config-files.md).

**Previous module:** [03 Terminal](../03-terminal/index.md)
**Next module:** [05 File System →](../05-file-system/index.md)
