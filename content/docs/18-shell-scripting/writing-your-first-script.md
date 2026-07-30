---
title: "Writing Your First Script"
description: "The shebang line, making a script executable, and running it — the minimum ceremony every Bash script needs before its logic even starts."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/cron-troubleshooting-and-logging"]
relatedTopics: ["variables-and-data-types"]
relatedCommands: ["chmod"]
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#shebang-line"]
relatedCheatsheet: ""
furtherReading: [{"label": "Bash Reference Manual", "url": "https://www.gnu.org/software/bash/manual/bash.html"}]
nextTopic: "18-shell-scripting/variables-and-data-types"
prevTopic: "17-cron/cron-troubleshooting-and-logging"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["bash script tutorial", "shebang line", "chmod +x script", "run bash script"]
canonicalUrl: "/docs/shell-scripting/writing-your-first-script"
---

# Writing Your First Script

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** A shell script is a text file starting with a `#!/bin/bash` shebang line, made
> executable with `chmod +x`, and run either directly (`./script.sh`) or by naming an interpreter
> (`bash script.sh`).

## What is it?

A shell script is a plain text file containing a sequence of shell commands, saved so it can be
run as a single unit rather than typed interactively one line at a time.

## Why does it exist?

Every command typed in earlier modules ([Module 03](../03-terminal/index.md),
[Module 04](../04-bash/index.md)) works fine interactively, but any sequence run more than once —
a backup routine, a deploy step, a health check — becomes error-prone to retype and impossible to
schedule ([Module 17](../17-cron/index.md)) without saving it as a file first. Scripting turns a
one-off sequence of commands into a reusable, versionable, schedulable artifact.

## Where is it used?

Anywhere a sequence of commands needs to run repeatedly and reliably: deployment automation,
scheduled maintenance ([cron](../17-cron/index.md) jobs run scripts far more often than raw
commands), CI/CD pipeline steps, and any task complex enough that retyping it by hand risks a
mistake.

## How it works

> 📊 Diagram: a script file broken into three labeled parts top to bottom — the shebang line
> (`#!/bin/bash`), a comment block, and the command body — with an arrow from the file to a
> terminal showing `chmod +x` making it runnable, then `./script.sh` executing it.

**The minimum ceremony:**

1. **The shebang line** — the first line of the file, always `#!/bin/bash` (or `#!/bin/sh` for
   POSIX-portable scripts). It tells the kernel which interpreter should run the rest of the
   file. Without it, running the file directly falls back to whatever shell invoked it, which may
   not be what the script assumes.
2. **Make it executable** — `chmod +x script.sh` sets the execute permission bit (full detail in
   [Module 07: Permissions](../07-permissions/index.md)). Without this, `./script.sh` fails with
   "Permission denied."
3. **Run it** — `./script.sh` (using the shebang's interpreter) or explicitly `bash script.sh`
   (which works even without the execute bit set, since you're telling `bash` directly to read
   the file).

## Real-world example

A new engineer copies a colleague's script, tries to run it with `script.sh` (no `./` prefix), and
gets `command not found` — because the current directory usually isn't in `PATH`
([Module 04](../04-bash/path-variable.md)) for security reasons. Running it as `./script.sh`
(explicitly specifying the path) is the fix, and understanding *why* — that this isn't a
scripting bug but ordinary `PATH` behavior — avoids the same confusion on every script going
forward.

## Syntax

```
#!/bin/bash
# script body
```

## Commands

See [`chmod`](../../commands/chmod.md) for making a script executable — already fully covered in
Module 07.

## Production example

```
$ cat hello.sh
#!/bin/bash
echo "Hello from $(hostname)"

$ chmod +x hello.sh
$ ./hello.sh
Hello from web-prod-03
```

## Do / Don't

| Do | Don't |
|---|---|
| Start every script with an explicit shebang line | Assume a script will always be run with the interpreter you tested it with |
| Run a script as `./script.sh` or `bash script.sh` | Expect `script.sh` alone to work without it being on `PATH` |
| Set the execute bit with `chmod +x` before running directly | Forget the execute bit and get confused by "Permission denied" |

## Common mistakes

- Omitting the shebang line, causing the script to run under whatever shell invoked it instead of
  the intended one — subtly different behavior on systems where `/bin/sh` isn't `bash`.
- Forgetting `chmod +x`, then being confused by a "Permission denied" error that has nothing to do
  with the script's logic.
- Running `script.sh` without `./` and getting `command not found`, not realizing the current
  directory isn't in `PATH` by design.

## Best practices

- Always include an explicit shebang line, even for quick throwaway scripts — it costs nothing and
  prevents an entire class of environment-dependent bugs.
- Prefer `#!/bin/bash` over `#!/bin/sh` when using Bash-specific features (arrays, `[[ ]]`,
  `local`) — `/bin/sh` may be a stricter POSIX shell on some distros (`dash` on Debian/Ubuntu) that
  doesn't support them.
- Keep the first few lines of a new script minimal and test it runs at all before adding real
  logic — confirming the shebang and permissions are correct first isolates problems faster.

## Exercises

1. Write a two-line script that prints your username and the current date, make it executable,
   and run it.
2. Explain what happens if you omit the shebang line and run the script with `./script.sh` versus
   `bash script.sh`.
3. Describe why `script.sh` (without `./`) typically fails even after `chmod +x`.

## Quiz

**Q: What does the shebang line (#!/bin/bash) actually do?**
<details><summary>Show answer</summary>
It tells the kernel which interpreter to use to run the rest of the file when it's executed
directly.
</details>

**Q: Why does `./script.sh` work but `script.sh` alone often doesn't?**
<details><summary>Show answer</summary>
The current directory isn't normally included in `PATH`, so the shell can't find `script.sh` by
bare name — `./` explicitly specifies its location.
</details>

**Q: What error do you get if you try to run a script without the execute bit set?**
<details><summary>Show answer</summary>
"Permission denied" — `chmod +x` must be run first to grant execute permission.
</details>

## Interview questions

- What does the shebang line do, and what happens if it's missing? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Every script should start with an explicit shebang line (`#!/bin/bash`).
- `chmod +x` grants the execute permission a script needs to run directly.
- Run a script as `./script.sh` (or `bash script.sh`) — bare `script.sh` fails because the current
  directory isn't in `PATH`.
- This minimum ceremony (shebang, executable bit, explicit path) applies to every script written
  in the rest of this module.

## Related topics

- [Variables and Data Types](variables-and-data-types.md)
