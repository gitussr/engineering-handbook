---
title: "Regular Expressions Basics"
description: "The small set of regex metacharacters that unlock precise pattern matching in grep, sed, and beyond — not a full regex course, the 20% that covers 80% of real use."
module: "06-files"
moduleTitle: "Files"
stage: "beginner"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["06-files/cut-sort-uniq-wc"]
relatedTopics: ["grep", "sed-and-awk"]
relatedCommands: []
careerRelevance: ["devops", "backend", "cybersecurity", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#regex-basics"]
relatedCheatsheet: "regex"
furtherReading: [{"label": "regex101.com (interactive regex tester)", "url": "https://regex101.com"}]
nextTopic: "07-permissions/permission-model"
prevTopic: "06-files/cut-sort-uniq-wc"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["regex basics linux", "grep regex", "regex metacharacters", "basic vs extended regex"]
canonicalUrl: "/docs/files/regular-expressions-basics"
---

# Regular Expressions Basics

🟡 Good to Know · Relevant for: DevOps · Backend · Cybersecurity · Software Engineering

> **TL;DR:** A regular expression is a pattern language for matching text. `.` any character,
> `*` zero or more of the previous, `^`/`$` start/end of line, `[abc]` any one of a set,
> `\d`/`\w` digit/word character (with the right regex mode). This page covers the 20% of regex
> that handles 80% of real `grep`/`sed` use — not a full regex course.

## What is it?

A regular expression (regex) is a compact pattern language for matching text, used throughout
this documentation already — `grep`'s pattern matching, `sed`'s substitutions, and validation
logic in scripts all rely on it.

## Why does it exist?

Plain text matching ("does this line contain the exact string 'error'") is too rigid for most
real searches — you often need "a line that starts with a timestamp," "any 3-digit number," or
"an email-like pattern." Regex is the standard, portable way to express that flexibility, instead
of every tool inventing its own pattern syntax.

## Where is it used?

- `grep` (especially `-E` for extended regex)
- `sed` substitutions
- Input validation in shell scripts and virtually every programming language
- Log parsing and monitoring alert rules

## How it works

> 📊 Diagram: a single example string (`error: connection timeout at 10:42:07`) with several
> regex patterns overlaid, each highlighting what it would match — `[0-9]+` highlighting the
> numbers, `^error` highlighting just the start, `.*timeout.*` highlighting the whole line — to
> make abstract metacharacters concrete against one real example.

| Metacharacter | Meaning | Example |
|---|---|---|
| `.` | Any single character | `a.c` matches `abc`, `axc` |
| `*` | Zero or more of the preceding character | `ab*c` matches `ac`, `abc`, `abbbc` |
| `^` | Start of line | `^error` matches lines starting with "error" |
| `$` | End of line | `timeout$` matches lines ending with "timeout" |
| `[abc]` | Any one character from the set | `[aeiou]` matches any single vowel |
| `[0-9]` | Any digit (range notation) | `[0-9]+` (with extended regex) matches one or more digits |
| `\` | Escape a metacharacter to match it literally | `\.` matches a literal period, not "any character" |

**Basic vs. extended regex**: plain `grep` uses Basic Regular Expressions (BRE), where some
metacharacters (like `+` and `|`) need a backslash to work as metacharacters. `grep -E` (or
`egrep`) uses Extended Regular Expressions (ERE), where they work directly. This distinction
trips up beginners constantly — if a pattern "should work" but doesn't, checking BRE vs. ERE
mode is often the fix.

## Real-world example

An engineer needs to find every log line containing a 3-digit HTTP status code starting with 4
or 5 (client/server errors): `grep -E "\s[45][0-9]{2}\s" access.log` — a precise pattern that a
plain text search for "404" alone would miss entirely (it would also match "40400" or a
timestamp containing those digits by coincidence), demonstrating exactly why regex precision
matters over naive substring matching.

## Commands

No new canonical command page on this page — regex is a pattern language used by
[`grep`](../../commands/grep.md) and `sed`, not a command itself.

## Production example

```
$ grep -E "^[0-9]{4}-[0-9]{2}-[0-9]{2}" app.log
$ grep -E "\s(4|5)[0-9]{2}\s" access.log
```

The first matches lines starting with a `YYYY-MM-DD` date; the second matches HTTP 4xx/5xx status
codes surrounded by whitespace.

## Do / Don't

| Do | Don't |
|---|---|
| Use `-E` (or `egrep`) when using `+`, `|`, `{n}`, or similar metacharacters | Assume every metacharacter works the same in basic and extended regex |
| Test a regex against sample text before relying on it in production | Deploy an untested regex pattern into a script or alert rule |
| Escape special characters (`\.`) when you mean them literally | Forget that `.` matches any character, not just a literal period |

## Common mistakes

- Writing a pattern in plain `grep` that uses ERE-only syntax (`+`, `|`, `{n}`) without `-E`, and
  being confused why it doesn't behave as expected.
- Forgetting `.` matches any character, not literally a period — `192.168.1.1` as a plain pattern
  would also match `192a168a1a1`, unless the periods are escaped (`192\.168\.1\.1`).
- Writing an overly broad pattern that matches more than intended, silently including unwanted
  results.

## Best practices

- Test regex patterns against sample data before trusting them in a script, alert rule, or
  production `sed`/`grep` command — a regex testing tool (see Further Reading) helps a lot here.
- Default to `-E` for anything beyond the simplest literal search, to avoid basic-vs-extended
  syntax surprises.
- Escape literal periods, especially in IP addresses and version numbers, where an unescaped `.`
  would match too broadly.

## Exercises

1. Write a regex that matches lines starting with a 4-digit year.
2. Write a regex that matches a 3-digit number surrounded by whitespace.
3. Explain in one sentence the practical difference between basic and extended regex in `grep`.

## Quiz

**Q: What does `.` match in regex?**
<details><summary>Show answer</summary>
Any single character — not literally a period. To match a literal period, escape it: `\.`
</details>

**Q: Why might a regex pattern using `+` fail in plain `grep` but work with `grep -E`?**
<details><summary>Show answer</summary>
Plain `grep` uses Basic Regular Expressions, where `+` isn't a metacharacter by default;
`grep -E` enables Extended Regular Expressions, where it works as "one or more" directly.
</details>

## Interview questions

- Write a regex to match a valid-looking IP address pattern. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Regex is the standard pattern language behind `grep`, `sed`, and countless other tools.
- A small set of metacharacters (`.`, `*`, `^`, `$`, `[ ]`) covers most everyday needs.
- Basic vs. extended regex mode (`-E`) is a common, avoidable source of "why doesn't my pattern
  work" confusion.
- Always test a regex against real sample data before trusting it in production.

## Further Reading

- [regex101.com — interactive regex tester](https://regex101.com)

## Related topics

- [grep](grep.md)
- [sed and awk](sed-and-awk.md)
- Cheat sheet: [Regex](../../cheatsheets/regex.md)
