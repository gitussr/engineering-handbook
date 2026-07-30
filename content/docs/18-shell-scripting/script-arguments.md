---
title: "Script Arguments: $1, $@, $#"
description: "Reading command-line arguments passed to a script — positional parameters, the difference between $@ and $*, and $# for argument count."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/functions"]
relatedTopics: ["functions", "exit-codes-and-error-handling"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#dollar-at-vs-dollar-star"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/exit-codes-and-error-handling"
prevTopic: "18-shell-scripting/functions"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["bash positional parameters", "dollar at vs dollar star", "bash script arguments", "bash argument count"]
canonicalUrl: "/docs/shell-scripting/script-arguments"
---

# Script Arguments: $1, $@, $#

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** `$1`, `$2`, ... are positional arguments; `$@` expands to all arguments as separate
> quoted words (almost always what you want); `$#` is the argument count. Quote `"$@"` — unquoted
> `$@` reintroduces the word-splitting problem from earlier in this module.

## What is it?

The special variables Bash populates automatically with whatever was passed on the command line
when a script (or function — see [Functions](functions.md)) was invoked.

## Why does it exist?

A script that only ever does one fixed thing has limited reuse. Reading arguments lets one script
handle a filename, a server name, a mode flag — anything the caller supplies — without editing the
script itself for each use.

## Where is it used?

Any script invoked with input that varies per run: a filename to process, a target server, a
`start`/`stop`/`restart` mode (tying directly into [`case`](conditionals-if-else-case.md)), or a
count/threshold value.

## How it works

> 📊 Diagram: a terminal invocation `./deploy.sh web-01 --force` broken into labeled boxes:
> `$0` = `./deploy.sh`, `$1` = `web-01`, `$2` = `--force`, `$#` = `2`, `$@` = both `$1` and `$2` as
> separate quoted words.

| Variable | Meaning |
|---|---|
| `$0` | The script's own name/path as invoked |
| `$1`, `$2`, ... | Positional arguments, in order |
| `$#` | The number of arguments passed |
| `$@` | All arguments, as separate quoted words when used as `"$@"` |
| `$*` | All arguments, as a single word (joined by the first character of `IFS`, normally a space) |

**`"$@"` vs `"$*"` — the difference that matters:**

```bash
# script.sh
for arg in "$@"; do
    echo "Arg: $arg"
done
```

Called as `./script.sh "first item" "second item"`, `"$@"` preserves them as two separate
arguments in the loop; `"$*"` would instead treat everything as one combined string
(`"first item second item"`) — almost always the wrong choice when forwarding arguments or
iterating over them individually.

**Checking argument count before proceeding:**

```bash
if [[ $# -lt 1 ]]; then
    echo "Usage: $0 SERVER_NAME"
    exit 1
fi
```

## Real-world example

A deploy script written to accept one server name (`$1`) is later called with multiple servers
(`./deploy.sh web-01 web-02 web-03`) by someone who assumed it would loop over all of them. Because
the script only ever reads `$1`, it silently deploys to just the first server, ignoring the rest —
no error, no warning. Adding an argument-count check (`$#`) and looping over `"$@"` instead of
reading only `$1` is the fix, and validating `$#` up front is exactly what would have caught the
mismatch immediately instead of silently doing the wrong thing.

## Syntax

```bash
$0    # script name
$1    # first argument
$#    # argument count
"$@"  # all arguments, individually quoted
```

## Commands

No new command example on this page — see [Functions](functions.md) for the last one, or
[Exit Codes and Error Handling](exit-codes-and-error-handling.md) for the next.

## Production example

```
$ cat deploy.sh
#!/bin/bash
if [[ $# -lt 1 ]]; then
    echo "Usage: $0 SERVER [SERVER...]"
    exit 1
fi

for server in "$@"; do
    echo "Deploying to $server"
done

$ ./deploy.sh web-01 web-02
Deploying to web-01
Deploying to web-02

$ ./deploy.sh
Usage: ./deploy.sh SERVER [SERVER...]
```

Validating `$#` up front, then iterating `"$@"` to genuinely handle any number of arguments — the
fix for the real-world example above.

## Do / Don't

| Do | Don't |
|---|---|
| Quote `"$@"` when iterating over or forwarding all arguments | Use unquoted `$@` or `$*`, reintroducing word-splitting |
| Validate `$#` before assuming expected arguments were provided | Assume `$1`, `$2`, etc. are always present without checking |
| Print a usage message and `exit 1` on invalid/missing arguments | Let a script silently do the wrong thing (or crash unhelpfully) on bad input |

## Common mistakes

- Reading only `$1` when a script should genuinely handle multiple arguments via `"$@"`, silently
  ignoring everything after the first.
- Using unquoted `$@` or `$*` instead of `"$@"`, causing word-splitting on arguments that contain
  spaces.
- Not validating `$#` before referencing `$1`, `$2`, etc., causing confusing behavior (an empty
  variable, not a clear error) when a required argument is missing.

## Best practices

- Validate `$#` at the top of every script that requires arguments, printing a clear usage
  message and exiting non-zero on failure.
- Default to `"$@"` (quoted) whenever forwarding or iterating over all arguments — reserve `$*`
  for the rare case you deliberately want them joined into one string.
- Give scripts a usage message (often triggered by `-h`/`--help` or invalid `$#`) so a caller
  doesn't have to read the script's source to know what arguments it expects.

## Exercises

1. Write a script that prints an error and exits 1 if no arguments were given, otherwise prints
   each argument on its own line.
2. Demonstrate the difference between `"$@"` and `"$*"` by calling a script with an argument that
   contains a space.
3. Explain what `$0` refers to, as distinct from `$1`.

## Quiz

**Q: What's the difference between "$@" and "$*"?**
<details><summary>Show answer</summary>
`"$@"` preserves each argument as a separate quoted word; `"$*"` joins all arguments into a single
string. `"$@"` is almost always the correct choice for iterating or forwarding arguments.
</details>

**Q: What does $# represent?**
<details><summary>Show answer</summary>
The number of arguments passed to the script (or function) — useful for validating that the
expected arguments were actually provided before using them.
</details>

**Q: Why should you validate $# before referencing $1 in a script?**
<details><summary>Show answer</summary>
Referencing `$1` when it wasn't provided just gives an empty value, not an error — validating
`$#` first lets the script fail clearly with a usage message instead of behaving unpredictably.
</details>

## Interview questions

- What's the difference between `$@` and `$*` in a Bash script? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `$1`, `$2`, ... are positional arguments; `$#` is the count; `$@`/`$*` represent all arguments.
- Always quote `"$@"` when iterating or forwarding — unquoted forms reintroduce word-splitting.
- Validate `$#` before assuming required arguments were provided, and fail with a clear usage
  message if not.
- `$0` is the script's own invoked name, distinct from the positional arguments that follow it.

## Related topics

- [Functions](functions.md)
- [Exit Codes and Error Handling](exit-codes-and-error-handling.md)
