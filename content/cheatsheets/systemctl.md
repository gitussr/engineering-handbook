---
title: "systemctl Cheat Sheet"
description: "A single-page quick reference for systemctl and journalctl: starting, stopping, and enabling services, checking status and logs, and the basics of unit files and targets."
type: "cheatsheet"
module: "services"
updatedAt: "2026-07-30"
keywords: ["systemctl cheat sheet", "systemd cheat sheet", "journalctl cheat sheet", "linux services cheat sheet"]
canonicalUrl: "/cheatsheets/systemctl"
---

# systemctl Cheat Sheet

The commands for controlling and inspecting systemd-managed services — what most modern Linux
distros use to start, stop, and supervise everything from web servers to cron.

## Service control

| Command | Description |
|---|---|
| [`systemctl`](../commands/systemctl.md) `start {service}` | Start a service now (until next reboot unless also enabled) |
| `systemctl stop {service}` | Stop a running service |
| `systemctl restart {service}` | Stop then start — used after a config change |
| `systemctl reload {service}` | Reload config without dropping active connections (if the service supports it) |
| `systemctl enable {service}` | Start automatically on boot |
| `systemctl disable {service}` | Stop starting automatically on boot |
| `systemctl enable --now {service}` | Enable and start in one command |

Full explanation → [systemctl: start, stop, enable, disable, status](../docs/11-services/systemctl-start-stop-enable-disable-status.md)

## Status and logs

| Command | Description |
|---|---|
| `systemctl status {service}` | Current state, recent log lines, and the process tree |
| `systemctl is-active {service}` | Just the active/inactive state, script-friendly |
| `systemctl is-enabled {service}` | Whether it's set to start on boot |
| [`journalctl`](../commands/journalctl.md) `-u {service}` | Full logs for one service |
| `journalctl -u {service} -f` | Follow a service's logs live |
| `journalctl -u {service} -b` | Logs for one service since the last boot |
| `journalctl -p err` | Filter to error-priority messages and above, across all units |

Full explanation → [journalctl Basics](../docs/11-services/journalctl-basics.md)

## Unit files

| Concept | Description |
|---|---|
| `/etc/systemd/system/{name}.service` | Where custom/overriding unit files live |
| `[Unit]` | Metadata and dependency directives (`Description=`, `After=`, `Requires=`) |
| `[Service]` | How to run it (`ExecStart=`, `Restart=`, `User=`) |
| `[Install]` | How it hooks into boot targets (`WantedBy=multi-user.target`) |
| `systemctl daemon-reload` | Reload unit file definitions after editing one |

Full explanation → [Writing a systemd Unit File](../docs/11-services/writing-a-systemd-unit-file.md)

## Dependencies and targets

| Directive / command | Description |
|---|---|
| `After=` | Start order only — no dependency enforcement |
| `Requires=` | Hard dependency — this unit stops if the required unit fails |
| `WantedBy=` | Which target "pulls in" this unit when reached |
| `systemctl list-dependencies {service}` | Show a unit's dependency tree |
| `systemctl get-default` | Show the current default boot target |

Full explanation → [Service Dependencies and Targets](../docs/11-services/service-dependencies-and-targets.md)

## Related topics

- [Cron Cheat Sheet](cron.md)
- [Services Module](../docs/11-services/index.md)
- [systemd Overview](../docs/11-services/systemd-overview.md)
- [Cheat Sheets Hub](index.md)
