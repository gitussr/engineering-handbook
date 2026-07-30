---
title: "Shell Config Files"
description: "Where .bashrc, .bash_profile, and /etc/profile fit, which one runs when, and where PS1 prompt customization actually goes — closing the loop from Module 03."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/environment-variables"]
relatedTopics: ["aliases-and-functions", "path-variable"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#bashrc-vs-bash-profile"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/aliases-and-functions"
prevTopic: "04-bash/environment-variables"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["bashrc vs bash_profile", "etc profile", "ps1 customization", "login shell vs non-login shell"]
canonicalUrl: "/docs/bash/shell-config-files"
---

# Shell Config Files

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Software Engineering

> **TL;DR:** `.bashrc` runs for every new interactive shell (put aliases, functions, and `PS1`
> here). `.bash_profile` runs only for login shells (put environment variables meant to be set
> once per login here). `/etc/profile` is the system-wide equivalent, applying to every user.

## What is it?

Shell config files are scripts bash automatically runs at specific points (login, opening a new
terminal) to set up your environment — variables, aliases, functions, and your prompt (`PS1`,
previewed in [Customizing the Shell Prompt](../03-terminal/customizing-shell-prompt.md)) all get
configured here rather than typed manually every session.

## Why does it exist?

Without config files, every environment variable, alias, and prompt customization would need to
be retyped manually every time you open a terminal. Config files persist your setup automatically,
and the login-vs-interactive distinction exists because some setup (environment variables) only
needs to happen once per login, while other setup (aliases, prompt) needs to apply to every new
shell you open, even ones opened without a fresh login.

## Where is it used?

Every bash session, every time. This is also exactly how a company's platform team standardizes
environment setup across a fleet — via `/etc/profile` or `/etc/bash.bashrc` applying to every
user on every server.

## How it works

> 📊 Diagram: a flowchart — "New terminal opened" branches into "Login shell?" (yes → runs
> `/etc/profile` then `~/.bash_profile`) and "Interactive non-login shell?" (yes → runs
> `~/.bashrc`), both converging on "Shell ready for use," with a note that `.bash_profile`
> conventionally sources `.bashrc` itself so most setups don't have to think about the distinction
> day to day.

| File | Runs for | Typical contents |
|---|---|---|
| `/etc/profile` | Every user's login shell (system-wide) | Org-wide environment variables, PATH additions |
| `~/.bash_profile` (or `~/.profile`) | Your login shell only (once per login/SSH session) | Personal environment variables, things that should run once |
| `~/.bashrc` | Every new interactive shell (including non-login ones, e.g. a new terminal tab) | Aliases, functions, `PS1` prompt customization, anything you want in every new shell |

The common convention: `~/.bash_profile` sources (`source ~/.bashrc`) so most personal setup only
needs to live in one place (`.bashrc`), and works correctly whether the shell is a login shell or
not.

**Closing the loop from Module 03**: `PS1` customization
([Customizing the Shell Prompt](../03-terminal/customizing-shell-prompt.md)) belongs in
`.bashrc`, since you want your customized prompt in every new shell, not just at login.

## Real-world example

A platform team adds a company-wide `PATH` addition and a standard `PS1` format (showing
environment name) to `/etc/profile` and `/etc/bash.bashrc` respectively, so every engineer who
logs into any server in the fleet gets the same baseline setup automatically — without asking
each engineer to configure it themselves.

## Commands

No new canonical command page on this page — `source` (used to reload a config file without
reopening the terminal) is referenced as a forward reference.

## Production example

```
$ echo 'export EDITOR=vim' >> ~/.bashrc
$ source ~/.bashrc
$ echo $EDITOR
vim
```

`source` re-runs the config file in your current shell immediately, instead of requiring you to
close and reopen the terminal to pick up the change.

## Do / Don't

| Do | Don't |
|---|---|
| Put `PS1`, aliases, and functions in `.bashrc` | Put prompt customization only in `.bash_profile`, missing new non-login shells |
| Use `source ~/.bashrc` to apply changes immediately | Close and reopen your terminal every time you edit a config file |
| Use `/etc/profile` for genuinely org-wide settings | Rely on every user copying settings into their own files manually |

## Common mistakes

- Putting `PS1` or alias definitions only in `.bash_profile`, then being confused why a newly
  opened terminal tab (a non-login shell) doesn't have them.
- Forgetting to `source` a config file after editing it and assuming the change didn't work.
- Editing `/etc/profile` for a personal preference that should have been a per-user `.bashrc`
  change instead, affecting every user on a shared system.

## Best practices

- Keep personal customization (aliases, functions, `PS1`) in `.bashrc`, since it covers every new
  shell, not just logins.
- Reserve `/etc/profile` changes for genuinely system-wide, every-user settings.
- Get in the habit of `source`-ing a config file immediately after editing it, rather than
  reopening a terminal.

## Exercises

1. Add an alias to your `.bashrc` and use `source` to apply it without opening a new terminal.
2. Explain in one sentence why `.bash_profile` alone wouldn't be enough for prompt customization.
3. Find `/etc/profile` on a Linux system and identify one thing it configures.

## Quiz

**Q: Where should `PS1` prompt customization go, and why?**
<details><summary>Show answer</summary>
`.bashrc` — because it runs for every new interactive shell, including ones that aren't fresh
logins (e.g. a new terminal tab), while `.bash_profile` only runs at login.
</details>

**Q: What does `source ~/.bashrc` do?**
<details><summary>Show answer</summary>
Re-runs the config file in your current shell session immediately, applying any changes without
needing to open a new terminal.
</details>

## Interview questions

- What's the difference between `.bashrc` and `.bash_profile`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `.bashrc` runs for every new interactive shell — put aliases, functions, and `PS1` here.
- `.bash_profile` runs only at login — for setup that only needs to happen once per session.
- `/etc/profile` is the system-wide equivalent, applying to every user.
- `source` reloads a config file immediately without needing a new terminal.

## Related topics

- [Customizing the Shell Prompt](../03-terminal/customizing-shell-prompt.md)
- [Aliases and Functions](aliases-and-functions.md)
