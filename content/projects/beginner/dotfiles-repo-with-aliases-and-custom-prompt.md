---
title: "Project: Build a Personal Dotfiles Repo with Aliases and a Custom Prompt"
description: "A beginner Linux project: build a version-controlled dotfiles repository with shell aliases, functions, and a custom prompt you can drop onto any new machine in minutes."
type: "project"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops", "software-engineering"]
updatedAt: "2026-07-30"
keywords: ["dotfiles project", "bash dotfiles beginner project", "custom bash prompt project", "linux shell customization project"]
canonicalUrl: "/projects/beginner/dotfiles-repo-with-aliases-and-custom-prompt"
---

# Project: Build a Personal Dotfiles Repo with Aliases and a Custom Prompt

🟢 Beginner · Relevant for: Linux Administrator, DevOps, Software Engineering

## Goal

Build a version-controlled repository of your shell configuration files ("dotfiles") — aliases,
functions, and a custom prompt — that you can clone onto any new machine and be productive on in
under five minutes.

## Requirements

- A `.bashrc` (or `.zshrc`) with at least 8 custom aliases for commands you actually type often.
- At least 2 custom shell functions (not just aliases) that take arguments.
- A custom prompt (`PS1`) showing at minimum: current directory and exit status of the last command.
- A install/setup script that symlinks the repo's files into `$HOME`.
- A README explaining what's in the repo and how to install it on a new machine.

## Suggested Approach

1. Start by listing the 10 commands you type most often day-to-day — that's your alias list, not
   a generic "best dotfiles" list copied from the internet.
2. Write your aliases and functions in a separate file (e.g. `aliases.sh`), sourced from
   `.bashrc` rather than inlined — keeps the main file readable as it grows.
3. Build the prompt incrementally: start with directory + exit status, then add a git branch
   indicator once the basics work.
4. Write the install script last, once the dotfiles themselves are stable — use symlinks
   (`ln -s`), not copies, so future edits to the repo apply immediately without reinstalling.
5. Test the install script on a fresh user account or a throwaway VM, not just your main machine —
   that's the only way to catch an assumption your shell already satisfies.

## Stretch Goals

- Add OS detection to the install script so it works on both a Debian-based and RHEL-based box.
- Add a `.vimrc` or `.tmux.conf` alongside the shell config.
- Show git branch and dirty-state in the prompt.
- Publish the repo publicly and write a short blog-style README section on your design choices.

## Related Modules

- [Bash](../../docs/04-bash/index.md) — aliases, functions, `PATH`, shell config files
- [Terminal](../../docs/03-terminal/index.md) — customizing the shell prompt
- [Files](../../docs/06-files/index.md) — symbolic links

## Related topics

- [Beginner Cheat Sheet: Linux Commands](../../cheatsheets/linux-commands.md)
- [Intermediate Projects](../intermediate/server-health-check-and-alerting-script.md)
- [Projects Hub](../index.md)
