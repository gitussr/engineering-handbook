---
title: "Project: Build a System Information Dashboard Script"
description: "A beginner Linux project: build a script that prints a clean, at-a-glance dashboard of a system's CPU, memory, disk, and process information."
type: "project"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops", "sre"]
updatedAt: "2026-07-30"
keywords: ["bash system dashboard project", "linux system info script", "beginner monitoring script project", "shell script cpu memory disk"]
canonicalUrl: "/projects/beginner/system-information-dashboard-script"
---

# Project: Build a System Information Dashboard Script

🟢 Beginner · Relevant for: Linux Administrator, DevOps, SRE

## Goal

Build a single script that, when run, prints a clean summary of a system's current health —
CPU, memory, disk usage, and the top resource-consuming processes — in a format you could
actually glance at during an incident.

## Requirements

- Report current CPU usage (overall, not per-core detail).
- Report memory usage, showing both raw `free`/`used` and the more meaningful `available` figure.
- Report disk usage for each mounted filesystem.
- List the top 5 processes by CPU and the top 5 by memory.
- Format the output with clear section headers — this should read like a dashboard, not raw
  command output pasted together.

## Suggested Approach

1. Get each piece of data working as a standalone command first (`df`, `free`, `ps`) before
   wiring anything into a script.
2. Build the script section by section, testing after each addition — a script that silently
   breaks halfway through is harder to debug than one you built and tested incrementally.
3. Use `printf` rather than `echo` once you start formatting columns/headers — it gives you
   real control over spacing and alignment.
4. Extract the "top 5 by CPU/memory" logic using `ps` with the right sort flags rather than
   piping through several tools you don't need.
5. Run the finished script on two different machines (or a VM with different load) to confirm
   the formatting holds up when the numbers are very different in size.

## Stretch Goals

- Add color output (green/yellow/red) based on whether CPU/memory/disk cross a threshold.
- Add a `--watch` flag that refreshes the dashboard every few seconds instead of running once.
- Write the output to a log file with a timestamp, in addition to printing it.
- Package it as a proper CLI tool with `--help` output and flags for which sections to show.

## Related Modules

- [Processes](../../docs/10-processes/index.md) — `ps`, `top`
- [Performance](../../docs/21-performance/index.md) — CPU and memory performance tools
- [Shell Scripting](../../docs/18-shell-scripting/index.md) — script structure, functions

## Related topics

- [Linux Commands Cheat Sheet](../../cheatsheets/linux-commands.md)
- [SSH Key-Based Access Project](ssh-key-based-access-to-a-personal-server.md)
- [Projects Hub](../index.md)
