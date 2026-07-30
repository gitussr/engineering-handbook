---
title: "Lab: Diagnose and Fix a Service That Won't Start"
description: "A beginner Linux lab, framed as a real support ticket: a systemd service refuses to start, and you have to find out why using systemctl and journalctl."
type: "lab"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops", "sre"]
updatedAt: "2026-07-30"
keywords: ["systemd service wont start lab", "systemctl troubleshooting lab", "journalctl exercise beginner", "linux service debugging lab"]
canonicalUrl: "/labs/beginner/diagnose-and-fix-a-service-that-wont-start"
---

# Lab: Diagnose and Fix a Service That Won't Start

🟢 Beginner · Relevant for: Linux Administrator, DevOps, SRE

## Scenario

**Ticket #LX-104:** "I tried to start our internal status page service and it just fails
immediately. `systemctl start statuspage` returns an error and the service shows as `failed`.
Can you find out what's wrong and get it running?"

## Environment Setup

Reproduce a broken unit yourself:

```bash
sudo tee /etc/systemd/system/statuspage.service > /dev/null <<'EOF'
[Unit]
Description=Status Page Service

[Service]
ExecStart=/usr/local/bin/statuspage-server
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl start statuspage
```

The binary referenced in `ExecStart` doesn't actually exist yet — that's the broken state to
diagnose, mirroring a very common real cause (a unit file referencing a path that isn't there,
often after a deploy that moved or renamed the binary).

## Tasks

1. Confirm the service failed to start and get its current status.
2. Find the specific error explaining *why* it failed — not just that it failed.
3. Fix the underlying problem so the service starts successfully.
4. Enable the service so it starts automatically on future boots.

## Hints

<details><summary>Show hint</summary>
`systemctl status` shows the last few log lines inline, but `journalctl -u {service}` gives you
the full picture. The error is usually very literal — read exactly what it says before assuming
something more complicated is wrong.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# Confirm the failure
systemctl status statuspage
# ● statuspage.service - Status Page Service
#    Active: failed (Result: exit-code)

# Get the full error detail
journalctl -u statuspage -b
# ...statuspage.service: Failed to execute /usr/local/bin/statuspage-server: No such file or directory

# The binary genuinely doesn't exist — for this lab, simulate "the deploy" by creating a minimal
# placeholder so the unit can actually start:
sudo tee /usr/local/bin/statuspage-server > /dev/null <<'EOF'
#!/bin/bash
while true; do sleep 3600; done
EOF
sudo chmod +x /usr/local/bin/statuspage-server

# Retry
sudo systemctl start statuspage
systemctl status statuspage
# Active: active (running)

# Enable on boot
sudo systemctl enable statuspage
```

In a real incident, the fix would be restoring or correcting the actual application binary/path —
the diagnostic process (status → journalctl → read the literal error) is the transferable skill.
</details>

## What You Learned

- `systemctl status` gives a quick summary; `journalctl -u {service}` gives the full error detail.
- A failed unit's error message is usually literal and specific — read it before theorizing.
- `enable` and `start` are separate: a service can be running now but not survive a reboot, or
  vice versa.

## Related Modules

- [Services](../../docs/11-services/index.md)

## Related topics

- [systemctl Cheat Sheet](../../cheatsheets/systemctl.md)
- [Scenario-Based Interview Questions: systemd service won't start](../../interview-questions/scenario-based.md#systemd-service-wont-start-after-reboot)
- [Labs Hub](../index.md)
