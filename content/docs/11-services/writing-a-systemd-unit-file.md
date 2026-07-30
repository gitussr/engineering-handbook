---
title: "Writing a systemd Unit File"
description: "The anatomy of a .service unit file — [Unit], [Service], [Install] — and a complete, working example for a custom application."
module: "11-services"
moduleTitle: "Services"
stage: "intermediate"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["11-services/systemctl-start-stop-enable-disable-status"]
relatedTopics: ["journalctl-basics"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#unit-file-sections"]
relatedCheatsheet: "systemctl"
furtherReading: [{"label": "systemd.service(5) man page", "url": "https://www.freedesktop.org/software/systemd/man/systemd.service.html"}]
nextTopic: "11-services/journalctl-basics"
prevTopic: "11-services/systemctl-start-stop-enable-disable-status"
estimatedReadingTime: 7
updatedAt: "2026-07-25"
keywords: ["systemd unit file example", "systemd service file", "execstart restart wantedby"]
canonicalUrl: "/docs/services/writing-a-systemd-unit-file"
---

# Writing a systemd Unit File

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · SRE

> **TL;DR:** A `.service` unit file has three sections: `[Unit]` (metadata and dependencies),
> `[Service]` (how to run and manage the process), `[Install]` (what `enable` actually wires up).
> `ExecStart=`, `Restart=`, and `WantedBy=` cover most real-world needs.

## What is it?

A unit file is a plain-text configuration file defining how systemd should manage a specific
unit — for a `.service` unit, that means how to start it, restart it on failure, and where it
fits among other units.

## Why does it exist?

Every custom application that needs to run as a proper, systemd-managed service needs a
declarative description of how to run it — a unit file is that description, replacing the
imperative shell scripts SysV init required (this module's final topic) with a structured,
consistent format systemd itself validates and manages.

## Where is it used?

Any custom application, script, or tool that needs to run continuously, restart automatically on
failure, and be manageable via `systemctl` like any built-in service.

## How it works

> 📊 Diagram: a unit file split into its three labeled sections — `[Unit]` (What is this, what
> does it depend on), `[Service]` (How do I actually run and manage this process), `[Install]`
> (What happens when someone runs `enable`) — with the most common directive from each section
> shown alongside its label.

```ini
[Unit]
Description=My Custom Application
After=network.target

[Service]
ExecStart=/opt/myapp/bin/myapp
Restart=on-failure
User=myappuser

[Install]
WantedBy=multi-user.target
```

| Section | Purpose | Key directives |
|---|---|---|
| `[Unit]` | Metadata and dependency declarations | `Description=`, `After=`, `Requires=` (full dependency depth in this module's next-but-one topic) |
| `[Service]` | How to run and manage the process | `ExecStart=` (the command to run), `Restart=` (restart policy), `User=` (run as a specific, non-root account — Module 08) |
| `[Install]` | What `enable` actually wires up | `WantedBy=` (which target should pull this in — see this module's Dependencies/Targets topic) |

`Restart=on-failure` is what gives a service the automatic-restart behavior previewed in
[Module 10](../10-processes/systemd-process-management-preview.md) — without it, a crashed
service simply stays down until manually restarted.

## Real-world example

A team deploys a custom Python application as a proper systemd service instead of running it in a
`tmux` session (Module 03) or a bare `nohup` background process (Module 04) — with a unit file
specifying `Restart=on-failure` and `User=appuser`, the application survives crashes
automatically, runs under a dedicated non-root account (Module 08), and is manageable with the
exact same `systemctl` commands as any built-in service like `nginx`.

## Commands

No new command example on this page — creating a unit file uses a text editor (Module 06);
applying it uses [`systemctl`](../../commands/systemctl.md), already covered.

## Production example

```
$ sudo nano /etc/systemd/system/myapp.service
# (paste the unit file content above)
$ sudo systemctl daemon-reload
$ sudo systemctl enable --now myapp
```

`enable --now` is a convenient shortcut combining `enable` and `start` in one command — exactly
the pairing recommended in this module's previous topic.

## Do / Don't

| Do | Don't |
|---|---|
| Run `daemon-reload` after creating or editing a unit file | Expect changes to take effect without reloading systemd's configuration |
| Set `User=` to a dedicated, non-root account | Run a custom service as root without a specific reason |
| Set `Restart=on-failure` for anything that should self-heal | Leave a custom service without a restart policy and require manual recovery |

## Common mistakes

- Forgetting `systemctl daemon-reload` after creating or editing a unit file — systemd won't see
  the change until its configuration is reloaded.
- Leaving out `User=`, causing a custom service to run as root by default with no specific reason
  — a real, avoidable security exposure (Module 07/08's least-privilege principle applied here).
- Omitting `Restart=on-failure` and being surprised a crashed service doesn't come back on its
  own.

## Best practices

- Always run `daemon-reload` after unit file changes, before testing.
- Set `User=` to a dedicated service account for any custom application, following the same
  reasoning as [Module 08's account overview](../08-users/user-accounts-overview.md).
- Default to `Restart=on-failure` for anything expected to run continuously and recover from
  transient crashes on its own.

## Exercises

1. Write a minimal unit file for a simple test script.
2. Reload systemd's configuration and start your test service with `systemctl enable --now`.
3. Explain in one sentence why `daemon-reload` is a required step after editing a unit file.

## Quiz

**Q: What does `Restart=on-failure` do?**
<details><summary>Show answer</summary>
Configures systemd to automatically restart the service if it exits with a failure status,
without needing manual intervention.
</details>

**Q: Why is `daemon-reload` necessary after editing a unit file?**
<details><summary>Show answer</summary>
systemd caches unit file contents; `daemon-reload` tells it to re-read them from disk so the
changes actually take effect.
</details>

## Interview questions

- Walk through the three main sections of a systemd unit file and what each controls. →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- A unit file has three sections: `[Unit]` (metadata/dependencies), `[Service]` (how to run it),
  `[Install]` (what `enable` wires up).
- `ExecStart=`, `Restart=`, and `WantedBy=` cover most real-world custom service needs.
- Always run `daemon-reload` after creating or editing a unit file.
- Run custom services under a dedicated, non-root `User=` by default.

## Further Reading

- [systemd.service(5) man page](https://www.freedesktop.org/software/systemd/man/systemd.service.html)

## Related topics

- [journalctl Basics](journalctl-basics.md)
- [Service Dependencies and Targets](service-dependencies-and-targets.md)
