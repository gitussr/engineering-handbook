---
title: "Loops: for/while/until"
description: "Repeating work in a script — for over a fixed list, while for a condition-driven loop, and until as its inverse — plus break and continue."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/conditionals-if-else-case"]
relatedTopics: ["conditionals-if-else-case", "functions"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#for-vs-while-loop"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/functions"
prevTopic: "18-shell-scripting/conditionals-if-else-case"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["bash for loop", "bash while loop", "bash until loop", "bash break continue"]
canonicalUrl: "/docs/shell-scripting/loops-for-while-until"
---

# Loops: for/while/until

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** `for` iterates over a fixed list (files, arguments, a range); `while` repeats as long
> as a condition is true; `until` repeats until a condition becomes true. `break` exits a loop
> early, `continue` skips to the next iteration.

## What is it?

The repetition constructs that let a script process multiple items or repeat an action until a
condition changes, without duplicating the same commands manually.

## Why does it exist?

Processing a list of files, retrying a check until a service comes up, or repeating a task a fixed
number of times all need repetition without hand-copying the same block of code for every item —
exactly the problem loops solve.

## Where is it used?

Processing every file in a directory, retrying a health check with a delay until a service
responds, iterating over server names to run the same deploy step on each, or reading a file
line by line.

## How it works

> 📊 Diagram: three loop shapes side by side — `for` shown iterating a fixed list left to right
> (three boxes, one arrow per box), `while` shown as a loop-back arrow re-checking a condition
> before each pass, `until` shown as the same loop-back shape but with the condition inverted
> (loop continues while false, exits when true).

**`for` — iterate over a fixed list:**

```bash
for server in web-01 web-02 web-03; do
    echo "Deploying to $server"
done

for file in *.log; do
    echo "Processing $file"
done
```

**`while` — repeat while a condition is true:**

```bash
count=0
while [[ $count -lt 5 ]]; do
    echo "Attempt $count"
    ((count++))
done
```

**`until` — repeat until a condition becomes true (the inverse of `while`):**

```bash
until curl -sf http://localhost:8080/health > /dev/null; do
    echo "Waiting for service..."
    sleep 2
done
echo "Service is up"
```

**`break` and `continue`:**

```bash
for n in 1 2 3 4 5; do
    if [[ $n -eq 3 ]]; then
        continue    # skip 3, move to next iteration
    fi
    if [[ $n -eq 5 ]]; then
        break       # stop the loop entirely
    fi
    echo "$n"
done
```

## Real-world example

A deploy script needs to wait for a newly-started service to become reachable before running the
next step. An `until` loop polling the health endpoint every few seconds, with a fixed maximum
retry count to avoid looping forever if the service never comes up, is the standard pattern —
`until` reads naturally here ("keep trying *until* it's healthy"), where the equivalent `while
!condition` would be less readable for the same logic.

## Syntax

```bash
for VAR in LIST; do commands; done
while [[ condition ]]; do commands; done
until [[ condition ]]; do commands; done
```

## Commands

No new command example on this page — see
[Conditionals](conditionals-if-else-case.md) for the last one, or
[Functions](functions.md) for the next.

## Production example

```
$ cat wait-for-service.sh
#!/bin/bash
max_attempts=10
attempt=0

until curl -sf http://localhost:8080/health > /dev/null 2>&1; do
    ((attempt++))
    if [[ $attempt -ge $max_attempts ]]; then
        echo "Service failed to become healthy after $max_attempts attempts"
        exit 1
    fi
    echo "Waiting for service (attempt $attempt/$max_attempts)..."
    sleep 3
done
echo "Service is healthy"

$ ./wait-for-service.sh
Waiting for service (attempt 1/10)...
Waiting for service (attempt 2/10)...
Service is healthy
```

A bounded retry loop — critical in production so a broken dependency doesn't loop forever, tying
directly into [Exit Codes and Error Handling](exit-codes-and-error-handling.md).

## Do / Don't

| Do | Don't |
|---|---|
| Bound retry loops with a maximum attempt count | Write an `until`/`while` retry loop with no exit condition, risking an infinite hang |
| Use `for` when iterating a known, fixed list | Force a `while` counter loop when `for` says the same thing more clearly |
| Use `break`/`continue` to keep loop logic readable | Nest deep conditionals inside a loop when `continue` would skip cleanly |

## Common mistakes

- Writing a `while`/`until` retry loop with no maximum attempt count, risking an infinite loop if
  the condition never resolves (a service that never comes up hangs the script forever).
- Forgetting to increment a counter variable inside a `while` loop, causing an infinite loop.
- Using `for var in $list` on an unquoted variable containing spaces, causing unexpected
  word-splitting during iteration (the same class of bug from
  [Variables and Data Types](variables-and-data-types.md), applied inside a loop).

## Best practices

- Always bound a condition-driven retry loop with a maximum attempt count and a clear failure
  path (`exit 1` with a message) when it's exceeded.
- Prefer `for` for known, fixed iteration and `while`/`until` for condition-driven repetition —
  choosing the right construct signals intent to the next reader.
- Add a short `sleep` inside polling loops (like a health-check retry) to avoid hammering a
  service with requests every fraction of a second.

## Exercises

1. Write a `for` loop that prints the numbers 1 through 5.
2. Write a `while` loop that counts down from 5 to 1.
3. Write an `until` loop that waits (with a bounded retry count) for a file to exist.

## Quiz

**Q: What's the key difference between while and until?**
<details><summary>Show answer</summary>
`while` repeats as long as its condition is true; `until` repeats as long as its condition is
false, stopping once it becomes true — they're inverses of each other.
</details>

**Q: Why is it important to bound a retry loop with a maximum attempt count?**
<details><summary>Show answer</summary>
Without a bound, a condition that never becomes true (a service that never comes up) causes the
script to hang indefinitely instead of failing with a clear error.
</details>

**Q: What does continue do inside a loop, as opposed to break?**
<details><summary>Show answer</summary>
`continue` skips the rest of the current iteration and moves to the next one; `break` exits the
loop entirely, running no further iterations.
</details>

## Interview questions

- How would you write a retry loop that waits for a service to become healthy? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `for` iterates a fixed list; `while` repeats while true; `until` repeats while false.
- `break` exits a loop entirely; `continue` skips to the next iteration.
- Always bound condition-driven loops with a maximum retry count to avoid infinite hangs.
- Choosing the right loop construct for the situation signals intent clearly to future readers.

## Related topics

- [Conditionals: if/else/case](conditionals-if-else-case.md)
- [Functions](functions.md)
