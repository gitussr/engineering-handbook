---
title: "Service Won't Start"
description: "A systematic order for diagnosing a systemd service that fails to start — status, journal logs, config validation, and dependency checks, in the order that actually finds the cause fastest."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["11-services/systemctl-start-stop-enable-disable-status", "11-services/journalctl-basics"]
relatedTopics: ["33-troubleshooting/high-cpu-memory-issues", "33-troubleshooting/permission-denied-issues"]
relatedCommands: ["systemctl", "journalctl"]
careerRelevance: ["linux-administrator", "devops", "sre"]
relatedLabs: ["beginner/diagnose-and-fix-a-service-that-wont-start"]
relatedInterviewQuestions: ["scenario-based#systemd-service-wont-start-after-reboot"]
relatedCheatsheet: "systemctl"
furtherReading: []
nextTopic: "33-troubleshooting/permission-denied-issues"
prevTopic: "33-troubleshooting/high-cpu-memory-issues"
estimatedReadingTime: 7
updatedAt: "2026-07-30"
keywords: ["service wont start linux", "systemctl start failed", "journalctl debug service", "systemd troubleshooting"]
canonicalUrl: "/docs/troubleshooting/service-wont-start"
---

# Service Won't Start

🟢 Must Know · Relevant for: Linux Administrator · DevOps · SRE

> **TL;DR:** `systemctl status` gives a quick summary; `journalctl -u {service}` gives the actual
> error. Read the literal error message before theorizing — it's usually specific and correct.

## What is it?

A fixed order for diagnosing why a systemd service fails to start: check its current status, read
its full logs for the specific error, then work outward to config, dependencies, and permissions
only as the evidence points that way.

## Why does it exist?

"The service won't start" has many distinct causes — a missing binary, a config syntax error, a
port already in use, a missing dependency, wrong file permissions — that all present identically
as `systemctl start` failing. Guessing at the cause wastes time; the journal almost always states
the specific reason directly.

## Where is it used?

Any service that's `failed` or won't stay `active`, whether discovered manually or via a "service
down" alert — including immediately after a fresh deploy, a config change, or a server reboot.

## How it works

> 📊 Diagram: a flowchart — "systemctl start fails" → "systemctl status (quick summary)" →
> "journalctl -u {service} (full error)" → branching by error type: "binary/path not found" →
> check the unit file's `ExecStart`; "permission denied" → see Permission Denied Issues; "port
> already in use" → find and resolve the conflicting process; "dependency failed" → check
> `After=`/`Requires=` targets.

1. **Check current status** with `systemctl status {service}` — confirms it actually failed
   (versus just being stopped) and shows the last few log lines inline.
2. **Read the full error** with `journalctl -u {service} -b` — the status output truncates; the
   full journal almost always states the literal cause (a missing file, a bad config directive, a
   port conflict).
3. **If the error points to config**, validate the unit file's `ExecStart` path exists and is
   executable, and check for syntax errors in the application's own config file if one is loaded.
4. **If the error points to a dependency**, check `systemctl list-dependencies {service}` and
   confirm the units it depends on (`After=`/`Requires=`) are actually up.
5. **If the error is permission-related**, move to
   [Permission Denied Issues](permission-denied-issues.md) — the diagnostic steps there apply
   directly to a service failing due to file/directory access.

## Real-world example

A status page service fails to start after a routine reboot. `systemctl status` shows `failed
(Result: exit-code)` with no further detail; `journalctl -u statuspage -b` shows the actual line:
`Failed to execute /usr/local/bin/statuspage-server: No such file or directory` — the binary was
never deployed to this path on this particular host. The fix (redeploying the binary to the
correct path) took thirty seconds once the actual error was visible; guessing without the journal
could have burned much longer chasing config or permission theories that weren't the cause.

## Commands

See [`systemctl`](../../commands/systemctl.md) and [`journalctl`](../../commands/journalctl.md)
for full flag references.

## Production example

```
$ systemctl status statuspage
● statuspage.service - Status Page Service
     Loaded: loaded
     Active: failed (Result: exit-code)

$ journalctl -u statuspage -b --no-pager
statuspage.service: Failed to execute /usr/local/bin/statuspage-server: No such file or directory
statuspage.service: Failed at step EXEC spawning /usr/local/bin/statuspage-server: No such file or directory

$ ls -l /usr/local/bin/statuspage-server
ls: cannot access '/usr/local/bin/statuspage-server': No such file or directory
```

The journal names the exact missing path — no guessing required once you read past the summary
`systemctl status` output into the full journal.

## Do / Don't

| Do | Don't |
|---|---|
| Read the full `journalctl -u {service}` output, not just `systemctl status`'s summary | Stop at "failed" without reading the actual error |
| Check what changed recently (deploy, config edit, reboot) | Assume the cause is random or "flaky" |
| Verify the `ExecStart` path actually exists and is executable | Assume the unit file is correct because it worked before |
| Check dependency units if the error mentions one | Ignore `After=`/`Requires=` when a dependency might be the real cause |

## Common mistakes

- Reading only `systemctl status`'s truncated log preview instead of the full `journalctl` output.
- Assuming a service that "worked yesterday" can't have a config or path problem today — deploys,
  reboots, and package updates all change what's actually on disk.
- Treating every startup failure as a code bug when it's often an environment mismatch (missing
  file, wrong permission, unmet dependency).
- Not checking `enable` status separately from `active` status — a service can be running now but
  not configured to survive the next reboot, or vice versa.

## Best practices

- Always pair `systemctl status` with `journalctl -u {service} -b` — the second one usually has
  the actual answer.
- Ask "what changed since this last worked?" before assuming a mysterious, unprovoked failure.
- Use `Restart=on-failure` in unit files for services that should self-recover from transient
  failures, but still investigate root cause rather than relying on the restart alone.
- Confirm both `active` and `enabled` state after a fix — starting it once isn't the same as
  surviving the next reboot.

## Exercises

1. Deliberately break a unit file's `ExecStart` path and practice diagnosing it with
   `systemctl status` and `journalctl -u`.
2. Explain in one sentence why `journalctl -u {service} -b` is more useful than `systemctl
   status`'s inline log preview for this kind of diagnosis.
3. Write out the order you'd check things in if the journal error mentioned a dependency unit.

## Quiz

**Q: `systemctl status` shows a service failed but not why. What's the next command to run?**
<details><summary>Show answer</summary>
`journalctl -u {service} -b` — it shows the full log for that unit since the last boot, including
the specific error `systemctl status`'s summary view truncates.
</details>

**Q: A service ran fine yesterday and fails today with no code changes. What should you check first?**
<details><summary>Show answer</summary>
What actually changed in the environment — a reboot, a config edit, a package update, or a moved/
renamed file — since the code itself didn't change, something around it did.
</details>

## Interview questions

- A service starts fine when you run it manually but fails to come up after a server reboot. How
  do you debug this? → [Full answer in Scenario-Based Interview Questions](../../interview-questions/scenario-based.md#systemd-service-wont-start-after-reboot)

## Key Takeaways

- `systemctl status` gives a quick summary; `journalctl -u {service} -b` gives the actual, usually
  literal, error.
- Diagnose in order: status → full journal → config/path validation → dependencies → permissions.
- "What changed recently?" is the highest-value question for a service that used to work.
- `enabled` and `active` are separate states — verify both after a fix.

## Related topics

- [High CPU / Memory Issues](high-cpu-memory-issues.md)
- [Permission Denied Issues](permission-denied-issues.md)
- [Services](../11-services/index.md)
