---
title: "Variables and Data Types"
description: "Declaring, reading, and quoting shell variables — and why Bash's 'everything is a string' model catches people who expect real data types."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/writing-your-first-script"]
relatedTopics: ["writing-your-first-script", "conditionals-if-else-case"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#bash-variable-quoting"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "18-shell-scripting/conditionals-if-else-case"
prevTopic: "18-shell-scripting/writing-your-first-script"
estimatedReadingTime: 7
updatedAt: "2026-07-28"
keywords: ["bash variables", "bash arrays", "bash quoting rules", "bash string vs integer"]
canonicalUrl: "/docs/shell-scripting/variables-and-data-types"
---

# Variables and Data Types

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Software Engineering

> **TL;DR:** Bash variables are set with `name=value` (no spaces around `=`), read with `$name` or
> `"${name}"`, and are all strings by default — even things that look like numbers. Always quote
> variable expansions unless you specifically need word-splitting.

## What is it?

A named storage location for a value, set and read within a script (or interactive shell —
[environment variables](../04-bash/environment-variables.md) were already introduced in Module 04
for that context). This page covers variables as they're used inside script logic specifically:
assignment, quoting, and Bash's limited type system.

## Why does it exist?

Scripts need to store and reuse values — a filename, a count, a flag — without hardcoding them
repeatedly. Bash's variable model is simple by design (no declared types, no compiler), which
makes it fast to write but also the source of some of the most common scripting bugs, almost all
related to quoting.

## Where is it used?

Every script beyond the most trivial one-liner: storing a filename to process, a loop counter, a
computed result, or a flag that changes the script's behavior based on user input
([Script Arguments](script-arguments.md)).

## How it works

> 📊 Diagram: a single box labeled "variable" holding the literal text `"42"` (a string, not a
> number), with two labeled arrows out of it — one to `echo $var` printing `42`, another to
> `((var + 1))` where Bash coerces the string to a number only inside arithmetic context — making
> clear the value itself never changes type, only how it's interpreted does.

**Assignment — no spaces around `=`:**

```bash
name="production"
count=5
```

`name = "production"` (with spaces) is a syntax error in Bash — it's parsed as running a command
called `name` with arguments `=` and `"production"`.

**Reading — `$name` or `"${name}"`:**

```bash
echo $name
echo "${name}"
```

`${name}` (braces) disambiguates where the variable name ends, needed when concatenating
(`"${name}_backup"` vs the ambiguous `"$name_backup"`, which looks for a variable literally named
`name_backup`).

**Everything is a string, by default:**

```bash
count=5
echo $((count + 1))   # arithmetic context: treated as a number, prints 6
echo $count+1          # string context: prints literally "5+1"
```

Bash has no true integer or boolean type — arithmetic context (`$(( ))`, `let`, `[[ ]]` with `-eq`)
is what makes a string like `"5"` behave numerically; outside that context, it's just text.

**Arrays** (Bash-specific, not POSIX `sh`):

```bash
servers=("web-01" "web-02" "web-03")
echo "${servers[0]}"       # web-01
echo "${servers[@]}"       # all elements
echo "${#servers[@]}"      # array length: 3
```

## Real-world example

A script builds a file path with `path=$dir/$filename` and it works fine in testing, but breaks
in production the moment `$filename` contains a space (a real filename like `"backup 2026.tar"`)
— because the unquoted expansion word-splits on the space, turning one argument into two. Quoting
consistently (`"$dir/$filename"`) from the start would have prevented this specific, extremely
common class of bug entirely.

## Syntax

```bash
name=value
echo "$name"
array=(value1 value2 value3)
```

## Commands

No new command example on this page — see [Writing Your First Script](writing-your-first-script.md)
for the last one, or [Conditionals](conditionals-if-else-case.md) for the next.

## Production example

```
$ cat check-disk.sh
#!/bin/bash
threshold=90
usage=$(df / | tail -1 | awk '{print $5}' | tr -d '%')

if [ "$usage" -gt "$threshold" ]; then
    echo "WARNING: disk usage at ${usage}%"
fi

$ ./check-disk.sh
WARNING: disk usage at 93%
```

A realistic pattern: capturing a command's output into a variable, then using it in both string
interpolation (`${usage}%`) and numeric comparison (`-gt`).

## Do / Don't

| Do | Don't |
|---|---|
| Quote variable expansions: `"$var"` | Leave expansions unquoted and risk word-splitting on spaces |
| Use `${var}` braces when concatenating with adjacent text | Write `$var_suffix` expecting it to mean `$var` + `_suffix` |
| Remember variables are strings unless in arithmetic context | Assume `$count + 1` does math outside `$(( ))` |

## Common mistakes

- Adding spaces around `=` in an assignment, causing a syntax error or an attempt to run a
  nonexistent command.
- Leaving a variable expansion unquoted, causing unexpected word-splitting when the value contains
  spaces — the single most common source of "works on my machine, breaks in production" script
  bugs.
- Expecting `$var+1` to perform arithmetic outside of `$(( ))` or `let` — it just concatenates
  text.

## Best practices

- Quote every variable expansion by default (`"$var"`); only leave it unquoted in the rare case
  you deliberately want word-splitting.
- Use `${var}` braces whenever a variable is immediately adjacent to other text, to avoid
  ambiguity about where the variable name ends.
- Use arrays (`servers=(...)`) instead of a single space-separated string when a value is
  conceptually a list — it avoids fragile manual splitting later.

## Exercises

1. Write a script that assigns a variable, then prints it with both `$var` and `"${var}"`.
2. Create a variable containing a value with a space in it, and demonstrate the difference between
   quoted and unquoted expansion in an `echo`.
3. Explain why `count=5; echo $count+1` doesn't print `6`.

## Quiz

**Q: Why does `name = "value"` (with spaces) fail in Bash?**
<details><summary>Show answer</summary>
Bash parses it as running a command named `name` with arguments `=` and `"value"`, since spaces
separate assignment from a command invocation. Assignment requires no spaces around `=`.
</details>

**Q: What's the main risk of leaving a variable expansion unquoted?**
<details><summary>Show answer</summary>
Word-splitting — if the variable's value contains spaces, an unquoted expansion breaks it into
multiple separate words/arguments unexpectedly.
</details>

**Q: Does Bash have a true integer data type?**
<details><summary>Show answer</summary>
No — all variables are strings by default; arithmetic context (`$(( ))`, `let`, numeric `[[ ]]`
comparisons) is what makes a string behave numerically.
</details>

## Interview questions

- Why should you quote variable expansions in Bash scripts? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Assign with `name=value` (no spaces); read with `"$name"` or `"${name}"`.
- Bash variables are strings by default — arithmetic context is what makes them behave
  numerically.
- Always quote variable expansions to avoid word-splitting bugs, the most common class of
  scripting error.
- Arrays (`name=(...)`) handle lists more robustly than manually splitting a single string.

## Related topics

- [Writing Your First Script](writing-your-first-script.md)
- [Conditionals: if/else/case](conditionals-if-else-case.md)
