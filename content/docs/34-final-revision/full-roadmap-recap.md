---
title: "Full Roadmap Recap"
description: "A one-line-per-module recap of the entire 34-module Linux roadmap, grouped by stage, for final revision before an interview or a production role."
module: "34-final-revision"
moduleTitle: "Final Revision"
stage: "production-engineer"
difficulty: "must-know"
foundational: true
type: "concept"
prerequisites: ["33-troubleshooting/boot-issues-and-rescue-mode"]
relatedTopics: ["34-final-revision/concept-map-recap", "34-final-revision/command-reference-recap"]
relatedCommands: []
careerRelevance: []
relatedLabs: []
relatedInterviewQuestions: []
relatedCheatsheet: ""
furtherReading: []
nextTopic: "34-final-revision/command-reference-recap"
prevTopic: "33-troubleshooting/boot-issues-and-rescue-mode"
estimatedReadingTime: 9
updatedAt: "2026-07-30"
keywords: ["linux roadmap recap", "linux learning path summary", "linux revision before interview", "34 module linux roadmap"]
canonicalUrl: "/docs/final-revision/full-roadmap-recap"
---

# Full Roadmap Recap

🟢 Must Know · Relevant for: All career paths

> **TL;DR:** All 34 modules, one line each, grouped by stage — use this page to spot-check that
> you can still explain every module's core idea before an interview or a production role, not to
> re-learn anything from scratch.

## What is it?

A compressed, one-line-per-module recap of the entire roadmap, organized the same way it was
taught: Beginner (01-09), Intermediate (10-18), Professional (19-24), and Production Engineer
(25-34). Every line links to that module's own landing page for a full refresher if a line doesn't
immediately make sense.

## Why does it exist?

218 pages across 34 modules is a lot to hold in working memory at once. Before an interview, a new
role, or just periodically, it's more useful to scan a compressed recap and notice which specific
lines don't immediately make sense — those are exactly the modules worth revisiting — than to
re-read everything linearly again.

## Where is it used?

Interview prep, onboarding into a new role that assumes broad Linux fluency, or a periodic
self-check to catch skill decay in modules you haven't touched in a while.

## How it works

Read each line below and ask yourself, honestly, whether you could explain it to someone else in
a sentence or two. If yes, move on. If not, that module's link is exactly where to go — this page
is a map of gaps, not a replacement for the modules themselves.

### Stage: Beginner (Modules 01-09)

| Module | Core idea |
|---|---|
| [01 Linux Basics](../01-linux-basics/index.md) | Linux is a kernel, not an OS by itself; distros package it with GNU tools; servers run it without a GUI because a GUI is pure overhead there. |
| [02 Installing Linux](../02-installing-linux/index.md) | Virtualization options, installing major distros, and spinning up a cloud VM as the modern default environment. |
| [03 Terminal](../03-terminal/index.md) | The terminal, shell, and console are three different things; `pwd`/`ls`/`cd` are the minimum to navigate a filesystem you can't see. |
| [04 Bash](../04-bash/index.md) | Environment variables, `PATH`, redirection, and pipes — the mechanics every shell command chains through. |
| [05 File System](../05-file-system/index.md) | The FHS gives every directory a defined purpose; inodes hold metadata separately from filenames; hard links share data, symlinks just point at a path. |
| [06 Files](../06-files/index.md) | Creating, finding, searching, and compressing files — `grep`, `find`, `tar`, and regex basics. |
| [07 Permissions](../07-permissions/index.md) | Owner/group/other each get independent rwx bits; `chmod 777` is a trap, not a fix; diagnose ownership before bits before ACLs before SELinux. |
| [08 Users](../08-users/index.md) | `/etc/shadow` keeps hashes out of reach of `/etc/passwd`'s world-readability; `sudo` beats `su` for logging and least-privilege. |
| [09 Groups](../09-groups/index.md) | Groups let you grant permissions once to a team, not per user, and manage membership without touching file permissions at all. |

