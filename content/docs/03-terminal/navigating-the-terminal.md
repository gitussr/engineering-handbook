---
title: "Navigating the Terminal"
description: "Absolute vs relative paths, the current working directory, and tab completion — the mental model behind every terminal navigation command."
module: "03-terminal"
moduleTitle: "Terminal"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["03-terminal/terminal-vs-shell-vs-console"]
relatedTopics: ["pwd-ls-cd", "command-structure-and-syntax"]
relatedCommands: ["pwd", "ls", "cd"]
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#absolute-vs-relative-paths"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "03-terminal/pwd-ls-cd"
prevTopic: "03-terminal/terminal-vs-shell-vs-console"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["absolute path", "relative path", "current working directory", "tab completion"]
canonicalUrl: "/docs/terminal/navigating-the-terminal"
---

# Navigating the Terminal

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** Every location in Linux is a path — absolute (starts at `/`, always works, longer to
> type) or relative (starts from where you currently are, shorter, breaks if you're in the wrong
> place). Tab completion is not optional convenience — it's how professionals avoid typos on long
> paths.

## What is it?

The mental model and habits behind moving around a Linux filesystem from the command line:
understanding your current location (the current working directory), the difference between
absolute and relative paths, and using tab completion instead of typing full paths by hand.

## Why does it exist?

Every command that touches a file or directory needs to know where that file or directory is.
Linux gives you two ways to specify that: spell out the full path from the root every time
(absolute), or describe it relative to wherever you currently are (relative). Neither is
"better" — they're suited to different situations, and professionals switch between them
deliberately.

## Where is it used?

Every single terminal session, from your very first command through the most advanced shell
script in Module 18. Path confusion is one of the most common sources of "file not found" and
"command not found" errors for beginners — this page exists to prevent that confusion before it
starts.

## How it works

> 📊 Diagram: a filesystem tree diagram showing `/home/user/projects/app` with two labeled paths
> to the same file — one absolute (`/home/user/projects/app/config.txt`) starting from `/`, one
> relative (`config.txt` or `./config.txt`) assuming the current directory is already
> `/home/user/projects/app` — to make the "same destination, two notations" idea concrete.

- **Absolute path** — starts with `/`, always describes the same location regardless of where you
  currently are. Example: `/etc/nginx/nginx.conf`.
- **Relative path** — describes a location relative to your **current working directory** (where
  you "are" right now in the filesystem). Example: from `/etc`, `nginx/nginx.conf` reaches the
  same file.
- **Special relative notations**: `.` means "current directory," `..` means "one directory up,"
  `~` means "your home directory." `cd ..` and `cd ~` are two of the most-typed commands in daily
  Linux work.
- **Tab completion**: pressing Tab while typing a path auto-completes it as far as it's
  unambiguous, and shows options when it's not. This isn't a shortcut for the lazy — it's how you
  avoid a typo three directories deep in a long path.

## Real-world example

An engineer SSHs into a server and needs to check a log file. Typing the full absolute path
(`/var/log/nginx/error.log`) works from anywhere, immediately, with zero ambiguity — the right
choice when you don't yet know or care where you currently are. Once already working inside
`/var/log/nginx/`, typing just `error.log` (a relative path) is faster and just as correct. Both
are professional habits; picking the right one for the situation is the actual skill.

## Commands

- [`pwd`](../../commands/pwd.md) — print your current working directory
- [`ls`](../../commands/ls.md) — list what's in a directory
- [`cd`](../../commands/cd.md) — change your current working directory

Full syntax and options for all three: [pwd, ls, cd](pwd-ls-cd.md).

## Production example

```
$ pwd
/home/deploy
$ cd /var/log/nginx
$ pwd
/var/log/nginx
$ cd ..
$ pwd
/var/log
```

This sequence — check where you are, move somewhere absolute, move relative — is the exact
pattern used constantly during log investigation and server troubleshooting.

## Do / Don't

| Do | Don't |
|---|---|
| Use `pwd` when you're unsure where you currently are | Guess your current directory and run a command anyway |
| Use tab completion on any path longer than a few characters | Type long paths fully by hand and risk a typo |
| Use absolute paths in scripts (predictable regardless of caller's location) | Hardcode relative paths in scripts run from unknown locations |

## Common mistakes

- Running a relative-path command from the wrong directory, causing a confusing "file not found"
  that has nothing to do with the file actually being missing.
- Not using tab completion, leading to typos on long paths — especially costly on paths with
  similar-looking directory names.
- Using relative paths inside scripts that might be run from any directory, causing them to work
  on your machine and fail elsewhere — a very common early shell-scripting bug (foreshadowed here,
  fully covered in [Module 18](../18-shell-scripting/index.md)).

## Best practices

- When in doubt about your location, run `pwd` first — it costs nothing and prevents an entire
  category of mistakes.
- Use tab completion by default; it's faster and safer than typing paths in full.
- Prefer absolute paths inside scripts and automation specifically because they don't depend on
  where the script happens to be run from.

## Exercises

1. Open a terminal, run `pwd`, then navigate to your home directory using `~` and confirm with
   `pwd` again.
2. Navigate two directories up using `cd ../..` and identify where you land.
3. Practice tab completion by typing the first few characters of a long directory name and
   pressing Tab.

## Quiz

**Q: What does `cd ..` do?**
<details><summary>Show answer</summary>
Moves your current working directory up one level (to the parent directory).
</details>

**Q: Why prefer absolute paths inside scripts?**
<details><summary>Show answer</summary>
A script using relative paths only works correctly if run from the exact directory it expects —
absolute paths work regardless of where the script is invoked from.
</details>

## Interview questions

- What's the difference between an absolute and a relative path? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Absolute paths start at `/` and always work; relative paths depend on your current location.
- `.`, `..`, and `~` are the three relative notations worth memorizing immediately.
- Tab completion prevents typos on long paths — use it by default, not as a last resort.
- Scripts should default to absolute paths for predictability regardless of caller location.

## Related topics

- [pwd, ls, cd](pwd-ls-cd.md)
- [Module 05: File System](../05-file-system/index.md)
