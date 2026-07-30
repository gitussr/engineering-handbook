---
title: "Project: Build a Bash-Based Server Health-Check and Alerting Script"
description: "An intermediate Linux project: build a script that checks CPU, memory, disk, and key service health, and sends an alert when a threshold is crossed."
type: "project"
tier: "intermediate"
careerRelevance: ["devops", "sre", "linux-administrator"]
updatedAt: "2026-07-30"
keywords: ["server health check script project", "bash alerting script", "linux monitoring script project", "devops shell scripting project"]
canonicalUrl: "/projects/intermediate/server-health-check-and-alerting-script"
---

# Project: Build a Bash-Based Server Health-Check and Alerting Script

🟡 Intermediate · Relevant for: DevOps, SRE, Linux Administrator

## Goal

Build a health-check script that goes beyond reporting numbers (Beginner's system dashboard
project) to actually deciding when something is wrong and alerting — the difference between a
status report and real monitoring.

## Requirements

- Checks CPU, memory, and disk usage against configurable thresholds.
- Checks that a list of critical systemd services are active, not just that the host is up.
- Sends an alert (email, webhook, or even just a clearly-flagged log entry) only when a threshold
  is crossed — it must not spam on every run when everything is healthy.
- Avoids alert fatigue: a single brief spike shouldn't page anyone; the check should require the
  condition to hold for more than one consecutive run before alerting.
- Runs unattended on a schedule (cron or a systemd timer) and logs every run, pass or fail.

## Suggested Approach

1. Write the individual checks first as separate functions (`check_cpu`, `check_memory`,
   `check_disk`, `check_services`) — each returning a clear pass/fail, not printing directly.
2. Add a state file that tracks how many consecutive times a check has failed, so you can
   implement the "must fail N times before alerting" rule without an external database.
3. Build the alerting mechanism (a webhook call, or `mail`, or a Slack incoming webhook) as its
   own function, called only from the threshold-crossing logic — keep detection and notification
   decoupled.
4. Test failure paths deliberately: fill a disk temporarily, stop a service you're monitoring —
   don't just trust that your thresholds work because the code compiles.
5. Schedule it and let it run for real for a day or two, checking the logs to confirm it isn't
   alerting on noise before you consider it done.

## Stretch Goals

- Make thresholds configurable via a separate config file instead of hardcoded in the script.
- Add a "resolved" notification when a previously-failing check passes again.
- Track and log historical check results so you can later plot trends, not just current state.
- Port the alerting logic to also work as a systemd service with its own timer, and compare the
  experience to running it under cron.

## Related Modules

- [Shell Scripting](../../docs/18-shell-scripting/index.md) — functions, exit codes, real automation scripts
- [Monitoring](../../docs/20-monitoring/index.md) — alerting concepts and thresholds
- [Services](../../docs/11-services/index.md) — checking service status programmatically
- [Cron](../../docs/17-cron/index.md) — scheduling the script

## Related topics

- [Cron Cheat Sheet](../../cheatsheets/cron.md)
- [systemctl Cheat Sheet](../../cheatsheets/systemctl.md)
- [System Information Dashboard Project (Beginner)](../beginner/system-information-dashboard-script.md)
- [Projects Hub](../index.md)