### Stage: Intermediate (Modules 10-18)

| Module | Core idea |
|---|---|
| [10 Processes](../10-processes/index.md) | Every process has a PID/PPID and a lifecycle; `SIGTERM` before `SIGKILL`; zombies are a parent-process bug worth investigating. |
| [11 Services](../11-services/index.md) | systemd supervises services via unit files and targets; `After=` is ordering only, `Requires=` is a real dependency. |
| [12 Networking](../12-networking/index.md) | IP/DNS/routing fundamentals, plus the diagnostic commands (`ping`, `dig`, `ss`) for when any layer breaks. |
| [13 SSH](../13-ssh/index.md) | Key exchange happens before authentication; key-based auth beats passwords; test hardening changes in a second session first. |
| [14 Package Managers](../14-package-managers/index.md) | `apt`/`dpkg` on Debian-based systems, `dnf`/`yum`/`rpm` on RHEL-based systems — same job, different tooling. |
| [15 Storage](../15-storage/index.md) | Partitions, filesystems, LVM snapshots (copy-on-write), and RAID's write-hole problem. |
| [16 Logs](../16-logs/index.md) | `journalctl` and rsyslog are complementary, not competing; `logrotate` is what keeps logs from filling a disk. |
| [17 Cron](../17-cron/index.md) | Cron runs with a minimal environment — absolute paths and explicit `PATH` avoid the classic "works manually, fails under cron" bug. |
| [18 Shell Scripting](../18-shell-scripting/index.md) | Variables, conditionals, loops, functions, and `set -e`'s surprising gaps around pipelines and conditionals. |

### Stage: Professional (Modules 19-24)

| Module | Core idea |
|---|---|
| [19 Security](../19-security/index.md) | DAC vs MAC, SELinux enforcing vs permissive, and a hardening checklist that goes beyond default configuration. |
| [20 Monitoring](../20-monitoring/index.md) | Prometheus pulls and stores metrics over time; alert on sustained, symptom-level thresholds to avoid alert fatigue. |
| [21 Performance](../21-performance/index.md) | Load average isn't CPU utilization; `free`'s "available" column matters more than "free"; confirm a leak via a trend, not a snapshot. |
| [22 Containers](../22-containers/index.md) | Namespaces isolate what a process can see; cgroups limit what it can consume — a container needs both. |
| [23 Docker Basics](../23-docker-basics/index.md) | Images vs containers, Dockerfiles, volumes vs bind mounts, and Compose for declaring a multi-container app as code. |
| [24 Kubernetes Basics](../24-kubernetes-basics/index.md) | Pods are the smallest unit; Deployments manage replicas; Services give Pods a stable identity since Pods themselves are ephemeral. |

### Stage: Production Engineer (Modules 25-34)

| Module | Core idea |
|---|---|
| [25 Linux for Cloud](../25-linux-for-cloud/index.md) | `cloud-init` configures a generic image per-instance; instance roles beat hardcoded credentials. |
| [26 Linux for DevOps](../26-linux-for-devops/index.md) | Every CI/CD pipeline step is ultimately ordinary shell commands in a runner; Terraform provisions, Ansible configures. |
| [27 Linux for Cybersecurity](../27-linux-for-cybersecurity/index.md) | SOC work is mostly log analysis and timeline-building; a CIS Benchmark is hardening you can prove to an auditor. |
| [28 Linux for Web Servers](../28-linux-for-web-servers/index.md) | Nginx's event-driven model scales concurrency better than Apache's process-per-connection model; a 502 means the proxy got no backend response at all. |
| [29 Linux Interview Questions](../../interview-questions/index.md) | Question banks by tier (beginner/intermediate/senior/scenario-based) and by company type, each linking back to the topic that actually teaches the answer. |
| [30 Linux Cheat Sheets](../../cheatsheets/index.md) | Ten single-page quick references — syntax lookup for someone who already understands the concept. |
| [31 Practice Projects](../../projects/index.md) | Multi-session builds across Beginner/Intermediate/Advanced tiers — portfolio-worthy, not single-incident. |
| [32 Real Production Labs](../../labs/index.md) | Ticket/incident-framed hands-on exercises across four tiers, ending in Production on-call simulations. |
| [33 Troubleshooting](../33-troubleshooting/index.md) | A repeatable methodology — define, gather evidence, hypothesize, test narrowly, fix, verify — applied to the seven most common production symptoms. |
| **34 Final Revision** | This module: the roadmap recap, command recap, concept map, readiness self-check, and what to learn next. |

