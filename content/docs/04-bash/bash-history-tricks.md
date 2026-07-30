---
title: "Bash History Tricks"
description: "Beyond Ctrl+R — history expansion, searching past commands, and controlling what gets saved to history at all."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/job-control", "03-terminal/keyboard-shortcuts-and-history"]
relatedTopics: []
relatedCommands: ["history"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#history-expansion"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "05-file-system/fhs-deep-dive"
prevTopic: "04-bash/job-control"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["bash history tricks", "histcontrol", "history expansion bash", "history command"]
canonicalUrl: "/docs/bash/bash-history-tricks"
---

# Bash History Tricks

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** Beyond `Ctrl+R` (Module 03), the `history` command lets you list, search, and re-run
> commands by number (`!42`), and `HISTCONTROL`/`HISTIGNORE` let you control what gets saved to
> history at all — useful for keeping secrets typed at the prompt out of a persisted history file.

## What is it?

The deeper set of history tools beyond the `Ctrl+R` reverse search already covered in
[Keyboard Shortcuts and History](../03-terminal/keyboard-shortcuts-and-history.md): listing
history with line numbers, re-running specific past commands by number, and controlling what
bash saves to history in the first place.

## Why does it exist?

`Ctrl+R` covers interactive recall well, but doesn't help with reviewing a long session's full
history, re-running a specific numbered command precisely, or — importantly — keeping sensitive
values (a password typed directly at the prompt by mistake) from being permanently written to
`~/.bash_history`.

## Where is it used?

- Auditing what commands were run during an incident or investigation
- Re-running a specific earlier command precisely, by its history number
- Preventing sensitive commands from being saved to a persisted history file — a real, practical
  security habit

## How it works

> 📊 Diagram: a timeline of a terminal session with numbered commands, showing `history` listing
> them all, `!42` re-running command #42 specifically, and one command marked "not saved" due to
> `HISTCONTROL=ignorespace` (a leading space excludes a command from history).

- `history` — lists your command history with line numbers.
- `!42` — re-run the command at history line 42 exactly.
- `!string` — re-run the most recent command starting with `string` (also covered briefly in
  Module 03).
- `HISTCONTROL=ignorespace` — a command typed with a **leading space** is not saved to history at
  all; useful for keeping a one-off command containing a password out of your permanent history.
- `HISTCONTROL=ignoredups` — consecutive duplicate commands aren't saved repeatedly.
- `HISTIGNORE="ls:cd:pwd"` — exclude specific frequent, low-value commands from history entirely.

## Real-world example

An engineer accidentally types a database password directly into a command instead of using an
environment variable. Because `HISTCONTROL=ignorespace` is set and they prefixed the command with
a space out of habit, that command never gets written to `~/.bash_history` — a small
configuration choice that prevented a real, if minor, credential exposure in a plaintext file.

## Commands

- [`history`](../../commands/history.md) — list and manage command history

## Production example

```
$ history | tail -5
  501  cd /var/log
  502  ls -la
  503  grep error app.log
  504  history
$ !503
grep error app.log
```

## Do / Don't

| Do | Don't |
|---|---|
| Set `HISTCONTROL=ignorespace` and prefix sensitive one-off commands with a space | Type secrets directly into commands without any history precaution |
| Use `!N` to precisely re-run a known earlier command | Guess and retype a complex command you already ran once |
| Review `history` when investigating what happened during a session | Assume you'll remember exactly what you ran without checking |

## Common mistakes

- Typing a password or secret directly into a command with no `HISTCONTROL` precaution, leaving
  it sitting in plaintext in `~/.bash_history` indefinitely.
- Not knowing `!N` exists and retyping a long command from scratch instead of recalling it
  precisely by number.
- Assuming history is automatically excluded across all shells/sessions the same way — behavior
  depends on `HISTCONTROL`/`HISTIGNORE` settings that must be deliberately configured.

## Best practices

- Set `HISTCONTROL=ignorespace:ignoredups` in `.bashrc` as a standing habit, and get used to
  prefixing sensitive one-off commands with a leading space.
- Use `history | grep keyword` to search further back than interactive `Ctrl+R` recall
  comfortably covers.
- Periodically review history after an incident investigation to reconstruct exactly what was run.

## Exercises

1. Set `HISTCONTROL=ignorespace` in your current session and confirm a space-prefixed command
   doesn't appear in `history`.
2. Use `history | tail -5` to see your five most recent commands, then re-run one by number with
   `!N`.
3. Explain in one sentence why leading-space exclusion is a useful security habit.

## Quiz

**Q: How do you re-run the exact command at history line 42?**
<details><summary>Show answer</summary>
`!42`
</details>

**Q: What does `HISTCONTROL=ignorespace` do?**
<details><summary>Show answer</summary>
Excludes any command typed with a leading space from being saved to history — useful for keeping
one-off commands containing secrets out of a persisted history file.
</details>

## Interview questions

- How would you prevent a command containing a password from being saved to your shell history? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `history` lists commands with line numbers; `!N` re-runs a specific one precisely.
- `HISTCONTROL=ignorespace` is a real, practical way to keep sensitive one-off commands out of a
  persisted history file.
- This complements, rather than replaces, the `Ctrl+R` interactive search from Module 03.

## Related topics

- [Module 03: Keyboard Shortcuts and History](../03-terminal/keyboard-shortcuts-and-history.md)
