---
title: "systemctl — Control the systemd System and Service Manager"
description: "Start, stop, enable, disable, and inspect systemd units, including service status and logs."
relatedConcepts: ["11-services/systemctl-start-stop-enable-disable-status", "11-services/systemd-overview"]
relatedCommands: ["journalctl"]
careerRelevance: ["devops", "linux-administrator", "sre"]
difficulty: "must-know"
updatedAt: "2026-07-25"
keywords: ["systemctl command", "systemctl status", "systemctl enable vs start", "systemctl list-units"]
canonicalUrl: "/commands/systemctl"
---

# systemctl

🟢 Must Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** `systemctl start|stop|restart|enable|disable|status NAME` covers the vast majority
> of daily use. `start`/`enable` are independent — pair them for anything that should run now and
> survive a reboot.

## Purpose

`systemctl` is the primary command-line interface to systemd — see
[systemctl: start, stop, enable, disable, status](../docs/11-services/systemctl-start-stop-enable-disable-status.md)
for the full start/enable distinction.

## Syntax

```
systemctl [COMMAND] [UNIT]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `COMMAND` | The action to perform (start, stop, status, etc.) | Yes |
| `UNIT` | The unit name to act on (e.g. `nginx.service`, often shortened to `nginx`) | Usually |

## Options

| Command | Meaning |
|---|---|
| `start` | Start a unit now |
| `stop` | Stop a unit now |
| `restart` | Stop then start |
| `reload` | Ask a running service to reload its config, if it supports it, without a full restart |
| `enable` | Configure to start automatically at boot |
| `disable` | Remove from automatic boot startup |
| `status` | Show current state, enabled/active status, and recent log lines |
| `list-units` | List all currently loaded units |
| `daemon-reload` | Reload systemd's own configuration after editing a unit file |

## Examples

```
$ sudo systemctl start nginx
```
Start a service now.

```
$ sudo systemctl enable nginx
```
Configure it to start at boot.

```
$ systemctl status nginx
```
Check current state.

```
$ sudo systemctl daemon-reload
```
Reload systemd's configuration after editing a unit file (required before changes take effect).

## Expected Output

```
$ systemctl status nginx
● nginx.service - A high performance web server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2026-07-25 09:00:12 UTC; 2h 14min ago
   Main PID: 1234 (nginx)
```

## Exit Status

`0` on success, non-zero if the unit doesn't exist or the operation fails (e.g. `status` returns
non-zero for an inactive service, which is useful for scripting checks).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `Unit name.service not found` | Typo, or the unit file doesn't exist | Verify with `systemctl list-units` or check the unit file path |
| Edited a unit file but changes don't apply | Forgot `systemctl daemon-reload` | Run `daemon-reload`, then `restart` the affected service |
| `Failed to start name.service` | Check `systemctl status` and `journalctl -u name` for the actual error | See [journalctl Basics](../docs/11-services/journalctl-basics.md) |

## Security Considerations

Most `systemctl` operations affecting system state require root/sudo — a deliberate boundary
consistent with how significant service start/stop is as an action.

## Performance Considerations

Not applicable — `systemctl` itself is lightweight; the performance characteristics of the
services it manages are a separate concern.

## Production Usage

`systemctl status` is almost always the first command run when a service isn't behaving as
expected — it surfaces recent log lines directly, often enough to diagnose common issues without
a separate `journalctl` call.

## Related Commands

- [`journalctl`](journalctl.md) — deeper log inspection than `status`'s summary

## Related Concepts

- [systemctl: start, stop, enable, disable, status](../docs/11-services/systemctl-start-stop-enable-disable-status.md)
- [systemd Overview](../docs/11-services/systemd-overview.md)
