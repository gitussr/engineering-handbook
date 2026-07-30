---
title: "logrotate"
description: "Automatically rotating, compressing, and eventually deleting log files so they don't grow forever and fill the disk."
module: "16-logs"
moduleTitle: "Logs"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["16-logs/syslog-and-rsyslog"]
relatedTopics: ["var-log-directory-structure"]
relatedCommands: ["logrotate"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#logrotate-explained"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "16-logs/centralized-logging-concepts"
prevTopic: "16-logs/syslog-and-rsyslog"
estimatedReadingTime: 6
updatedAt: "2026-07-27"
keywords: ["logrotate config explained", "logrotate.d examples", "logrotate compress", "log file grows forever"]
canonicalUrl: "/docs/logs/logrotate"
---

# logrotate

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `logrotate` runs on a schedule (typically daily, via cron/systemd timer) and rotates
> log files per rules in `/etc/logrotate.conf` and `/etc/logrotate.d/*` — renaming, compressing,
> and eventually deleting old ones, so a log file doesn't grow forever and fill the disk.

## What is it?

A utility that automatically rotates (renames and starts fresh), compresses, and eventually
deletes log files based on configured rules — the mechanism that keeps the file-based logs from
[/var/log](var-log-directory-structure.md) from growing without bound.

## Why does it exist?

A log file that's never rotated grows forever, eventually consuming all available disk space
([Module 15's disk usage topic](../15-storage/disk-usage-df-du.md)) and becoming unwieldy to
search through. `logrotate` exists to automate the tedious, easy-to-forget process of managing
that growth — rotating on a schedule or size threshold, compressing old copies, and deleting the
oldest ones once a retention limit is reached.

## Where is it used?

Every production system running services that produce file-based logs — web servers, package
managers, custom applications — almost always has `logrotate` rules covering them, either
provided by the package itself or configured manually.

## How it works

> 📊 Diagram: `app.log` shown being rotated — renamed to `app.log.1` (with older
> `app.log.1`→`app.log.2`, etc., shifting down), a fresh empty `app.log` created in its place, and
> the oldest numbered file beyond the retention count deleted entirely — with compression applied
> to older rotated copies.

```
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```

| Directive | Meaning |
|---|---|
| `daily` (or `weekly`, `size 100M`, etc.) | How often/when to rotate |
| `rotate N` | Keep N old rotated copies before deleting the oldest |
| `compress` | Compress rotated copies (typically gzip) to save space |
| `missingok` | Don't error if the log file doesn't exist |
| `notifempty` | Don't rotate an empty log file |

`logrotate` itself typically runs once per day via cron or a systemd timer, checking every
configured rule and rotating whatever's due.

## Real-world example

A custom application ships without any `logrotate` configuration, and its log file grows
unchecked for months until it fills the disk entirely, causing an outage
([Module 15's disk usage investigation pattern](../15-storage/disk-usage-df-du.md) would have
caught this earlier). Adding a proper `/etc/logrotate.d/myapp` rule with `daily`, `rotate 14`, and
`compress` afterward ensures the same failure mode can't recur — the log now rotates and
compresses automatically, with old copies aging out on a fixed schedule.

## Commands

- [`logrotate`](../../commands/logrotate.md) — full syntax and examples

## Production example

```
$ cat /etc/logrotate.d/myapp
/var/log/myapp/app.log {
    daily
    rotate 14
    compress
    missingok
    notifempty
}

$ sudo logrotate -d /etc/logrotate.d/myapp
```

`-d` (debug/dry-run) shows what `logrotate` *would* do without actually rotating anything —
useful for testing a new configuration safely.

## Do / Don't

| Do | Don't |
|---|---|
| Add a `logrotate` rule for any custom application that logs to a file | Assume a custom application's logs are rotated automatically without configuration |
| Test a new rule with `-d` (dry run) before relying on it | Deploy an untested rotation rule directly to production |
| Set a `rotate` count that balances retention needs against disk usage | Set an unbounded or excessively large retention count without considering disk impact |

## Common mistakes

- Assuming every application's logs are rotated automatically by default, when custom or
  third-party applications often need their own explicit `logrotate.d` rule.
- Not testing a new rule with `-d` before it runs for real, discovering a configuration mistake
  only after logs are unexpectedly rotated (or not rotated) in production.
- Setting `rotate` to an excessively high count without considering the cumulative disk space
  impact of that many retained (even compressed) copies.

## Best practices

- Add a `logrotate.d` rule for every custom application that logs to a file — don't assume it's
  handled automatically.
- Always test with `logrotate -d` before trusting a new or modified rule.
- Balance the `rotate` retention count against actual disk capacity and how far back logs
  realistically need to be searchable.

## Exercises

1. Check `/etc/logrotate.d/` on a system you have access to and identify at least one
   application's rotation rule.
2. Run `logrotate -d` against a configuration file and read the dry-run output.
3. Explain in one sentence what would happen to a log file with no `logrotate` rule at all.

## Quiz

**Q: What does the `rotate N` directive control?**
<details><summary>Show answer</summary>
How many old rotated copies of a log file to keep before the oldest is deleted entirely.
</details>

**Q: How can you safely test a new `logrotate` configuration without actually rotating anything?**
<details><summary>Show answer</summary>
Run `logrotate -d` (debug/dry-run) against the configuration file — it shows what would happen
without making any actual changes.
</details>

## Interview questions

- What problem does `logrotate` solve, and what happens to a log file if it's never configured? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `logrotate` automatically rotates, compresses, and eventually deletes log files per configured
  rules.
- Rules live in `/etc/logrotate.conf` and `/etc/logrotate.d/*`, one file per application/service
  is common.
- `rotate N` controls retention; `compress` saves space on older copies.
- Always test a new or modified rule with `logrotate -d` before trusting it in production.

## Related topics

- [Centralized Logging Concepts](centralized-logging-concepts.md)
- [/var/log Directory Structure](var-log-directory-structure.md)
