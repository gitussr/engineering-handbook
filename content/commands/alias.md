---
title: "alias — Create a Command Shortcut"
description: "Define, list, and remove shell aliases — simple text substitutions for frequently typed commands."
relatedConcepts: ["04-bash/aliases-and-functions"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["alias command linux", "bash alias syntax", "unalias"]
canonicalUrl: "/commands/alias"
---

# alias

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `alias name='command'` creates a shortcut. Aliases defined at the prompt only last
> for the current session — put them in `.bashrc` to persist.

## Purpose

`alias` creates a shorthand name that expands to a longer command when typed — the mechanism
behind the aliases covered conceptually in
[Aliases and Functions](../docs/04-bash/aliases-and-functions.md).

## Syntax

```
alias name='command'
alias
unalias name
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `name='command'` | Define an alias | No (omit to list existing aliases) |
| `name` (with `unalias`) | Remove an existing alias | Yes, for `unalias` |

## Options

`alias`/`unalias` are shell built-ins with no options in common day-to-day use.

## Examples

```
$ alias ll='ls -la'
```
Define an alias.

```
$ alias
```
List all currently defined aliases.

```
$ unalias ll
```
Remove an alias.

## Expected Output

```
$ alias
alias ll='ls -la'
alias gs='git status'
```

## Exit Status

`0` on success, `1` if `unalias` is given a name that isn't currently aliased.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Alias disappears in a new terminal | Defined only at the prompt, not saved to a config file | Add the `alias` line to `.bashrc` |
| `bash: unalias: name: not found` | Trying to remove an alias that doesn't exist | Check `alias` output first to confirm the name |
| Alias silently doesn't behave like the real command | It's shadowing a real command name unexpectedly | Use `\command` (backslash prefix) to bypass an alias once |

## Security Considerations

An alias shadowing a common command name (e.g. `alias ls='rm -rf /'` as an extreme example) is a
known social-engineering trick in shared/compromised environments — always check `alias` output
on an unfamiliar system before trusting basic commands blindly.

## Performance Considerations

Negligible.

## Production Usage

Teams often standardize a shared set of aliases (e.g. `alias k='kubectl'`) across `.bashrc`
templates distributed via dotfiles repos or provisioning scripts, so the whole team gets
consistent shortcuts without each person configuring them individually.

## Related Commands

None documented yet — shell functions (the next step up from aliases) are covered conceptually,
not as a standalone command page, since they're a shell language feature rather than a single
command.

## Related Concepts

- [Aliases and Functions](../docs/04-bash/aliases-and-functions.md)
