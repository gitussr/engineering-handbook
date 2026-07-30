---
title: "Real Automation Scripts: Backup, Deploy, Health-Check"
description: "Three complete, production-shaped scripts combining every construct from this module — a backup script, a deploy script, and a health-check script."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["18-shell-scripting/debugging-scripts-set-x-shellcheck"]
relatedTopics: ["debugging-scripts-set-x-shellcheck"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator", "cloud"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#write-a-backup-script"]
relatedCheatsheet: ""
furtherReading: []
nextTopic: "19-security/linux-security-model-overview"
prevTopic: "18-shell-scripting/debugging-scripts-set-x-shellcheck"
estimatedReadingTime: 10
updatedAt: "2026-07-28"
keywords: ["bash backup script example", "bash deploy script example", "bash health check script", "production bash script example"]
canonicalUrl: "/docs/shell-scripting/real-automation-scripts"
---

# Real Automation Scripts: Backup, Deploy, Health-Check

🔴 Expert · Relevant for: DevOps · SRE · Linux Administrator · Cloud

> **TL;DR:** This page combines everything in this module — variables, conditionals, loops,
> functions, arguments, exit-code discipline, and debuggability — into three realistic scripts:
> backup, deploy, and health-check. Not new syntax, but how the syntax is actually assembled.

## What is it?

Three complete, production-shaped scripts, each applying multiple constructs from earlier in this
module together, the way real automation actually looks rather than isolated syntax examples.

## Why does it exist?

Every earlier page in this module taught one construct in isolation. Real scripts combine several
of them at once, and the combination itself — where to put a `set -e`, how to structure a
usage-check, when a piece of logic deserves its own function — is a skill beyond just knowing each
piece individually. This page exists to show that assembly.

## Where is it used?

These three script shapes (backup, deploy, health-check) cover the large majority of real-world
scheduled and manually-triggered automation on Linux systems — often themselves scheduled via
[cron or a systemd timer](../17-cron/index.md).

## How it works

> 📊 Diagram: three script "shapes" side by side, each broken into the same four generic stages —
> validate input/preconditions, do the work, check results, report outcome — showing that despite
> different content, all three (backup, deploy, health-check) follow the same underlying skeleton.

### 1. Backup script

```bash
#!/bin/bash
set -euo pipefail

SOURCE_DIR="/data"
BACKUP_DIR="/backup"
RETENTION_DAYS=30

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

if [[ ! -d "$SOURCE_DIR" ]]; then
    log "ERROR: Source directory $SOURCE_DIR does not exist"
    exit 1
fi

timestamp=$(date '+%Y%m%d-%H%M%S')
archive="$BACKUP_DIR/backup-${timestamp}.tar.gz"

log "Starting backup of $SOURCE_DIR"
if tar -czf "$archive" "$SOURCE_DIR"; then
    log "Backup succeeded: $archive"
else
    log "ERROR: Backup failed"
    exit 1
fi

log "Pruning backups older than $RETENTION_DAYS days"
find "$BACKUP_DIR" -name "backup-*.tar.gz" -mtime "+${RETENTION_DAYS}" -delete

log "Backup complete"
```

Applies: `set -euo pipefail` (Exit Codes/Error Handling), a `log()` function, precondition
validation before doing anything destructive, explicit success/failure checking around the
critical step, and only pruning old backups *after* confirming the new one succeeded.

### 2. Deploy script

```bash
#!/bin/bash
set -euo pipefail

if [[ $# -lt 1 ]]; then
    echo "Usage: $0 SERVER [SERVER...]"
    exit 1
fi

deploy_to() {
    local server="$1"
    echo "Deploying to $server..."
    ssh "$server" "sudo systemctl restart myapp"
}

wait_for_healthy() {
    local server="$1"
    local attempts=0
    until ssh "$server" "curl -sf http://localhost:8080/health" > /dev/null 2>&1; do
        ((attempts++))
        if [[ $attempts -ge 10 ]]; then
            echo "ERROR: $server never became healthy"
            return 1
        fi
        sleep 3
    done
    echo "$server is healthy"
}

for server in "$@"; do
    deploy_to "$server"
    wait_for_healthy "$server"
done

echo "Deploy complete"
```

Applies: `$#` validation (Script Arguments), `"$@"` iteration over multiple servers, two named
functions (`deploy_to`, `wait_for_healthy`) each with `local` variables, and a bounded retry loop
(`until` with a max attempt count) per server before moving to the next.

### 3. Health-check script

```bash
#!/bin/bash
set -euo pipefail

SERVICES=("nginx" "myapp" "postgresql")
failures=0

for service in "${SERVICES[@]}"; do
    if systemctl is-active --quiet "$service"; then
        echo "OK: $service is running"
    else
        echo "FAIL: $service is not running"
        ((failures++))
    fi
done

if [[ $failures -gt 0 ]]; then
    echo "$failures service(s) failed health check"
    exit 1
fi

echo "All services healthy"
exit 0
```

