---
title: "Lab: Debug a Memory Leak Using top, vmstat, and journalctl"
description: "An advanced Linux lab, framed as a real incident ticket: a long-running service's memory usage keeps climbing, and you have to confirm the leak and identify the process responsible."
type: "lab"
tier: "advanced"
careerRelevance: ["sre", "linux-administrator", "devops", "backend"]
updatedAt: "2026-07-30"
keywords: ["memory leak lab linux", "vmstat top exercise", "debug memory leak process lab", "advanced linux lab performance"]
canonicalUrl: "/labs/advanced/debug-a-memory-leak-with-top-vmstat-journalctl"
---

# Lab: Debug a Memory Leak Using top, vmstat, and journalctl

🔴 Advanced · Relevant for: SRE, Linux Administrator, DevOps, Backend

## Scenario

**Ticket #LX-301:** "Our worker process has been getting OOM-killed roughly once a day for the
past week. Memory usage looks fine right after a restart but climbs steadily until it dies. Find
proof this is a genuine leak (not just normal steady-state usage) and identify the process."

## Environment Setup

Simulate a leaking process:

```bash
sudo tee /usr/local/bin/leaky-worker.sh > /dev/null <<'EOF'
#!/bin/bash
declare -a leak
while true; do
  leak+=("$(head -c 1000000 /dev/zero | tr '\0' 'x')")
  sleep 2
done
EOF
sudo chmod +x /usr/local/bin/leaky-worker.sh
sudo tee /etc/systemd/system/leaky-worker.service > /dev/null <<'EOF'
[Unit]
Description=Leaky Worker
[Service]
ExecStart=/usr/local/bin/leaky-worker.sh
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl start leaky-worker
```

## Tasks

1. Confirm overall system memory pressure is trending upward, not just currently high.
2. Identify which specific process is responsible.
3. Watch that process's memory usage over time and confirm it's climbing steadily rather than
   fluctuating around a stable baseline.
4. Check `journalctl` for evidence of past OOM kills tied to this process.
5. Stop the leaking process and confirm system memory recovers.

## Hints

<details><summary>Show hint</summary>
`vmstat 2` gives you a live trend of memory over time, not just a snapshot. `top` sorted by
memory (`Shift+M` inside `top`, or `ps aux --sort=-%mem`) tells you which process to watch
specifically. The kernel logs OOM kills with the string `Out of memory` or `oom-kill` via `journalctl -k`.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm a memory pressure trend, not just a single reading
vmstat 2 10
# Watch the 'free' column trend downward across the 10 samples

# Identify the top memory consumer
ps aux --sort=-%mem | head -5

# Watch it specifically over a couple of minutes
watch -n 5 'ps -p $(pgrep -f leaky-worker.sh) -o pid,%mem,rss,etime'
# RSS climbs steadily every sample — a stable process would plateau, this one doesn't

# Check kernel logs for past OOM kills
journalctl -k | grep -i "out of memory\|oom-kill"

# Stop the leaking process
sudo systemctl stop leaky-worker

# Confirm memory recovers
free -h
vmstat 2 5
```

The distinguishing signal of a real leak versus normal usage: RSS that climbs *without ever
plateauing* over an extended watch window, confirmed by watching the same PID over time rather
than judging from a single `top` snapshot.
</details>

## What You Learned

- Why a single `top` snapshot can't confirm a leak — you need a trend over time (`vmstat`, or
  repeated `ps` samples of the same PID).
- How to find OOM-kill evidence in kernel logs via `journalctl -k`.
- The practical difference between "memory usage is high" and "memory usage never stops growing"
  — only the second is a leak.

## Related Modules

- [Performance](../../docs/21-performance/index.md)
- [Processes](../../docs/10-processes/index.md)
- [Logs](../../docs/16-logs/index.md)

## Related topics

- [Linux Commands Cheat Sheet](../../cheatsheets/linux-commands.md)
- [Scenario-Based Interview Questions: OOM killer terminated wrong process](../../interview-questions/scenario-based.md#oom-killer-terminated-wrong-process)
- [Labs Hub](../index.md)
