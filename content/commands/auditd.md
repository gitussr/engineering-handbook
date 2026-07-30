---
title: "auditd — The Linux Audit Daemon"
description: "The daemon that records kernel-level security-relevant events per configured rules, managed via auditctl."
relatedConcepts: ["19-security/auditing-auditd-ausearch"]
relatedCommands: ["ausearch"]
careerRelevance: ["cybersecurity", "linux-administrator", "sre"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["auditd command", "auditctl examples", "audit rules linux", "audit.log format"]
canonicalUrl: "/commands/auditd"
---

# auditd

🔴 Expert · Relevant for: Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `auditd` is the daemon; `auditctl` is how you manage its rules at runtime. `auditctl
> -w /path -p wa -k mykey` watches a path for write/attribute changes, tagged with a searchable
> key.

## Purpose

`auditd` records kernel-level security-relevant events (file access, syscalls) per configured
rules — see
[Auditing: auditd, ausearch](../docs/19-security/auditing-auditd-ausearch.md) for the full
concept.

## Syntax

```
systemctl [start|stop|status] auditd
auditctl [OPTIONS]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `PATH` (with `-w`) | The file or directory path to watch | Yes, for a watch rule |

## Options

| Flag | Meaning |
|---|---|
| `-w PATH` | Add a watch rule on a file or directory |
| `-p PERMS` | Permissions to watch: `r`ead, `w`rite, `x`ecute, `a`ttribute change (combinable, e.g. `wa`) |
| `-k KEY` | Tag the rule with a searchable key |
| `-l` | List currently loaded rules |
| `-D` | Delete all currently loaded rules |
| `-a ACTION,FILTER` | Add a rule matching a specific syscall/filter, rather than a file watch |

## Examples

```
$ sudo auditctl -w /etc/shadow -p wa -k shadow_changes
```
Watch `/etc/shadow` for write and attribute-change access, tagged `shadow_changes`.

```
$ sudo auditctl -l
```
List all currently loaded audit rules.

```
$ sudo auditctl -a always,exit -F arch=b64 -S execve -k exec_commands
```
Record every executed command system-wide (a syscall-based rule, rather than a file watch),
tagged `exec_commands`.

```
$ sudo systemctl status auditd
```
Confirm the audit daemon itself is running (Module 11).

## Expected Output

```
$ sudo auditctl -l
-w /etc/shadow -p wa -k shadow_changes
-w /etc/security -p wa -k security_configs
```

## Exit Status

`0` on success, non-zero if a rule is malformed or the daemon isn't running.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| Rules disappear after reboot | Rules were only added at runtime via `auditctl`, not persisted | Add matching rules to `/etc/audit/rules.d/` |
| `Error sending add rule request` | `auditd` isn't running, or the rule syntax is invalid | Check `systemctl status auditd`; verify rule syntax |
| No events appear for a watched path | The watch permissions (`-p`) don't include the access type that occurred | Broaden `-p` to include the relevant access type (`r`/`w`/`x`/`a`) |

## Security Considerations

Audit logs themselves (`/var/log/audit/audit.log`) can contain sensitive information about system
activity and should be protected with restrictive permissions and, ideally, forwarded to a
centralized, tamper-resistant log store — a compromised host's local audit log can be altered or
deleted by an attacker with sufficient privilege.

## Performance Considerations

Overly broad rules (auditing every syscall system-wide) generate substantial log volume and can
measurably impact performance on busy systems — scope rules to genuinely sensitive paths and
specific syscalls rather than blanket coverage.

## Production Usage

Compliance-driven environments typically maintain a standard set of `auditd` rules (watching
`/etc/passwd`, `/etc/shadow`, sudoers files, and privileged command execution) applied
consistently across every server via configuration management, rather than configured ad hoc per
host.

## Related Commands

- [`ausearch`](ausearch.md) — queries the events `auditd` records

## Related Concepts

- [Auditing: auditd, ausearch](../docs/19-security/auditing-auditd-ausearch.md)
