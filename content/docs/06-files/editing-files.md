---
title: "Editing Files: nano, vim Basics"
description: "Making a quick edit to a remote config file without leaving the terminal — nano for simplicity, vim for ubiquity and power."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["06-files/viewing-files"]
relatedTopics: ["finding-files"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#vim-basics"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "vimtutor (run `vimtutor` in any terminal with vim installed)", "url": "https://www.vim.org/"}]
nextTopic: "06-files/finding-files"
prevTopic: "06-files/viewing-files"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["nano vs vim", "vim insert mode", "vim basics tutorial", "how to exit vim"]
canonicalUrl: "/docs/files/editing-files"
---

# Editing Files: nano, vim Basics

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend

> **TL;DR:** `nano file` is immediately usable — on-screen shortcuts, no modes. `vim file` is
> modal and has a real learning curve, but is installed nearly everywhere and vastly more
> powerful once learned. To exit vim without saving: `Esc` then `:q!`. To save and exit: `Esc`
> then `:wq`.

## What is it?

Two terminal-based text editors for making changes to files directly over SSH, without a GUI.
`nano` is simple and immediately usable; `vim` is modal (different modes for typing vs. issuing
commands) with a steeper learning curve but far more editing power once learned.

## Why does it exist?

Config files on a remote server need editing without a GUI available — see
[GUI vs CLI](../01-linux-basics/gui-vs-cli.md). Both editors solve this; they exist as separate
tools because they make different tradeoffs between immediate usability (`nano`) and long-term
editing efficiency (`vim`).

## Where is it used?

- Quick config edits during troubleshooting, directly over SSH
- `vim` specifically: any environment where `nano` isn't installed (vim or its minimal cousin
  `vi` is present on nearly every Linux system by default, making it worth knowing baseline
  survival commands even if it's not your daily editor)

## How it works

> 📊 Diagram: two flowcharts side by side — "nano": open → type directly → `Ctrl+O` save →
> `Ctrl+X` exit, all visible as on-screen hints. "vim": open in Normal mode → `i` enters Insert
> mode to type → `Esc` returns to Normal mode → `:wq` saves and exits — emphasizing vim's mode
> switching as the core concept to internalize.

**nano**: opens directly into an editable view with shortcuts listed on screen (`^O` = `Ctrl+O`
to save, `^X` = `Ctrl+X` to exit). No modes — what you type is what appears.

**vim**: opens in **Normal mode** (keys are commands, not text). Press `i` to enter **Insert
mode** and start typing; press `Esc` to return to Normal mode. From Normal mode:

| Keys | Effect |
|---|---|
| `i` | Enter Insert mode (start typing) |
| `Esc` | Return to Normal mode |
| `:w` | Save |
| `:q` | Quit (fails if there are unsaved changes) |
| `:wq` | Save and quit |
| `:q!` | Quit without saving, discarding changes |
| `dd` | Delete the current line (Normal mode) |
| `/pattern` | Search forward for a pattern |

The single most commonly needed vim survival skill for beginners: getting *out* of it. `Esc` then
`:q!` always works to exit without saving.

## Real-world example

An engineer SSHs into a server to fix a typo in an Nginx config. If `nano` is installed, it's the
faster choice for a one-line fix — no mode-switching needed. If only `vim`/`vi` is available (true
on many minimal server images), knowing the bare minimum (`i` to edit, `Esc` then `:wq` to save)
is the difference between a thirty-second fix and being stuck in an editor you don't know how to
leave.

## Commands

No new canonical command page on this page — `nano` and `vim` are interactive full-screen
programs, documented here as a topic rather than a single-command reference page.

## Production example

```
$ vim /etc/nginx/nginx.conf
# press i to enter Insert mode, make the edit
# press Esc, then type :wq and press Enter to save and exit
```

## Do / Don't

| Do | Don't |
|---|---|
| Learn `Esc` then `:q!` before anything else in vim | Panic and force-close your terminal because you're stuck in vim |
| Use `nano` for quick one-off edits when available | Fight with vim's modes for a trivial edit if `nano` is installed and sufficient |
| Confirm which mode you're in before typing in vim | Type text expecting it to appear while still in Normal mode |

## Common mistakes

- Typing text while still in vim's Normal mode, triggering unexpected commands instead of
  inserting text — the classic vim beginner confusion.
- Not knowing how to exit vim and closing the whole terminal window instead — `Esc` then `:q!`
  (or `:wq` to save) always works.
- Assuming `nano` is always installed — many minimal server/container images ship only `vi`/`vim`.

## Best practices

- Memorize the vim exit sequence (`Esc`, `:q!` or `:wq`) even if `vim` isn't your primary editor —
  it comes up unexpectedly often on unfamiliar systems.
- Use `nano` for quick, low-stakes edits when it's available; invest in learning vim properly
  (via `vimtutor`) if you expect to be editing files over SSH regularly.

## Exercises

1. Open a test file in `nano`, make an edit, save, and exit.
2. Open the same file in `vim`, enter Insert mode, make an edit, and save with `:wq`.
3. Deliberately get "stuck" in vim's Normal mode and practice exiting without saving using `:q!`.

## Quiz

**Q: How do you exit vim without saving any changes?**
<details><summary>Show answer</summary>
Press `Esc` to ensure you're in Normal mode, then type `:q!` and press Enter.
</details>

**Q: Why can't you just start typing immediately after opening a file in vim?**
<details><summary>Show answer</summary>
vim opens in Normal mode, where keystrokes are interpreted as commands, not text — you must press
`i` first to enter Insert mode before typing appears as content.
</details>

## Interview questions

- How would you edit a file over SSH if only vim is available? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `nano` is immediately usable with on-screen shortcuts; `vim` is modal with a learning curve but
  far more powerful.
- `Esc` then `:q!` (discard) or `:wq` (save and quit) are the essential vim survival commands.
- `vim`/`vi` is present on nearly every Linux system by default; `nano` sometimes isn't.

## Further Reading

- [`vimtutor`](https://www.vim.org/) — an interactive vim tutorial built into most vim installs

## Related topics

- [Finding Files](finding-files.md)