Applies: an array (Variables and Data Types), a `for` loop over it, a conditional per iteration,
an accumulating failure counter, and a final exit code reflecting overall success/failure — ready
to be scheduled directly via [cron or a systemd timer](../17-cron/index.md) and alerted on.

## Real-world example

A team's health-check script (structured much like the one above) is scheduled every five minutes
via a systemd timer. When `postgresql` crashes at 3 AM, the script's non-zero exit code — checked
by the scheduler's own alerting integration — pages the on-call engineer within minutes, entirely
because the script was written to `exit 1` on failure instead of just printing a message and
exiting `0` regardless. This is the payoff of the exit-code discipline taught earlier in this
module: a script's exit status is a real, checkable signal other tooling relies on.

## Syntax

No new syntax — this page combines constructs already introduced earlier in this module.

## Commands

No new command — see [`shellcheck`](../../commands/shellcheck.md) and
[`set`](../../commands/set.md) from the previous page; all three scripts above should be run
through `shellcheck` before deployment.

## Production example

```
$ shellcheck healthcheck.sh
$ ./healthcheck.sh
OK: nginx is running
FAIL: myapp is not running
OK: postgresql is running
1 service(s) failed health check
$ echo $?
1
```

Running `shellcheck` first, then confirming the script's exit code (`$?`) genuinely reflects
failure — the detail that makes this script safe to wire into scheduling and alerting.

## Do / Don't

| Do | Don't |
|---|---|
| Combine `set -euo pipefail`, functions, and explicit exit codes in every real script | Write a "real" script without any of the error-handling discipline from earlier pages |
| Validate preconditions before doing anything destructive | Assume inputs and environment are always as expected |
| Give a health-check/deploy script a meaningful, checkable exit code | Let a script always exit `0` regardless of whether it actually succeeded |

## Common mistakes

- Writing a "quick" production script without `set -e` or argument validation, treating the
  discipline from earlier pages as optional for anything that feels simple.
- A health-check or monitoring script that always exits `0`, silently defeating any alerting
  system that depends on its exit code to detect a real failure.
- Skipping `shellcheck` on a script "because it's short," missing exactly the kind of subtle
  quoting bug that causes production incidents.

## Best practices

- Treat every script destined for production the same way regardless of its apparent simplicity:
  `set -euo pipefail`, validated inputs, functions for repeated logic, meaningful exit codes,
  passed through `shellcheck`.
- Design health-check scripts so their exit code is the actual signal downstream tooling
  (alerting, `cron`, systemd timers) relies on — never just informational `echo` output.
- Structure backup and deploy scripts so a failure at any step stops further destructive action,
  rather than plowing ahead regardless.

## Exercises

1. Take the backup script above and add a check that fails clearly (with `exit 1` and a message)
   if `$BACKUP_DIR` doesn't have enough free disk space before starting.
2. Modify the deploy script to roll back (restart the previous version) if `wait_for_healthy`
   fails for any server.
3. Run `shellcheck` against all three scripts on this page and confirm they pass cleanly.

## Quiz

**Q: Why does the health-check script's exit code matter beyond just its printed output?**
<details><summary>Show answer</summary>
Scheduling and alerting tooling (cron, systemd timers, monitoring systems) typically checks the
exit code, not the printed text, to determine whether the job succeeded — a script that always
exits 0 defeats that entirely, regardless of what it prints.
</details>

**Q: In the backup script, why is the retention-pruning step placed after the tar step succeeds, not before or in parallel?**
<details><summary>Show answer</summary>
Pruning old backups should only happen once a new backup is confirmed successful — pruning before
or regardless of that confirmation risks deleting old, valid backups while leaving only a failed
or incomplete new one.
</details>

**Q: What single flag combination gives a baseline safety net across all three example scripts?**
<details><summary>Show answer</summary>
`set -euo pipefail` — stop on any failing command, treat unset variables as an error, and catch
failures anywhere in a pipeline, not just its last command.
</details>

## Interview questions

- Walk through how you'd structure a production backup script, and what error handling it needs. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Real automation scripts combine every construct from this module: variables, conditionals,
  loops, functions, arguments, and exit-code discipline.
- A script's exit code is a real signal other systems (schedulers, alerting) depend on — never
  let it be meaningless.
- Validate preconditions and check each step's success before proceeding, especially before
  destructive actions.
- This page closes the module — everything after it in the roadmap assumes this level of scripting
  comfort.

## Related topics

- [Debugging Scripts: set -x, shellcheck](debugging-scripts-set-x-shellcheck.md)
- [Module 19: Security](../19-security/index.md)
