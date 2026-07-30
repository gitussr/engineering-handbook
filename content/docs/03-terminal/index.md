---
title: "Terminal"
description: "Module 03 of the Linux roadmap — navigating, reading, and working efficiently in the terminal. Where real command-line skill starts."
module: "03-terminal"
moduleTitle: "Terminal"
stage: "beginner"
type: "module-index"
nextTopic: "03-terminal/terminal-vs-shell-vs-console"
updatedAt: "2026-07-25"
canonicalUrl: "/docs/terminal"
---

# Terminal

Module 03 of 34 · Stage: Beginner · Previous: [02 Installing Linux](../02-installing-linux/index.md)

This is where hands-on command-line work actually starts. Modules 01 and 02 used a handful of
illustrative commands; from here on, every command introduced gets its own canonical reference
page under [`/commands/`](../../commands/index.md), and this module builds the foundation —
navigation, syntax, getting help, and working efficiently — that every later module assumes.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Terminal vs Shell vs Console](terminal-vs-shell-vs-console.md) | 🟢 Must Know |
| [Navigating the Terminal](navigating-the-terminal.md) | 🟢 Must Know |
| [pwd, ls, cd](pwd-ls-cd.md) | 🟢 Must Know |
| [Command Structure and Syntax](command-structure-and-syntax.md) | 🟢 Must Know |
| [man, --help, tldr](man-help-tldr.md) | 🟢 Must Know |
| [Keyboard Shortcuts and History](keyboard-shortcuts-and-history.md) | 🟢 Must Know |
| [Terminal Multiplexers: tmux, screen](terminal-multiplexers.md) | 🟡 Good to Know |
| [Customizing the Shell Prompt](customizing-shell-prompt.md) | 🟡 Good to Know |

## What you should be able to do after this module

- Explain the difference between a terminal, a shell, and a console without hand-waving.
- Navigate any filesystem confidently using absolute and relative paths.
- Read and construct commands correctly — options, flags, arguments, and man page notation.
- Find help for any command without leaving the terminal or searching the web.
- Work efficiently: history recall, keyboard shortcuts, and persistent sessions via a multiplexer.

## Known, intentional gaps in this module

- Canonical command pages exist for `pwd`, `ls`, `cd`, `man`, and `tmux` — the first four are the
  commands this module is built around; `tmux` got a standalone page directly since no later
  module owns and deepens it. Other commands mentioned (`tldr`, `history`, `screen`, `alias`) are
  referenced but their full `/commands/{cmd}` pages are written when their owning module deepens
  them (e.g. `alias` in [Module 04: Bash](../04-bash/index.md)).
- `relatedLabs` and interview/cheat sheet links follow the same forward-reference policy noted in
  Modules 01 and 02 — they resolve once those later modules exist.

**Previous module:** [02 Installing Linux](../02-installing-linux/index.md)
**Next module:** [04 Bash →](../04-bash/index.md)
