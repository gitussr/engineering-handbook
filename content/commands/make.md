---
title: "make — Build Software from a Makefile"
description: "Read a Makefile's build rules and compile only what's changed since the last build — the compilation step of building software from source."
relatedConcepts: ["14-package-managers/building-from-source-make-configure"]
relatedCommands: []
careerRelevance: ["devops", "linux-administrator", "software-engineering"]
difficulty: "good-to-know"
compatibility: []
updatedAt: "2026-07-27"
keywords: ["make command explained", "makefile targets", "make install", "make clean"]
canonicalUrl: "/commands/make"
---

# make

🟡 Good to Know · Relevant for: DevOps · Linux Administrator · Software Engineering

> **TL;DR:** `make` reads a `Makefile` in the current directory and builds the default target —
> typically compiling source code. `make install` runs the install target, usually copying built
> files into place. `make` only rebuilds what's actually changed since the last build.

## Purpose

`make` executes build rules defined in a `Makefile` — see
[Building from Source: make, ./configure](../docs/14-package-managers/building-from-source-make-configure.md)
for how it fits into the full configure/make/install sequence.

## Syntax

```
make [OPTIONS] [TARGET]
```

## Arguments

| Argument | Meaning | Required |
|---|---|---|
| `TARGET` | A named build target defined in the `Makefile` (e.g. `install`, `clean`) | No — defaults to the `Makefile`'s first/default target |

## Options

| Flag | Meaning |
|---|---|
| `-j N` | Run up to N build jobs in parallel (faster on multi-core systems) |
| `-C DIR` | Run as if started in DIR instead of the current directory |
| `-n` | Dry run — show what would be executed without doing it |

## Examples

```
$ make
```
Build the default target (typically compiles the project).

```
$ make -j4
```
Build using 4 parallel jobs, significantly faster on a multi-core machine.

```
$ sudo make install
```
Run the `install` target, typically copying built files into system directories.

```
$ make clean
```
Run the `clean` target, typically removing previously built files.

## Expected Output

```
$ make
gcc -c main.c -o main.o
gcc -c utils.c -o utils.o
gcc main.o utils.o -o myapp
```

## Exit Status

`0` on a successful build, non-zero if any build rule fails.

## Common Errors

| Symptom | Cause | Fix |
|---|---|---|
| `No rule to make target` | Target name doesn't exist in the `Makefile`, or a required source file is missing | Check the `Makefile` for actual target names |
| `Makefile: No such file or directory` | `make` run outside the project's source directory | `cd` into the correct directory (or run `./configure` first if it hasn't generated the `Makefile` yet) |
| Compiler errors during build | Missing a required development library/header | Install the missing dependency (often named `libX-dev` on Debian/Ubuntu or `libX-devel` on RHEL-family) |

## Security Considerations

Building from source runs arbitrary build scripts and compiles arbitrary code — only build source
obtained from a trusted origin, the same caution applied to any executable code.

## Performance Considerations

`-j N` (matching or slightly exceeding CPU core count) meaningfully speeds up large builds by
compiling independent files in parallel. `make` also only rebuilds files that changed since the
last build, making repeated builds during development fast.

## Production Usage

`make -j$(nproc)` (using all available cores) is the standard pattern for CI pipelines and local
development builds where build time matters.

## Related Commands

None documented separately — `./configure` (project-generated, not a standalone system command)
is the step that typically runs before `make`.

## Related Concepts

- [Building from Source: make, ./configure](../docs/14-package-managers/building-from-source-make-configure.md)
