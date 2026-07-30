---
title: "Command Reference Recap"
description: "A recap of the roadmap's command surface organized by task, not by module — which command to reach for, pointing to the full command reference and cheat sheets for syntax."
module: "34-final-revision"
moduleTitle: "Final Revision"
stage: "production-engineer"
difficulty: "must-know"
foundational: true
type: "concept"
prerequisites: ["34-final-revision/full-roadmap-recap"]
relatedTopics: ["34-final-revision/full-roadmap-recap", "34-final-revision/concept-map-recap"]
relatedCommands: []
careerRelevance: []
relatedLabs: []
relatedInterviewQuestions: ["beginner#ps-aux-vs-ps-ef", "intermediate#docker-vs-containerd-vs-cri-o"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "34-final-revision/concept-map-recap"
prevTopic: "34-final-revision/full-roadmap-recap"
estimatedReadingTime: 6
updatedAt: "2026-07-30"
keywords: ["linux command reference recap", "which linux command to use", "linux commands by task", "linux command cheat sheet summary"]
canonicalUrl: "/docs/final-revision/command-reference-recap"
---

# Command Reference Recap

🟢 Must Know · Relevant for: All career paths

> **TL;DR:** This page recaps *which* command to reach for, by task, not the flags themselves —
> for exact syntax, go to [/commands](/commands) or the matching [cheat sheet](../../cheatsheets/index.md).

## What is it?

A task-oriented recap of the ~100 canonical commands taught across this roadmap — organized by
"what are you trying to do" rather than by which module introduced each one, since that's how
you'll actually need to recall them under pressure.

## Why does it exist?

By the end of 34 modules, the challenge isn't remembering that `grep` exists — it's recalling,
under time pressure, exactly which of several similar-looking commands fits a specific task
(`ss` vs `netstat`, `dig` vs `nslookup`, `du` vs `df`). This page groups commands by task to make
that recall faster than searching module-by-module.

## Where is it used?

Quick recall during an interview, a live incident, or just sanity-checking "is there a better
command for this than the one I always default to."

## How it works

| Task | Reach for | Not |
|---|---|---|
| Navigate/inspect the filesystem | [`ls`](../../commands/ls.md), [`find`](../../commands/find.md), [`stat`](../../commands/stat.md) | `locate` (index can be stale) |
| Search text | [`grep`](../../commands/grep.md) | `awk`/`sed` for simple matching (more power than you need) |
| Check what's listening on a port | [`ss`](../../commands/ss.md) | `netstat` (legacy, still works, `ss` is current) |
| Check DNS resolution directly | [`dig`](../../commands/dig.md) | `nslookup` (older, less detail) |
| Check what's filling a disk | [`df`](../../commands/df.md) then [`du`](../../commands/du.md) | Guessing at a path with `du` first |
| Check current process/resource usage | [`ps`](../../commands/ps.md), [`top`](../../commands/top.md) | `top` alone for scripting (use `ps` — parseable output) |
| Manage a service | [`systemctl`](../../commands/systemctl.md) | Directly killing/starting the process by PID |
| Read a service's logs | [`journalctl`](../../commands/journalctl.md) `-u {service}` | Grepping `/var/log/syslog` for systemd-managed services |
| Connect to a remote server | [`ssh`](../../commands/ssh.md) | Anything without key-based auth in production |
| Change file permissions | [`chmod`](../../commands/chmod.md) | `chown`-ing to work around what's actually a permission-bit problem |
| Change ownership | [`chown`](../../commands/chown.md) | `chmod 777` as a substitute for a real ownership fix |

## Real-world example

Under interview pressure, "how do you check what's listening on port 443" is a question worth
having an instant, confident answer to (`ss -tlnp | grep 443`) rather than reconstructing it from
`netstat` syntax half-remembered from years ago — this recap exists to keep the *current*, not
just historically-correct, answer on top of mind.

## Commands

This entire page is a commands recap — see the full canonical list at
[Commands Index](../../commands/index.md) and the nine task-grouped
[Cheat Sheets](../../cheatsheets/index.md) for exact flags and syntax.

## Production example

Not applicable as a single terminal session — this page's value is the table above, used as a
lookup during real work rather than demonstrated as one example.

## Common mistakes

- Defaulting to whichever command you learned first (`netstat`, `nslookup`) instead of the current
  standard tool (`ss`, `dig`) — both still work, but interviewers and modern documentation expect
  the current one.
- Trying to recall exact flags from memory instead of keeping the [Cheat Sheets](../../cheatsheets/index.md)
  bookmarked for actual syntax — this recap is for *which* command, not flag-by-flag recall.
- Not practicing saying the command names and their purpose out loud before an interview — reading
  recognition is a much lower bar than confident, unprompted recall.

## Best practices

- Know *which* command fits a task cold; look up exact flags when you need them — that split is
  normal and expected, not a knowledge gap.
- Default to the modern tool (`ss`, `dig`, `ip`) over its legacy equivalent (`netstat`, `nslookup`,
  `ifconfig`) unless a specific environment only has the older one installed.
- Revisit the [Cheat Sheets](../../cheatsheets/index.md) periodically even for commands you use
  daily — flags you don't use often are the ones you forget first.

## Exercises

1. Cover the "Reach for" column above and try to fill it in from the "Task" column alone.
2. Pick three commands from this page you use least often and look up one flag for each you
   didn't already know, using the matching cheat sheet.
3. Explain out loud, in one sentence each, why `ss` is generally preferred over `netstat` today.

## Quiz

**Q: What's the difference in purpose between this recap page and the Cheat Sheets?**
<details><summary>Show answer</summary>
This page recaps *which* command to reach for, by task; the Cheat Sheets give the exact flags and
syntax once you already know which command you need.
</details>

**Q: Why prefer `ss` over `netstat`, or `dig` over `nslookup`, today?**
<details><summary>Show answer</summary>
Both older tools still work, but `ss` and `dig` are the current, actively-maintained standard
tools with more detailed and consistent output — using them is expected in modern documentation
and interviews alike.
</details>

## Interview questions

- What's the difference between `ps aux` and `ps -ef`? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md#ps-aux-vs-ps-ef)
- What's the difference between Docker, containerd, and CRI-O? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md#docker-vs-containerd-vs-cri-o)

## Key Takeaways

- Recall commands by task first — the exact flags are one cheat-sheet lookup away.
- Prefer current tools (`ss`, `dig`, `ip`) over legacy equivalents when both would work.
- This page complements the [Cheat Sheets](../../cheatsheets/index.md); it doesn't replace them.

## Related topics

- [Full Roadmap Recap](full-roadmap-recap.md)
- [Concept Map Recap](concept-map-recap.md)
- [Cheat Sheets](../../cheatsheets/index.md)
