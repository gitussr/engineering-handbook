---
title: "Functions"
description: "Full shell function syntax for scripts — parameters, return values via exit status, local variables, and why they're the building block of maintainable scripts."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/loops-for-while-until"]
relatedTopics: ["loops-for-while-until", "script-arguments"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#bash-function-return-value"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/script-arguments"
prevTopic: "18-shell-scripting/loops-for-while-until"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["bash function syntax", "bash function return value", "bash local variable", "bash function parameters"]
canonicalUrl: "/docs/shell-scripting/functions"
---

# Functions

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** A shell function groups reusable logic under a name, takes arguments as `$1`, `$2`,
> ... just like a script does, and "returns" through its exit status (0-255) or by printing output
> to be captured — not a return value in the general-purpose-language sense.

## What is it?

A named, reusable block of shell code within a script, callable like a command. This page is the
full scripting-context treatment of functions —
[Module 04](../04-bash/aliases-and-functions.md) already introduced the alias-vs-function
distinction at a basic interactive-shell level; this page owns the complete syntax: parameters,
return values, and variable scope.

## Why does it exist?

Repeating the same block of logic multiple times in one script (or across scripts) is exactly the
duplication functions exist to eliminate — define it once, call it by name wherever it's needed,
and fix bugs in one place instead of several.

## Where is it used?

Any script with logic repeated more than once, or complex enough to benefit from being broken into
named, testable pieces — a logging helper, a retry wrapper, a validation check reused across
several places in the same script.

## How it works

> 📊 Diagram: a function definition box with three labeled parts — parameters flowing in (`$1`,
> `$2`, ...), a body of commands, and an exit status flowing out (0-255) — contrasted with a
> separate small arrow showing "or capture stdout via `$(function_name)`" as the alternative way
> to get a computed value out.

**Definition and calling:**

```bash
greet() {
    echo "Hello, $1"
}

greet "World"    # Hello, World
```

**Parameters work exactly like script arguments** (full detail in
[Script Arguments](script-arguments.md)) — `$1`, `$2`, `$@`, `$#` inside a function refer to that
function's own arguments, not the script's.

**"Return values" — two different mechanisms:**

```bash
# Mechanism 1: exit status (0-255), checked via $? or directly in a condition
is_valid() {
    if [[ -z "$1" ]]; then
        return 1    # failure
    fi
    return 0        # success
}

if is_valid "$name"; then
    echo "Valid"
fi

# Mechanism 2: "returning" a computed value via stdout, captured with $()
get_hostname_short() {
    hostname | cut -d. -f1
}

short=$(get_hostname_short)
```

`return` only sets the exit status (a number 0-255, meant for success/failure signaling, like a
script's own [exit code](exit-codes-and-error-handling.md)) — it cannot return a string or
computed value the way a function in a general-purpose language does. To get a computed value
out, the function prints it and the caller captures that output with `$(...)`.

**`local` — scope variables to the function:**

```bash
process() {
    local result="processing"
    echo "$result"
}
```

Without `local`, a variable assigned inside a function is global by default — it leaks into and
can silently overwrite the caller's variables of the same name, a common source of hard-to-trace
bugs in longer scripts.

## Real-world example

A script defines a `log()` function used a dozen times to print consistently-formatted,
timestamped messages, instead of repeating the same `echo "$(date) ..."` pattern everywhere. When
the team later decides log lines should also go to a file, the change happens in exactly one
place — the `log()` function definition — rather than a dozen scattered `echo` calls, which is the
entire practical case for using functions once a script grows past a few lines.

## Syntax

```bash
function_name() {
    commands
    return EXIT_STATUS   # optional, defaults to the last command's exit status
}
```

## Commands

No new command example on this page — see [Loops](loops-for-while-until.md) for the last one, or
[Script Arguments](script-arguments.md) for the next.

## Production example

```
$ cat deploy-helpers.sh
#!/bin/bash

log() {
    echo "[$(date '+%H:%M:%S')] $1"
}

check_service() {
    local service="$1"
    if systemctl is-active --quiet "$service"; then
        return 0
    else
        return 1
    fi
}

log "Checking nginx..."
if check_service "nginx"; then
    log "nginx is running"
else
    log "nginx is NOT running"
    exit 1
fi
```

Two functions in real use: `log()` for consistent output formatting, `check_service()` using
`return` as a boolean-style success/failure signal that the caller checks directly in an `if`.

## Do / Don't

| Do | Don't |
|---|---|
| Use `local` for variables that shouldn't leak outside the function | Assume function-local variables are automatically scoped without `local` |
| Use `return` for success/failure signaling (0-255) | Try to `return` a string or a computed non-status value |
| Capture computed values via `$(function_name)` | Expect a function's `echo` output to be its "return value" without capturing it |

## Common mistakes

- Forgetting `local`, causing a variable inside a function to silently overwrite a same-named
  variable in the calling script — a bug that's easy to introduce and hard to trace.
- Trying to `return` a string or number directly as a "return value," not realizing `return` only
  sets a 0-255 exit status, not an arbitrary value.
- Calling a function that prints its result without capturing that output (`$(...)`), then being
  confused why a variable assignment didn't work as expected.

## Best practices

- Default to `local` for every variable declared inside a function unless there's a specific
  reason it needs to be global.
- Use `return` purely for success/failure signaling, and `echo` + `$(...)` capture for any
  function that needs to produce a computed value.
- Extract any logic repeated more than once (or complex enough to obscure the script's main flow)
  into a named function — it's the single highest-leverage readability improvement available in
  shell scripting.

## Exercises

1. Write a function that takes a filename as an argument and returns success (0) if it exists,
   failure (1) otherwise.
2. Write a function that computes and prints a value, then capture that value into a variable in
   the calling script using `$(...)`.
3. Demonstrate (with a short example) what happens to a variable set inside a function without
   `local`.

## Quiz

**Q: What does return actually set in a Bash function?**
<details><summary>Show answer</summary>
The function's exit status — a number from 0 to 255, meant for success/failure signaling, not an
arbitrary return value.
</details>

**Q: How do you get a computed value out of a function, if not through return?**
<details><summary>Show answer</summary>
Have the function print the value with `echo`, and capture it in the caller using command
substitution: `result=$(function_name)`.
</details>

**Q: What happens to a variable assigned inside a function without the local keyword?**
<details><summary>Show answer</summary>
It becomes global by default, potentially overwriting a same-named variable outside the function —
`local` is required to scope it to the function only.
</details>

## Interview questions

- How does a Bash function "return" a value, and why is that different from most languages? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Functions group reusable logic under a name, taking arguments the same way scripts do.
- `return` only sets a 0-255 exit status; use `echo` + `$(...)` capture to get a computed value
  out.
- `local` scopes a variable to the function — without it, variables are global by default.
- Extracting repeated logic into functions is one of the most effective ways to keep a growing
  script maintainable.

## Related topics

- [Loops: for/while/until](loops-for-while-until.md)
- [Script Arguments](script-arguments.md)
