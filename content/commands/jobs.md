---
title: "jobs — List Background and Stopped Jobs"
description: "List the background and stopped jobs running in the current shell session."
relatedConcepts: ["04-bash/job-control"]
relatedCommands: []
careerRelevance: ["devops", "sre", "linux-administrator"]
difficulty: "good-to-know"
updatedAt: "2026-07-25"
keywords: ["jobs command linux", "list background jobs bash", "job control jobs command"]
canonicalUrl: "/commands/jobs"
---

# jobs

🟡 Good to Know · Relevant for: DevOps · SRE · Linux Administrator

> **TL;DR:** Lists the background and stopped jobs tied to your current shell session, each with
> a job number you can reference with `%N` in `fg`/`bg`.

## Purpose

`jobs` lists the background and stopped jobs associated with the current shell session — the
inspection command for the job control mechanics covered in
[Job Control](../docs/04-bash/job-control.md).

## Syntax

```
jobs [OPTIONS]
```

## Arguments

None.

## Options

| Flag | Long form | Meaning |
|---|---|---|
| `-l` | | Include process IDs (PIDs) alongside job numbers |
| `-r` | | Show only running jobs |
| `-s` | | Show only stopped jobs |

## Examples

```
$ jobs
```
List all jobs in the current session.

```
$ jobs -l
```
List jobs with their PIDs included.

## Expected Output

```
$ jobs
[1]-  Running                 long-task &
[2]+  Stopped                 vim notes.txt
```

The `+` marks the "current" job (what `fg`/`bg` act on by default without an argument); `-`
marks the "previous" job.

## Exit Status

`0` on success, non-zero if job control isn't active in the current shell (e.g. inside a script,
where job control is typically disabled by default).

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `jobs` shows nothing despite a background task | The task was started in a different shell session | `jobs` only shows jobs tied to the current shell — check the session where it was started |
| Job disappears after closing the terminal | Backgrounded jobs are tied to the session unless started with `nohup` | Use `nohup` or a multiplexer for anything that must survive a disconnect |

## Security Considerations

None specific to `jobs` — it only reports on the current shell's own job list.

## Performance Considerations

Negligible.

## Production Usage

Checking `jobs` before closing an SSH session is a small but real habit that prevents
accidentally killing background work that wasn't protected with `nohup` or a multiplexer.

## Related Commands

None documented yet — `bg`, `fg`, and `nohup` are forward references pending their own canonical
pages.

## Related Concepts

- [Job Control](../docs/04-bash/job-control.md)
