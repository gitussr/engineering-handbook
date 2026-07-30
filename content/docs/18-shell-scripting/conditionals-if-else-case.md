---
title: "Conditionals: if/else/case"
description: "Branching logic in shell scripts — if/elif/else, the test operators inside [ ] and [[ ]], and case for matching against multiple patterns."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/variables-and-data-types"]
relatedTopics: ["variables-and-data-types", "loops-for-while-until"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#single-vs-double-bracket-test"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/loops-for-while-until"
prevTopic: "18-shell-scripting/variables-and-data-types"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["bash if else", "bash case statement", "single bracket vs double bracket bash", "bash test operators"]
canonicalUrl: "/docs/shell-scripting/conditionals-if-else-case"
---

# Conditionals: if/else/case

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** `if [[ condition ]]; then ... fi` branches on a test; `case` matches one value
> against multiple patterns more readably than a long `elif` chain. Prefer `[[ ]]` over `[ ]` in
> Bash scripts — it's safer and supports more operators.

## What is it?

The branching constructs that let a script make decisions: `if`/`elif`/`else` for
condition-based branching, and `case` for matching a single value against several patterns.

## Why does it exist?

A script that always runs the same commands in the same order isn't much better than a fixed
alias. Real automation needs to behave differently depending on conditions — whether a file
exists, whether a check passed, which argument was given — and conditionals are how that decision
gets made.

## Where is it used?

Every non-trivial script: checking a precondition before proceeding (does a file/directory
exist?), branching based on an exit code ([Exit Codes and Error Handling](exit-codes-and-error-handling.md)),
or dispatching behavior based on a command-line flag ([Script Arguments](script-arguments.md)).

## How it works

> 📊 Diagram: an if/elif/else flowchart (diamond decision boxes chaining downward) next to a
> case statement's flatter structure (one input value branching directly to multiple labeled
> pattern boxes) — visually contrasting sequential-check style vs pattern-match style for the
> same kind of decision.

**`if`/`elif`/`else`:**

```bash
if [[ -f "$file" ]]; then
    echo "File exists"
elif [[ -d "$file" ]]; then
    echo "It's a directory, not a file"
else
    echo "Not found"
fi
```

**Common test operators (inside `[[ ]]`):**

| Operator | Meaning |
|---|---|
| `-f FILE` | True if FILE exists and is a regular file |
| `-d DIR` | True if DIR exists and is a directory |
| `-z STRING` | True if STRING is empty |
| `-n STRING` | True if STRING is non-empty |
| `==` / `!=` | String equality / inequality |
| `-eq` / `-ne` / `-gt` / `-lt` | Numeric equal / not-equal / greater-than / less-than |
| `&&` / `\|\|` | Logical AND / OR |

**`[ ]` vs `[[ ]]`:** `[ ]` is the older, POSIX-portable test syntax (an alias for the `test`
command); `[[ ]]` is a Bash keyword with more features (pattern matching, no word-splitting
surprises on unquoted variables, `&&`/`||` inside the brackets directly). Prefer `[[ ]]` in
Bash-specific scripts (identified by the `#!/bin/bash` shebang from
[Writing Your First Script](writing-your-first-script.md)); use `[ ]` only when a script must run
under a strict POSIX `sh`.

**`case` — cleaner for matching one value against several patterns:**

```bash
case "$1" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        ;;
esac
```

## Real-world example

A health-check script uses a long `if`/`elif`/`elif`/`elif` chain to handle a status argument
(`healthy`, `degraded`, `down`, anything else), and it's hard to scan at a glance. Rewriting it as
a `case` statement makes each branch visually distinct and adds a default (`*)`) case that catches
any unexpected value — exactly the kind of readability improvement `case` is designed for once a
script is checking one variable against more than two or three fixed values.

## Syntax

```bash
if [[ condition ]]; then
    commands
elif [[ condition ]]; then
    commands
else
    commands
fi

case "$value" in
    pattern1) commands ;;
    pattern2) commands ;;
    *) commands ;;
esac
```

## Commands

No new command example on this page — see
[Variables and Data Types](variables-and-data-types.md) for the last one, or
[Loops](loops-for-while-until.md) for the next.

## Production example

```
$ cat status-check.sh
#!/bin/bash
status=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:8080/health)

case "$status" in
    200) echo "OK" ;;
    5*) echo "Server error: $status" ;;
    *) echo "Unexpected status: $status" ;;
esac

$ ./status-check.sh
OK
```

`case` pattern-matching (`5*`) catches any 5xx status without listing every possible code
individually — something a plain `if`/`elif` chain would need far more lines to express.

## Do / Don't

| Do | Don't |
|---|---|
| Use `[[ ]]` in Bash scripts for safer, more capable tests | Default to `[ ]` out of habit when writing Bash-specific scripts |
| Use `case` when matching one value against 3+ patterns | Chain a long `elif` ladder when `case` would be clearer |
| Always include a default `*)` case or final `else` | Assume every possible input value is explicitly handled |

## Common mistakes

- Using `[ ]` with an unquoted variable that turns out empty, causing a syntax error
  (`[ -z ]` instead of the intended `[ -z "$var" ]`) — `[[ ]]` avoids this specific class of bug.
- Forgetting the terminating `;;` after each `case` pattern's commands, causing fall-through to
  the next pattern unexpectedly.
- Omitting a default `else` or `*)` case, silently doing nothing when an unexpected value appears
  instead of failing loudly or logging it.

## Best practices

- Prefer `[[ ]]` over `[ ]` in any script that already commits to Bash via its shebang.
- Reach for `case` the moment an `if`/`elif` chain checks the same variable against three or more
  fixed values — it's more readable and less error-prone.
- Always handle the unexpected case explicitly (a final `else` or `*)`), even if it just logs an
  error — silent fall-through hides bugs.

## Exercises

1. Write an `if`/`elif`/`else` chain that checks whether a number is positive, negative, or zero.
2. Rewrite that same logic as a `case` statement (or explain why `case` isn't a good fit for
   numeric range checks, if that's the conclusion you reach).
3. Explain the difference in safety between `[ ]` and `[[ ]]` when a variable might be empty.

## Quiz

**Q: Why is [[ ]] generally preferred over [ ] in Bash-specific scripts?**
<details><summary>Show answer</summary>
`[[ ]]` is a Bash keyword with safer handling of unquoted/empty variables, pattern matching
support, and `&&`/`||` usable directly inside the brackets — `[ ]` is the older, more
error-prone POSIX form.
</details>

**Q: When should you reach for case instead of if/elif?**
<details><summary>Show answer</summary>
When checking one value against three or more fixed patterns — it's more readable than a long
elif chain and supports pattern matching (like `5*` for any value starting with 5).
</details>

**Q: What happens if you forget the ;; after a case pattern's commands?**
<details><summary>Show answer</summary>
Execution falls through into the next pattern's commands unexpectedly, rather than exiting the
case statement after that branch.
</details>

## Interview questions

- What's the difference between `[ ]` and `[[ ]]` in Bash? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `if`/`elif`/`else` branches on conditions; `case` matches one value against multiple patterns
  more readably.
- Prefer `[[ ]]` over `[ ]` in Bash scripts for safer variable handling and more operators.
- Always include a default branch (`else` or `*)`) to handle unexpected values explicitly.
- `case` pattern matching (like `5*`) can express ranges of matching values far more concisely
  than an equivalent `if`/`elif` chain.

## Related topics

- [Variables and Data Types](variables-and-data-types.md)
- [Loops: for/while/until](loops-for-while-until.md)
