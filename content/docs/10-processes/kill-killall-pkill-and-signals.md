---
title: "kill, killall, pkill, and Signals"
description: "kill doesn't just kill — it sends a signal, and which signal you send changes everything. SIGTERM asks nicely; SIGKILL doesn't ask at all."
module: "10-processes"
moduleTitle: "Processes"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["10-processes/ps-top-htop"]
relatedTopics: ["foreground-vs-background-processes"]
relatedCommands: ["kill", "killall", "pkill"]
careerRelevance: ["devops", "sre", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#sigterm-vs-sigkill"]
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "signal(7) man page", "url": "https://man7.org/linux/man-pages/man7/signal.7.html"}]
nextTopic: "10-processes/foreground-vs-background-processes"
prevTopic: "10-processes/ps-top-htop"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["sigterm vs sigkill", "kill command linux", "killall vs pkill", "kill -9"]
canonicalUrl: "/docs/processes/kill-killall-pkill-and-signals"
---

# kill, killall, pkill, and Signals

🟢 Must Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** `kill` doesn't inherently "kill" anything — it sends a signal, and the process
> decides how to respond (or doesn't get the chance to). `SIGTERM` (default, `kill PID`) asks a
> process to shut down cleanly. `SIGKILL` (`kill -9 PID`) terminates it immediately, with no
> chance to clean up — a last resort, not a first instinct.

## What is it?

A signal is a limited, standardized message sent to a process by the kernel or another process —
`kill`, `killall`, and `pkill` are three commands for sending signals, differing only in how you
specify the target (by PID, by exact name, or by name pattern).

## Why does it exist?

Processes often need to be told to stop, reload their config, or handle an unusual condition, and
signals are the standardized mechanism for that — deliberately limited and simple (a small,
fixed set of signal types) so every process, regardless of what it's written in, can handle them
consistently.

## Where is it used?

Stopping a stuck process, gracefully restarting a service, sending a reload signal to a
long-running daemon without fully restarting it — an extremely common category of operational
task.

## How it works

> 📊 Diagram: two signal flows contrasted — `SIGTERM`: kernel delivers signal → process's own
> shutdown handler runs (closes files, saves state) → process exits cleanly. `SIGKILL`: kernel
> terminates the process immediately at the kernel level → no handler runs at all → process is
> simply gone, mid-operation.

| Signal | Number | Default `kill` behavior | Meaning |
|---|---|---|---|
| `SIGTERM` | 15 | `kill PID` (default, no flag needed) | "Please shut down" — the process can catch this and clean up before exiting |
| `SIGKILL` | 9 | `kill -9 PID` | Immediate termination at the kernel level — the process cannot catch, ignore, or clean up in response |
| `SIGHUP` | 1 | `kill -1 PID` | Historically "the terminal hung up"; commonly repurposed by daemons to mean "reload your config" |
| `SIGINT` | 2 | `kill -2 PID` | What `Ctrl+C` sends — interrupt |

- **`kill PID`** — send a signal to one specific PID (SIGTERM by default).
- **`killall name`** — send a signal to every process matching an exact name.
- **`pkill pattern`** — send a signal to every process matching a name pattern (more flexible
  matching than `killall`).

**Why SIGTERM before SIGKILL**: SIGTERM gives a process the chance to close files, flush data,
and release resources cleanly. SIGKILL gives it no chance at all — the kernel just removes it.
Reaching for `kill -9` immediately, instead of trying plain `kill` (SIGTERM) first, is a genuinely
common and consequential mistake.

## Real-world example

An engineer needs to stop a stuck application. `kill PID` (SIGTERM) is tried first, giving the
app a chance to close its database connections and write any pending data cleanly — if it doesn't
respond within a reasonable time, only then does `kill -9 PID` (SIGKILL) become appropriate. Going
straight to `-9` on a database-backed application risks corrupted or lost data that a clean
SIGTERM shutdown would have avoided.

## Commands

- [`kill`](../../commands/kill.md) — full syntax and examples
- [`killall`](../../commands/killall.md) — full syntax and examples
- [`pkill`](../../commands/pkill.md) — full syntax and examples

## Production example

```
$ kill 1234
$ sleep 5
$ ps -p 1234
# still running?
$ kill -9 1234
```

A deliberate escalation — SIGTERM first, SIGKILL only if the process didn't respond.

## Do / Don't

| Do | Don't |
|---|---|
| Try plain `kill` (SIGTERM) before escalating to `kill -9` | Reach for `kill -9` as a first instinct |
| Use `SIGHUP` for a graceful config reload when a daemon supports it | Restart a whole service when a reload signal would do |
| Verify the target PID before sending a signal | Send a signal based on a guessed or unverified PID |

## Common mistakes

- Reaching for `kill -9` immediately instead of trying SIGTERM first — risks data loss or
  corruption in anything the process didn't get to clean up.
- Confusing `killall` (exact name match) with `pkill` (pattern match) and hitting more or fewer
  processes than intended.
- Not realizing many daemons repurpose `SIGHUP` to mean "reload config" rather than its historical
  literal meaning — checking a specific daemon's documented signal behavior matters.

## Best practices

- Default to plain `kill` (SIGTERM) and only escalate to `-9` if the process doesn't respond
  within a reasonable time.
- Check a daemon's documentation for which signals it specifically handles (many support SIGHUP
  for reload) before assuming default behavior.
- Verify the exact PID (via `ps`, the previous topic) before sending any signal, especially `-9`.

## Exercises

1. Start a long-running test process, send it SIGTERM, and observe whether it exits cleanly.
2. Repeat with SIGKILL and note there's no opportunity for cleanup.
3. Explain in one sentence why SIGTERM is generally tried before SIGKILL.

## Quiz

**Q: Can a process catch and respond to SIGKILL the way it can to SIGTERM?**
<details><summary>Show answer</summary>
No — SIGKILL terminates the process immediately at the kernel level; it cannot be caught,
ignored, or handled in any way.
</details>

**Q: What's the difference between `killall` and `pkill`?**
<details><summary>Show answer</summary>
`killall` matches an exact process name; `pkill` matches against a broader pattern, giving more
flexible (and potentially riskier, if too broad) matching.
</details>

## Interview questions

- Why would you send SIGTERM before SIGKILL when stopping a process? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `kill` sends a signal; it doesn't inherently terminate anything by itself.
- SIGTERM (default) asks a process to shut down cleanly; SIGKILL (`-9`) terminates immediately
  with no cleanup chance.
- `kill` targets a PID, `killall` an exact name, `pkill` a name pattern.
- Many daemons repurpose SIGHUP as a graceful config-reload signal.

## Further Reading

- [signal(7) man page](https://man7.org/linux/man-pages/man7/signal.7.html)

## Related topics

- [Foreground vs Background Processes](foreground-vs-background-processes.md)
