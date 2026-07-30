---
title: "Project: Build a Centralized Log Viewer and Rotation Pipeline"
description: "An intermediate Linux project: aggregate logs from multiple services into one place, configure proper rotation, and build a simple script to search across all of them."
type: "project"
tier: "intermediate"
careerRelevance: ["devops", "sre", "linux-administrator", "cybersecurity"]
updatedAt: "2026-07-30"
keywords: ["log rotation project linux", "centralized logging project", "logrotate project", "log aggregation script"]
canonicalUrl: "/projects/intermediate/centralized-log-viewer-and-rotation-pipeline"
---

# Project: Build a Centralized Log Viewer and Rotation Pipeline

🟡 Intermediate · Relevant for: DevOps, SRE, Linux Administrator, Cybersecurity

## Goal

Take logs scattered across several services on one server, put proper rotation in place so none
of them fill the disk, and build a small tool that lets you search across all of them from one
place — the un-glamorous but constantly-needed work of log hygiene.

## Requirements

- At least 3 services generating logs (can be real services or scripts that generate realistic
  fake log lines on a schedule).
- A working `logrotate` configuration for each, with a sane retention policy (not "keep forever").
- A script that tails or searches across all the configured log files at once, with a common
  output format regardless of each service's original log format.
- A way to filter the aggregated search by time range and by severity (error/warn/info).
- Documentation of your retention policy and why you chose it.

## Suggested Approach

1. Get real log volume first — either run genuinely noisy services or write a small generator
   script that appends realistic-looking log lines on a timer, so rotation has something to do.
2. Write and test one `logrotate` config at a time, using `logrotate -d` (dry run/debug) to
   verify behavior before trusting it on a schedule.
3. Build the search/aggregation script as a thin wrapper over `grep`/`awk`, not a rewrite of them
   — the value is in normalizing multiple log formats and time ranges, not reimplementing search.
4. Test the retention policy by forcing rotations manually (`logrotate -f`) several times in a
   row and confirming old logs actually get pruned per your policy.
5. Deliberately break something (stop a service, corrupt a log line) and confirm your search tool
   still degrades gracefully instead of crashing on unexpected input.

## Stretch Goals

- Add basic log-based alerting: flag if error-level lines spike above a rate threshold.
- Compress older logs and confirm your search tool can still search inside `.gz` rotated files.
- Add a simple timeline view: how many errors per hour across the last 24 hours.
- Research (without necessarily implementing) how this would change moving to a real stack like
  the ELK stack or Grafana Loki, and write up the tradeoffs.

## Related Modules

- [Logs](../../docs/16-logs/index.md) — `/var/log`, `logrotate`, syslog/rsyslog, centralized logging concepts
- [Cron](../../docs/17-cron/index.md) — scheduling log-generation and checks
- [Shell Scripting](../../docs/18-shell-scripting/index.md) — building the aggregation script

## Related topics

- [Server Health-Check Project](server-health-check-and-alerting-script.md)
- [Cron Cheat Sheet](../../cheatsheets/cron.md)
- [Projects Hub](../index.md)
