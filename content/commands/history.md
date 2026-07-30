---
title: "history — List and Manage Command History"
description: "List past commands with line numbers, re-run a specific one by number, and clear history."
relatedConcepts: ["03-terminal/keyboard-shortcuts-and-history", "04-bash/bash-history-tricks"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["history command linux", "bash history", "clear bash history", "history -c"]
canonicalUrl: "/commands/history"
---

# history

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** Lists your command history with line numbers. Combine with `!N` to re-run a specific
> command precisely, or `history -c` to clear it.

## Purpose

`history` displays the list of commands previously run in the current shell session (and, once
the session ends, persisted to `~/.bash_history`) — the tool behind
[Bash History Tricks](../docs/04-bash/bash-history-tricks.md).

## Syntax

```
history [OPTIONS] [N]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `N` | Show only the last N history entries | No |

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-c` | | Clear the entire history list for the current session |
| `-d N` | | Delete a specific history entry by line number |
| `-a` | | Append the current session's history to the history file immediately |

## Examples

```
$ history
```
List full command history with line numbers.

```
$ history 10
```
List only the last 10 commands.

```
$ history -c
```
Clear history for the current session.

## Expected Output

```
$ history 3
  502  ls -la
  503  grep error app.log
  504  history 3
```

## Exit Status

`0` on success.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `!N` re-runs the wrong command | Line numbers shift as new commands are run | Run `history` again immediately before referencing `!N` |
| History missing after closing a terminal | Not all shells/configs persist history the same way | Check `HISTSIZE`/`HISTFILESIZE` and that the shell exited normally (an abrupt kill can skip saving) |

## Security Considerations

`~/.bash_history` is a plaintext file — anything typed at the prompt, including accidentally
typed secrets, can end up saved there indefinitely. See
[Bash History Tricks](../docs/04-bash/bash-history-tricks.md) for `HISTCONTROL=ignorespace` as a
mitigation.

## Performance Considerations

Negligible, though an extremely large `HISTFILESIZE` can slow shell startup slightly.

## Production Usage

Reviewing `history` is a routine part of reconstructing "what did I just do" during incident
follow-up, especially combined with terminal logging tools in more rigorous audit environments.

## Related Commands

None documented yet.

## Related Concepts

- [Keyboard Shortcuts and History](../docs/03-terminal/keyboard-shortcuts-and-history.md)
- [Bash History Tricks](../docs/04-bash/bash-history-tricks.md)
