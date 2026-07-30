---
title: "Building a CI/CD Pipeline on Linux Servers"
description: "Assembling this module's pieces into one working pipeline: a self-hosted runner, shell-scripted build/test steps, and a systemd-managed deployment — no specific vendor platform required to understand what's actually happening."
module: "26-linux-for-devops"
moduleTitle: "Linux for DevOps"
stage: "production-engineer"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["26-linux-for-devops/devops-toolchain-on-linux-end-to-end"]
relatedTopics: ["devops-toolchain-on-linux-end-to-end", "linux-role-in-ci-cd"]
relatedCommands: ["systemctl", "journalctl"]
careerRelevance: ["devops", "sre", "platform", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["senior#build-cicd-pipeline-from-scratch"]
relatedCheatsheet: ""
furtherReading: [{"label": "systemd.service Man Page", "url": "https://man7.org/linux/man-pages/man5/systemd.service.5.html"}]
nextTopic: "27-linux-for-cybersecurity/linux-for-soc-analysts"
prevTopic: "26-linux-for-devops/devops-toolchain-on-linux-end-to-end"
estimatedReadingTime: 10
updatedAt: "2026-07-29"
keywords: ["build ci cd pipeline from scratch", "self-hosted ci runner linux", "systemd deployment pipeline", "shell script build test deploy", "linux ci cd pipeline example"]
canonicalUrl: "/docs/linux-for-devops/building-a-cicd-pipeline-on-linux-servers"
---

# Building a CI/CD Pipeline on Linux Servers

🔴 Expert · Relevant for: DevOps · SRE · Platform · Software Engineering

> **TL;DR:** A working CI/CD pipeline can be built from primitives already covered in this
> roadmap — a script watching for new commits, shell-scripted build/test steps with real exit-code
> handling, and a systemd-managed deployment — with no specific vendor platform required to
> understand what's actually happening underneath one.

## What is it?

A hands-on assembly of this module's concepts into one working pipeline running entirely on plain
Linux servers: a self-hosted runner process, build and test steps as real shell scripts, and
deployment handled by systemd — deliberately avoiding a specific vendor product so the underlying
mechanics stay visible.

## Why does it exist?

Every managed CI/CD platform (GitHub Actions, GitLab CI, Jenkins) hides the same underlying
mechanics behind a polished UI and YAML syntax. Building a minimal version from scratch, using only
tools already covered in this roadmap, makes debugging any vendor platform easier afterward,
because the mental model underneath — however it's dressed up — is the same one built here.

## Where is it used?

Small teams or specific workloads that genuinely benefit from a self-hosted, lightweight pipeline
instead of a full platform; more broadly, this exact exercise is one of the fastest ways to
understand what a managed CI/CD platform is actually doing for you, which is valuable even for
engineers who will only ever use a managed platform in production.

## How it works

> 📊 Diagram: a webhook or polling script detecting a new Git commit, triggering a shell script
> that runs build and test steps with explicit exit-code checks (Module 18), and on success,
> restarting a systemd-managed service (Module 11) running the new build — each stage labeled with
> the specific earlier module it draws on.

**1. Trigger** — detect a new commit, either via a webhook received by a small listener process, or
by polling with cron (Module 17):

```bash
#!/bin/bash
# poll-and-deploy.sh
LAST_COMMIT=$(cat /var/lib/ci/last-commit 2>/dev/null || echo "")
CURRENT_COMMIT=$(git -C /srv/app rev-parse HEAD)

if [ "$CURRENT_COMMIT" != "$LAST_COMMIT" ]; then
  /usr/local/bin/build-and-deploy.sh && echo "$CURRENT_COMMIT" > /var/lib/ci/last-commit
fi
```

**2. Build and test** — real shell-scripting discipline from
[Module 18](../18-shell-scripting/exit-codes-and-error-handling.md), failing loudly instead of
silently:

```bash
#!/bin/bash
set -euo pipefail

cd /srv/app
git pull
npm install
npm test || { echo "Tests failed, aborting deploy" >&2; exit 1; }
npm run build
```

**3. Deploy** — hand off the running application to systemd (Module 11), which already handles
restart-on-failure and logging via `journalctl`:

```bash
sudo systemctl restart myapp.service
sudo systemctl status myapp.service --no-pager
```

## Real-world example

A small team building this exact pipeline discovers, the first time a test genuinely fails, that
their deploy script doesn't actually stop — it continues to the `systemctl restart` step anyway,
because the earlier build step wasn't checking its exit code, only printing its output. Adding
`set -euo pipefail` and an explicit `|| exit 1` after the test step (the exact pattern from
[Module 18](../18-shell-scripting/debugging-scripts-set-x-shellcheck.md)) turns a silent,
dangerous deploy-on-broken-build into a pipeline that correctly halts — the same lesson a managed
CI/CD platform enforces automatically, made visible here because there's no platform hiding it.

## Syntax

No new syntax — this page assembles Bash scripting (Module 18), cron/webhooks (Module 17), and
systemd (Module 11) syntax already covered.

## Commands

See [`systemctl`](../../commands/systemctl.md) (Module 11) for the deployment step and
[`journalctl`](../../commands/journalctl.md) (Module 11, extended in Module 16) for reading the
deployed service's logs after a rollout.

## Production example

```
$ sudo systemctl restart myapp.service
$ sudo journalctl -u myapp.service -n 20 --no-pager
Jul 29 14:02:11 web-01 myapp[8821]: Server listening on :8080
Jul 29 14:02:11 web-01 systemd[1]: Started myapp.service.
```

Confirming a deployment succeeded by reading the newly restarted service's own log output — the
same verification step a managed platform's "deployment successful" indicator is ultimately based
on.

## Do / Don't

| Do | Don't |
|---|---|
| Check every build/test step's exit code explicitly | Continue a pipeline after a silent failure |
| Let systemd manage the deployed process's lifecycle | Run the application as a detached, unmanaged background process |
| Verify a deployment via logs, not just "the script finished" | Assume no error output means success |

## Common mistakes

- Writing a build/deploy script without `set -euo pipefail` or explicit exit-code checks, letting a
  failed test or build silently proceed to deployment.
- Running the deployed application as a bare background process instead of a systemd service,
  losing automatic restart-on-crash and centralized logging.
- Treating "the script finished with no visible error" as proof of success instead of verifying the
  actual deployed service's health and logs.

## Best practices

- Apply the exact defensive scripting habits from Module 18 to every pipeline script — a pipeline
  is exactly the kind of unattended, must-not-fail-silently context those habits exist for.
- Let systemd own the deployed process's lifecycle rather than reinventing process supervision.
- Verify every deployment by checking the actual running service afterward, not just trusting the
  deploy script's own success message.

## Exercises

1. Write a minimal deploy script with `set -euo pipefail` and an explicit test-failure check, then
   deliberately break a test to confirm the script actually halts.
2. Convert a bare `nohup`-backgrounded application into a proper systemd service, and explain what
   capability that adds over the bare background process.
3. Using `journalctl -u`, verify a deployment actually succeeded rather than just checking the
   deploy script's exit code.

## Quiz

**Q: What happens to a deploy script without `set -euo pipefail` if an earlier step (like tests) fails silently?**
<details><summary>Show answer</summary>
The script continues to later steps (including deployment) despite the failure, unless every step
is explicitly exit-code-checked — exactly the failure mode `set -euo pipefail` prevents.
</details>

**Q: Why hand a deployed application off to systemd instead of running it as a bare background process?**
<details><summary>Show answer</summary>
systemd provides automatic restart-on-crash and centralized logging via `journalctl`, capabilities
a bare `nohup`-backgrounded process doesn't have.
</details>

**Q: What's the most reliable way to confirm a deployment actually succeeded?**
<details><summary>Show answer</summary>
Checking the actual running service's status and logs (`systemctl status`, `journalctl -u`), not
just trusting that the deploy script finished without printing an error.
</details>

## Interview questions

- Walk through how you'd build a minimal CI/CD pipeline using only Bash, cron/webhooks, and
  systemd. →
  [Full answer in Senior/Expert Interview Questions](../../interview-questions/senior.md)

## Key Takeaways

- A working CI/CD pipeline is buildable from primitives already covered in this roadmap: shell
  scripting, cron/webhooks, and systemd.
- Explicit exit-code handling (`set -euo pipefail`) is what prevents a silent failure from reaching
  deployment — the exact protection a managed platform provides automatically.
- systemd should own the deployed process's lifecycle, providing restart-on-crash and logging.
- Understanding this minimal pipeline makes debugging any managed CI/CD platform easier, since the
  underlying mechanics are the same.
- This closes Module 26 and the DevOps career branch — Module 27 continues into Linux for
  Cybersecurity.

## Further Reading

- [systemd.service Man Page](https://man7.org/linux/man-pages/man5/systemd.service.5.html)

## Related topics

- [The DevOps Toolchain on Linux, End to End](devops-toolchain-on-linux-end-to-end.md)
- [Linux's Role in CI/CD](linux-role-in-ci-cd.md)
