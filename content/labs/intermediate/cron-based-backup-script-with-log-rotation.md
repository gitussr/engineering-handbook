---
title: "Lab: Write a Cron-Based Backup Script with Log Rotation"
description: "An intermediate Linux lab, framed as a real ticket: build a scheduled backup script with proper logging, and make sure its logs don't grow unbounded."
type: "lab"
tier: "intermediate"
careerRelevance: ["linux-administrator", "devops"]
updatedAt: "2026-07-30"
keywords: ["cron backup script lab", "logrotate lab exercise", "linux backup automation lab", "intermediate linux lab cron"]
canonicalUrl: "/labs/intermediate/cron-based-backup-script-with-log-rotation"
---

# Lab: Write a Cron-Based Backup Script with Log Rotation

🟡 Intermediate · Relevant for: Linux Administrator, DevOps

## Scenario

**Ticket #LX-201:** "We need nightly backups of `/etc/app-config` to `/backups`. It has to run
unattended via cron, log what it does, and — this bit's important, we got burned by this before —
the log file itself must not be allowed to grow forever and fill the disk."

## Environment Setup

```bash
sudo mkdir -p /etc/app-config /backups
sudo bash -c 'echo "sample-config=true" > /etc/app-config/settings.conf'
```

## Tasks

1. Write a backup script that archives `/etc/app-config` into `/backups` with a timestamped filename.
2. The script must log each run (start time, what it backed up, success/failure) to a dedicated log file.
3. Schedule it via cron to run nightly.
4. Configure `logrotate` for the script's log file so it never grows unbounded.
5. Prove the whole pipeline works: force a manual run, confirm the archive and log entry both
   appear, then force a `logrotate` cycle and confirm rotation actually happens.

## Hints

<details><summary>Show hint</summary>
Redirect the script's own output into its log file rather than relying on cron's mail delivery,
which usually isn't configured on a bare server. `logrotate -f` forces an immediate rotation for
testing, so you don't have to wait for the real schedule to verify your config works.
</details>

## Solution

<details><summary>Show solution</summary>

```bash
# The backup script
sudo tee /usr/local/bin/backup-app-config.sh > /dev/null <<'EOF'
#!/bin/bash
set -euo pipefail
LOG="/var/log/app-config-backup.log"
STAMP=$(date +%Y%m%d-%H%M%S)
ARCHIVE="/backups/app-config-${STAMP}.tar.gz"

echo "[$(date)] Starting backup" >> "$LOG"
if tar -czf "$ARCHIVE" /etc/app-config; then
  echo "[$(date)] Backup succeeded: $ARCHIVE" >> "$LOG"
else
  echo "[$(date)] Backup FAILED" >> "$LOG"
  exit 1
fi
EOF
sudo chmod +x /usr/local/bin/backup-app-config.sh

# Test it manually first
sudo /usr/local/bin/backup-app-config.sh
ls /backups/
cat /var/log/app-config-backup.log

# Schedule it nightly at 2am
(sudo crontab -l 2>/dev/null; echo "0 2 * * * /usr/local/bin/backup-app-config.sh") | sudo crontab -

# logrotate config
sudo tee /etc/logrotate.d/app-config-backup > /dev/null <<'EOF'
/var/log/app-config-backup.log {
    weekly
    rotate 4
    compress
    missingok
    notifempty
}
EOF

# Force a rotation to prove the config is valid and actually rotates
sudo logrotate -f /etc/logrotate.d/app-config-backup
ls /var/log/app-config-backup.log*
```
</details>

## What You Learned

- Why redirecting a script's own output to a log file is more reliable than depending on cron's
  mail delivery.
- How to write and test a `logrotate` config, including forcing a rotation for verification.
- That "it runs" and "it runs safely forever" are different bars — the second requires thinking
  about what grows unbounded over time.

## Related Modules

- [Cron](../../docs/17-cron/index.md)
- [Logs](../../docs/16-logs/index.md)
- [Shell Scripting](../../docs/18-shell-scripting/index.md)

## Related topics

- [Cron Cheat Sheet](../../cheatsheets/cron.md)
- [File Organization and Backup Script Project](../../projects/beginner/file-organization-and-backup-script.md)
- [Labs Hub](../index.md)
