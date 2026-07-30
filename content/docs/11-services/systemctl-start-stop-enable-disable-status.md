---
title: "systemctl: start, stop, enable, disable, status"
description: "The single most-run systemd command — and the start/enable distinction that trips up nearly everyone at least once."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["11-services/systemd-overview"]
relatedTopics: ["writing-a-systemd-unit-file"]
relatedCommands: ["systemctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#start-vs-enable"]
relatedCheatsheet: "systemctl"
furtherReading: []
nextTopic: "11-services/writing-a-systemd-unit-file"
prevTopic: "11-services/systemd-overview"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["systemctl start vs enable", "systemctl status explained", "systemctl commands"]
canonicalUrl: "/docs/services/systemctl-start-stop-enable-disable-status"
---

# systemctl: start, stop, enable, disable, status

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `start`/`stop` act *now*, on the running system. `enable`/`disable` control whether
> a service starts automatically at boot. They're completely independent — a service can be
> started but not enabled (won't survive a reboot) or enabled but not started (will start next
> boot, but isn't running now). This distinction is the single most common systemd confusion.

## What is it?

`systemctl` is the primary command for interacting with systemd — starting, stopping,
enabling, disabling, and checking the status of units (most often services).

## Why does it exist?

`systemctl` is systemd's control interface — every interaction with systemd from the command
line goes through it, replacing the collection of separate scripts and commands SysV init
required (this module's last topic covers that transition in more detail).

## Where is it used?

Constantly — deploying a new version of a service, restarting after a config change,
troubleshooting why something isn't running, or setting up a new service to survive reboots.

## How it works

> 📊 Diagram: a 2x2 grid — rows labeled "Started" / "Not started," columns labeled "Enabled" /
> "Not enabled" — four distinct, independent states a service can be in, making clear that
> start/stop and enable/disable are two separate axes, not one combined setting.

| Command | Effect | When it applies |
|---|---|---|
| `systemctl start NAME` | Start the service now | Immediately, on the running system |
| `systemctl stop NAME` | Stop the service now | Immediately |
| `systemctl restart NAME` | Stop then start | Immediately |
| `systemctl enable NAME` | Configure to start automatically at boot | Takes effect on the *next* boot |
| `systemctl disable NAME` | Remove from automatic boot startup | Takes effect on the *next* boot |
| `systemctl status NAME` | Show current state, recent log lines, and enabled/active status | Immediately, read-only |

**The critical distinction**: `start`/`stop` and `enable`/`disable` are independent. A newly
installed service is often neither started nor enabled — installing the package alone does
neither automatically on most distros. A common deployment mistake is running `systemctl start`
and considering the job done, only to find the service didn't come back after the next reboot,
because it was never `enable`d.

## Real-world example

An engineer installs and starts a new monitoring agent with `systemctl start monitoring-agent`,
confirms it's running with `status`, and moves on — a week later, a routine server reboot for
patching leaves the agent silently not running, because `enable` was never run. `systemctl status`
would have shown "enabled: no" the whole time, if anyone had checked that specific field.

## Commands

- [`systemctl`](../../commands/systemctl.md) — full syntax and examples

## Production example

```
$ sudo systemctl start nginx
$ sudo systemctl enable nginx
$ sudo systemctl status nginx
● nginx.service - A high performance web server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2026-07-25 09:00:12 UTC; 2h 14min ago
```

Both `start` and `enable` run together — the correct pattern for a service that should be running
now *and* survive future reboots.

## Do / Don't

| Do | Don't |
|---|---|
| Run both `start` and `enable` for a service meant to persist across reboots | Assume `start` alone means it'll survive a reboot |
| Check `systemctl status`'s "enabled/disabled" line, not just "active/inactive" | Only check whether a service is currently running, ignoring boot persistence |
| Use `systemctl restart` after a binary/config update | Manually `stop` then `start` as two separate steps when `restart` does both |

## Common mistakes

- Running `systemctl start` and assuming that's sufficient for a service to survive a reboot —
  it isn't; `enable` is a separate, required step.
- Not reading `systemctl status`'s full output — it shows both the enabled/disabled state and the
  active/inactive state together, and skimming past one of them is easy to do.
- Confusing `restart` with `reload` (not covered in this list, but relevant — `reload` asks a
  service to re-read its config without fully restarting, if it supports it; full nuance in
  Module 11's later topics).

## Best practices

- Always pair `start` with `enable` (and `stop` with `disable`) for services meant to be
  permanently on or off, not just temporarily toggled.
- Make `systemctl status servicename` a reflexive first check whenever something's not behaving
  as expected — it surfaces recent log lines directly, often enough to diagnose the issue without
  a separate `journalctl` call.

## Exercises

1. Start a service, then check `systemctl status` and confirm it shows "enabled: no" if you
   haven't run `enable`.
2. Enable that same service and re-check status.
3. Explain in one sentence why a service could be "enabled" but currently "inactive."

## Quiz

**Q: If a service is started but not enabled, will it be running after a reboot?**
<details><summary>Show answer</summary>
No — `enable` controls automatic startup at boot; without it, a manually started service won't
come back after a reboot even though it's currently running.
</details>

**Q: What's the difference between `systemctl restart` and running `stop` then `start` manually?**
<details><summary>Show answer</summary>
Functionally the same result — `restart` is just a convenient single command that does both in
sequence.
</details>

## Interview questions

- What's the difference between `systemctl start` and `systemctl enable`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- `start`/`stop` act now; `enable`/`disable` control boot-time behavior — independent axes.
- A service can be started-but-not-enabled or enabled-but-not-started; both are valid, distinct
  states.
- `systemctl status` shows both dimensions together, plus recent log lines.
- Pair `start` with `enable` for anything meant to persist across reboots.

## Related topics

- [Writing a systemd Unit File](writing-a-systemd-unit-file.md)
