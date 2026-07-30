---
title: "Environment Variables"
description: "What environment variables are, how they differ from shell variables, and how to view, set, and export them."
module: "04-bash"
moduleTitle: "Bash"
stage: "beginner"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["04-bash/shells-overview"]
relatedTopics: ["shell-config-files", "path-variable"]
relatedCommands: ["export"]
careerRelevance: ["devops", "linux-administrator", "backend", "software-engineering"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#environment-variables"]
relatedCheatsheet: "linux-commands"
furtherReading: []
nextTopic: "04-bash/shell-config-files"
prevTopic: "04-bash/shells-overview"
estimatedReadingTime: 6
updatedAt: "2026-07-25"
keywords: ["environment variables linux", "export command", "shell variable vs environment variable"]
canonicalUrl: "/docs/bash/environment-variables"
---

# Environment Variables

🟢 Must Know · Relevant for: DevOps · Linux Administrator · Backend · Software Engineering

> **TL;DR:** Environment variables are named values available to a shell and every process it
> starts. A plain shell variable (`VAR=value`) only exists in your current shell; `export VAR`
> makes it inherited by child processes too — that one distinction explains most "why doesn't my
> variable show up" confusion.

## What is it?

An environment variable is a named value stored in the shell's environment, accessible by name
(`$VARNAME`) from the shell and from any program the shell starts. `PATH`, `HOME`, and `SHELL`
are all environment variables you've already seen referenced in this documentation.

## Why does it exist?

Programs need configuration and context (where to find executables, who the current user is,
which shell is running) without hardcoding it. Environment variables give every process a shared,
inheritable way to receive that context from whatever started it, without needing config files or
command-line arguments for information that rarely changes.

## Where is it used?

- Configuration for CLI tools and applications (API keys, feature flags, environment names like
  `production`/`staging`)
- System-level context (`PATH`, `HOME`, `USER`, `SHELL`)
- CI/CD pipelines, where environment variables are the standard way secrets and config are
  injected into a build or deployment
- Docker containers, where `ENV` in a Dockerfile sets environment variables for everything running
  inside

## How it works

> 📊 Diagram: two boxes — "Shell variable (local only)" showing `VAR=value` staying inside the
> current shell, and "Environment variable (exported)" showing `export VAR=value` flowing down
> into a child process started from that shell — to make the inheritance distinction visual.

- **Shell variable**: `VAR=value` — exists only in the current shell session, not visible to
  programs it starts.
- **Environment variable**: `export VAR=value` — marks the variable for inheritance, so any child
  process (a script, a program) started from this shell can see it too.
- Environment variables are read with `$VARNAME` or `${VARNAME}` (the braces form is safer when
  concatenating, e.g. `${VARNAME}_suffix`).
- `env` or `printenv` lists all currently exported environment variables; `set` (bash built-in)
  lists all variables including non-exported shell variables.

## Real-world example

A CI/CD pipeline needs a database password to run migrations during deployment. Instead of
hardcoding it into a script (a security risk if that script is ever committed to version
control), the pipeline injects it as an environment variable (`DB_PASSWORD`) at runtime — the
script reads `$DB_PASSWORD`, the actual secret never touches the codebase. This pattern is the
default way secrets and per-environment config are handled across nearly all modern deployment
tooling.

## Commands

- [`export`](../../commands/export.md) — mark a variable for inheritance by child processes

## Production example

```
$ MY_VAR=hello
$ bash -c 'echo $MY_VAR'
       # (empty — not exported, invisible to the child bash process)
$ export MY_VAR=hello
$ bash -c 'echo $MY_VAR'
hello
```

This is the exact experiment that makes the shell-variable-vs-environment-variable distinction
click — the only difference between the two commands is `export`.

## Do / Don't

| Do | Don't |
|---|---|
| `export` a variable that a child process/script needs to see | Assume a plain shell variable is automatically visible to scripts you run |
| Use environment variables for secrets/config injected at runtime | Hardcode secrets directly into scripts or source code |
| Use `${VAR}` braces when concatenating with other text | Use bare `$VAR` when it could be ambiguous with surrounding characters |

## Common mistakes

- Setting a variable without `export` and being confused why a script or program doesn't see it —
  the single most common beginner environment variable mistake.
- Hardcoding secrets into scripts instead of injecting them as environment variables — a real
  security risk if the script is ever shared or committed to version control.
- Confusing `$VAR` (read the value) with `VAR` (the name) — a frequent source of subtle bugs.

## Best practices

- Export anything a child process or script needs to see; keep purely local values as plain shell
  variables.
- Never hardcode secrets — use environment variables (or a proper secrets manager) instead.
- Use `env` or `printenv` to inspect the full exported environment when debugging a "why can't my
  script see this variable" issue.

## Exercises

1. Set a plain shell variable, then try to read it from a subshell (`bash -c 'echo $VARNAME'`) to
   confirm it's empty.
2. Export the same variable and repeat the test — confirm it now shows up.
3. Run `env` and identify three environment variables you didn't set yourself.

## Quiz

**Q: What's the practical difference between `VAR=value` and `export VAR=value`?**
<details><summary>Show answer</summary>
`export` makes the variable inheritable by any child process started from that shell; a plain
shell variable stays local to the current shell only.
</details>

**Q: Why are environment variables preferred over hardcoding secrets in scripts?**
<details><summary>Show answer</summary>
Hardcoded secrets end up in version control history and are hard to rotate; environment variables
can be injected at runtime and kept out of the codebase entirely.
</details>

## Interview questions

- How would you pass a secret to a script without hardcoding it? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Environment variables are inheritable by child processes; plain shell variables are not.
- `export VAR=value` is the mechanism that makes the difference.
- Environment variables are the standard way to inject config and secrets at runtime, especially
  in CI/CD and containers.
- `env`/`printenv` inspects the full exported environment; `set` shows all shell variables too.

## Related topics

- [Shell Config Files](shell-config-files.md)
- [The PATH Variable](path-variable.md)
