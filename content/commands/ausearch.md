---
title: "ausearch — Query the Linux Audit Trail"
description: "Search recorded audit events by key, user, time range, or syscall, translating auditd's raw log format into readable, filterable output."
relatedConcepts: ["19-security/auditing-auditd-ausearch"]
relatedCommands: ["auditd"]
careerRelevance: ["cybersecurity", "linux-administrator", "sre"]
difficulty: "expert"
compatibility: []
updatedAt: "2026-07-28"
keywords: ["ausearch examples", "ausearch by key", "ausearch by user", "query audit log linux"]
canonicalUrl: "/commands/ausearch"
---

# ausearch

🔴 Expert · Relevant for: Cybersecurity · Linux Administrator · SRE

> **TL;DR:** `ausearch -k KEY` retrieves every recorded event matching a tagged audit rule.
> `-ua UID` filters by user, `-ts TIME` by time range — the standard way to query
> `/var/log/audit/audit.log` without reading its raw format directly.

## Purpose

`ausearch` queries the event trail recorded by `auditd` — see
[Auditing: auditd, ausearch](../docs/19-security/auditing-auditd-ausearch.md) for the full
concept.

## Syntax

```
ausearch [OPTIONS]
```

## Arguments

None — entirely option-driven.

## Options

| Flag | Meaning |
|---|---|
| `-k KEY` | Filter to events from a rule tagged with this key |
| `-ua UID` | Filter to events by a specific user ID |
| `-ts TIME` | Filter to events since a given time (`today`, `yesterday`, or a specific timestamp) |
| `-te TIME` | Filter to events up to a given end time |
| `-i` | Interpret numeric IDs (UID, GID) into human-readable names in the output |
| `-m TYPE` | Filter by audit message type (e.g. `SYSCALL`, `EXECVE`) |

## Examples

```
$ sudo ausearch -k shadow_changes
```
Retrieve every event matching the `shadow_changes` tagged rule.

```
$ sudo ausearch -ua 1000 -i
```
Show events for user ID 1000, with names resolved instead of raw numeric IDs.

```
$ sudo ausearch -ts today -k exec_commands
```
Show today's events matching the `exec_commands` rule.

```
$ sudo ausearch -ts yesterday -te today -m SYSCALL
```
Show all syscall-type events between yesterday and today.

## Expected Output

```
$ sudo ausearch -k shadow_changes -i
----
type=PATH msg=audit(07/28/2026 14:32:10.123:456): item=0 name=/etc/shadow ...
type=SYSCALL msg=audit(07/28/2026 14:32:10.123:456): ... uid=root ... comm="passwd" ...
```

## Exit Status

`0` on success (results found or search completed), non-zero on invalid option syntax.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `<no matches>` | No events match the given filter | Confirm the key/time range is correct, and that a matching `auditctl` rule was actually in place when the event occurred |
| Permission denied reading audit log | Not running with sufficient privilege | Run with `sudo` |
| Numeric UIDs instead of usernames in output | `-i` wasn't passed | Add `-i` to interpret IDs into readable names |

## Security Considerations

`ausearch` output can reveal sensitive operational detail (who accessed what, when) — treat query
results with the same care as any other security-sensitive log data, restricting who can run it on
production systems.

## Performance Considerations

Broad, unscoped queries against a large audit log can be slow — always filter by `-k`, `-ua`, or a
time range rather than searching the entire log unscoped, the same discipline as scoping
`journalctl` queries (Module 16).

## Production Usage

`ausearch -k KEY -ts today` is the standard first query during an investigation involving a
specific watched resource — narrowing immediately by the rule's tag and a time window, rather than
searching the full audit trail.

## Related Commands

- [`auditd`](auditd.md) — the daemon and rule-management tool whose recorded events `ausearch`
  queries

## Related Concepts

- [Auditing: auditd, ausearch](../docs/19-security/auditing-auditd-ausearch.md)
