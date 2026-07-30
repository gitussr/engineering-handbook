---
title: "cd — Change Directory"
description: "Change your current working directory, including the home, previous-directory, and parent-directory shortcuts."
relatedConcepts: ["03-terminal/navigating-the-terminal", "03-terminal/pwd-ls-cd"]
relatedCommands: ["pwd", "ls"]
careerRelevance: ["devops", "linux-administrator", "backend"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["cd command", "change directory linux", "cd shortcuts", "cd dash"]
canonicalUrl: "/commands/cd"
---

# cd

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** Changes your current working directory. `cd` alone goes home, `cd -` toggles back to
> your previous directory, `cd ..` goes up one level — these three cover most real usage.

## Purpose

`cd` changes your shell's current working directory to the one specified. It's the answer to "how
do I get somewhere else" in the navigation loop covered in
[pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md). Unlike most commands, `cd` is a shell built-in,
not a separate program — it has to be, since it changes state (your current directory) that only
persists if the shell itself changes it.

## Syntax

```
cd [PATH]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` | Directory to move into — absolute, relative, or a shortcut like `~` or `-` | No (defaults to home) |

## Options

`cd` is a shell built-in with no standard flags in the way external commands have them — its
behavior is controlled entirely through the argument passed.

## Examples

```
$ cd /var/log
```
Move to an absolute path.

```
$ cd logs
```
Move to a relative path from the current directory.

```
$ cd
```
No argument — goes to your home directory.

```
$ cd ~
```
Explicitly goes to your home directory (equivalent to bare `cd`).

```
$ cd -
```
Toggles back to the directory you were in before your last `cd`.

```
$ cd ..
```
Moves up one directory level.

## Expected Output

`cd` produces no output on success — the only visible change is your prompt (if it displays the
current directory) and what `pwd` reports afterward. `cd -` is the one exception: it prints the
directory it switched to.

```
$ cd -
/home/deploy
```

## Exit Status

`0` on success, `1` if the target directory doesn't exist or isn't accessible.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `bash: cd: path: No such file or directory` | Typo in the path, or the directory doesn't exist | Use tab completion; verify with `ls` first |
| `bash: cd: path: Permission denied` | No execute permission on the target directory | Check permissions (full detail in Module 07) |
| `cd -` does nothing useful the first time | No previous directory recorded yet in this session | Normal — `cd -` only works after at least one prior `cd` |

## Security Considerations

None specific to `cd` itself — it only changes your own shell's state, not system state or
permissions.

## Performance Considerations

Negligible — `cd` is effectively instantaneous.

## Production Usage

`cd -` is the single most underused shortcut by beginners and the most used by experienced
engineers — jumping back and forth between two directories (e.g. a project root and a log
directory) during an investigation is far faster than retyping either path.

## Related Commands

- [`pwd`](pwd.md) — confirm where you landed after a `cd`
- [`ls`](ls.md) — see what's in a directory before or after moving into it

## Related Concepts

- [Navigating the Terminal](../docs/03-terminal/navigating-the-terminal.md)
- [pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md)