## Real-world example

An engineer preparing for a senior Linux/DevOps interview scans this recap the week before and
notices the Module 15 line on LVM snapshots doesn't immediately make sense anymore — they haven't
touched LVM in over a year. That's a five-minute flag this page exists to surface, caught before
the interview instead of during it.

## Commands

No new command example on this page — see [Command Reference Recap](command-reference-recap.md)
for a recap organized around commands specifically, and the [Cheat Sheets](../../cheatsheets/index.md)
for full syntax lookup.

## Production example

Not applicable in the usual terminal-session sense — this page's "production example" is the
scan-and-flag exercise described above: read every line, and treat any line that doesn't
immediately make sense as an actionable revision target, not a pass/fail grade.

## Common mistakes

- Re-reading the entire roadmap linearly instead of using this page to identify *specific* weak
  modules first.
- Treating every unclear line as equally urgent — prioritize modules most relevant to your actual
  target role first (see [What to Learn Next](what-to-learn-next.md) and the career-path framing
  in each career-branch module).
- Skipping this recap entirely because "I already did the modules once" — spaced revision is what
  actually prevents skill decay, not a single pass through the content months ago.

## Best practices

- Use this page as a diagnostic, not a study guide in itself — every unclear line should send you
  to that module's actual page, not just to a definition you half-remember.
- Revisit this recap periodically (not just before interviews) to catch decay in modules you don't
  use day-to-day in your current role.
- Pair a weak line here with the matching [Interview Questions](../../interview-questions/index.md)
  tier page for that topic to test whether you can produce the answer, not just recognize it.

## Exercises

1. Read every line in this recap and list the three modules you're least confident explaining
   from memory.
2. For one of those three, go to the module's index page and skim its topic list — identify the
   single sub-topic you're weakest on.
3. Test yourself against that sub-topic using the matching tier page in
   [Interview Questions](../../interview-questions/index.md).

## Quiz

**Q: What is this recap page for, and what is it explicitly not a substitute for?**
<details><summary>Show answer</summary>
It's a fast diagnostic for spotting which specific modules need revisiting — it's explicitly not a
substitute for the modules themselves, which is where you go once a gap is identified.
</details>

**Q: Why is scanning for unclear lines more useful than re-reading the whole roadmap linearly before an interview?**
<details><summary>Show answer</summary>
Re-reading everything spends equal time on material you already know solidly; scanning for gaps
concentrates your limited prep time on the specific modules where you'd actually struggle.
</details>

## Interview questions

No single interview question maps to this recap page directly — its entire purpose is preparing
you for the full [Interview Questions Hub](../../interview-questions/index.md), not one specific
answer.

## Key Takeaways

- All 34 modules compress to one line each here — use it to find gaps, not to re-learn anything.
- Any line that doesn't make sense anymore is a five-minute flag pointing at a specific module.
- Pair this recap with [Concept Map Recap](concept-map-recap.md) for how these modules connect,
  not just what each one covers individually.

## Related topics

- [Concept Map Recap](concept-map-recap.md)
- [Command Reference Recap](command-reference-recap.md)
- [Readiness Self-Assessment / Mock Interview](readiness-self-assessment-mock-interview.md)
