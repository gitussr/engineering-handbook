---
title: "crontab Syntax"
description: "How to read and write the five time fields plus command that make up a crontab line, including special strings and step/range/list values."
module: "17-cron"
moduleTitle: "Cron"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["17-cron/task-scheduling-overview"]
relatedTopics: ["task-scheduling-overview", "crontab-e-l-r"]
relatedCommands: ["crontab"]
careerRelevance: ["devops", "linux-administrator", "sre", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#crontab-syntax"]
relatedCheatsheet: "cron"
furtherReading: [{"label": "crontab(5) man page", "url": "https://man7.org/linux/man-pages/man5/crontab.5.html"}]
nextTopic: "17-cron/crontab-e-l-r"
prevTopic: "17-cron/task-scheduling-overview"
estimatedReadingTime: 8
updatedAt: "2026-07-27"
keywords: ["crontab syntax", "cron time fields", "cron special strings", "cron step values", "cron every 5 minutes"]
canonicalUrl: "/docs/cron/crontab-syntax"
---

# crontab Syntax

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE · Cloud

> **TL;DR:** A crontab line is five time fields (minute, hour, day-of-month, month, day-of-week)
> followed by the command to run. `*` means "every value"; `*/N`, ranges, and comma lists narrow
> that down.

## What is it?

The line format every `cron` job is written in — five whitespace-separated time fields that
define when a job runs, followed by the command itself.

## Why does it exist?

A schedule needs an unambiguous, machine-parseable way to express "every day at 2am" or "every 15
minutes" or "the first of every month." The five-field format is that expression, and it's been
stable and portable across Unix-like systems for decades — any engineer who has seen one crontab
can read another.

## Where is it used?

Every `cron` job, whether in a user's personal crontab (edited with `crontab -e`, next page) or a
system-wide file under `/etc/cron.d/` (covered in [System-wide cron](system-wide-cron.md)), uses
this exact five-field format.

## How it works

> 📊 Diagram: a single crontab line broken into six labeled boxes left to right — minute (0-59),
> hour (0-23), day-of-month (1-31), month (1-12), day-of-week (0-7, both 0 and 7 = Sunday), and
> command — with an example line (`0 2 * * *`) mapped onto the first five boxes.

**The five fields, in order:**

| Field | Allowed values | Meaning |
|---|---|---|
| Minute | 0-59 | Minute of the hour |
| Hour | 0-23 | Hour of the day (24-hour) |
| Day of month | 1-31 | Day of the month |
| Month | 1-12 | Month of the year |
| Day of week | 0-7 | Day of the week (`0` and `7` both mean Sunday) |

**Value syntax within a field:**

| Syntax | Meaning | Example |
|---|---|---|
| `*` | Every value | `* * * * *` = every minute |
| `N` | Exact value | `0 2 * * *` = 2:00 AM daily |
| `N-M` | Range | `9-17 * * * *` = every minute, hours 9 through 17 |
| `N,M,...` | List | `0 9,13,18 * * *` = at 9:00, 13:00, and 18:00 |
| `*/N` | Step | `*/15 * * * *` = every 15 minutes |
| Special strings | Shorthand | `@daily`, `@hourly`, `@weekly`, `@monthly`, `@yearly`, `@reboot` (run once at startup) |

All five fields are combined with AND logic, except day-of-month and day-of-week specifically —
if both are restricted (not `*`), cron runs the job when *either* matches, not only when both
match simultaneously.

## Real-world example

An engineer means to schedule a backup for 2:30 AM and intends "hour 2, minute 30," but writes
`2 30 * * *` instead of the correct `30 2 * * *` — swapping which field is which. Cron rejects `30`
as an invalid hour value, and the job never gets installed at all, silently. Reading each field in
its fixed position — minute first, hour second — before troubleshooting further catches exactly
this class of mistake, which is a common source of "why didn't my job run" tickets.

## Syntax

```
MINUTE HOUR DAY_OF_MONTH MONTH DAY_OF_WEEK COMMAND
```

## Commands

See [`crontab`](../../commands/crontab.md) for the command that edits and lists these lines.

## Production example

```
$ crontab -l
# Run backup nightly at 2:30 AM
30 2 * * * /usr/local/bin/backup.sh

# Run health check every 5 minutes
*/5 * * * * /usr/local/bin/healthcheck.sh

# Run report generation at 9am on weekdays only
0 9 * * 1-5 /usr/local/bin/weekly-report.sh
```

Three real schedules read directly from the five-field format: a fixed daily time, a step
interval, and a weekday-only range.

## Do / Don't

| Do | Don't |
|---|---|
| Read fields in fixed order — minute, hour, day-of-month, month, day-of-week | Guess field order from memory without checking, especially under time pressure |
| Use `*/N` for even intervals like "every 15 minutes" | Write out `0,15,30,45` when `*/15` says the same thing more clearly |
| Remember day-of-month and day-of-week are OR'd together when both are restricted | Assume all five fields are always AND'd together |

## Common mistakes

- Swapping the minute and hour fields, producing either an invalid schedule (rejected) or a
  schedule that runs at the wrong time.
- Forgetting that restricting both day-of-month and day-of-week produces OR logic, not AND —
  leading to a job firing more often than intended.
- Using `*/N` syntax incorrectly (e.g. `*/60` in the minute field, which doesn't mean "once an
  hour" the way it might seem — `0 * * * *` is the correct way to express that).

## Best practices

- Use special strings (`@daily`, `@hourly`) when they match the intended schedule exactly — they
  self-document intent better than the equivalent five-field form.
- Comment crontab lines (`#`) explaining what each job does, especially in shared system crontabs
  where the next engineer won't have context.
- Test a new schedule's logic against a crontab syntax reference or dry-run tool before trusting
  it unattended, particularly for OR-logic day-of-month/day-of-week combinations.

## Exercises

1. Write a crontab line that runs a command every 10 minutes.
2. Write a crontab line that runs a command at 6:00 PM every Friday.
3. Explain what `0 0 1,15 * *` schedules, in plain English.

## Quiz

**Q: What does `*/15 * * * *` schedule?**
<details><summary>Show answer</summary>
Every 15 minutes, every hour, every day — a step value in the minute field.
</details>

**Q: If a crontab line restricts both day-of-month and day-of-week, how are they combined?**
<details><summary>Show answer</summary>
With OR logic — the job runs if either field matches, not only when both match simultaneously.
</details>

**Q: What does `@reboot` schedule?**
<details><summary>Show answer</summary>
The command runs once, at system startup, rather than on a recurring time-based schedule.
</details>

## Interview questions

- Walk through how you'd write a crontab line for "every weekday at 8am." →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A crontab line is five time fields (minute, hour, day-of-month, month, day-of-week) plus a
  command.
- `*` means every value; ranges, lists, and step values (`*/N`) narrow that down.
- Day-of-month and day-of-week are OR'd, not AND'd, when both are restricted — a common source of
  confusion.
- Special strings like `@daily` and `@reboot` cover common cases more readably than the five-field
  equivalent.

## Related topics

- [crontab -e, -l, -r](crontab-e-l-r.md)
- [Task Scheduling Overview](task-scheduling-overview.md)
