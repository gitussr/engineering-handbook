---
title: "Linux Interview Questions — Beginner"
description: "Beginner-level Linux interview questions covering distros, the terminal and shell, files and permissions, users and groups, and processes — each with a concise answer and a link to the full topic explanation."
type: "interview"
tier: "beginner"
updatedAt: "2026-07-29"
keywords: ["linux interview questions for freshers", "beginner linux interview questions", "basic linux interview questions and answers", "linux fundamentals interview questions"]
canonicalUrl: "/interview-questions/beginner"
---

# Linux Interview Questions — Beginner

Questions a beginner-to-1-2-years-experience Linux candidate should expect: what Linux actually
is, the terminal and shell, files and the filesystem, permissions, users/groups, and processes.
Each answer is intentionally short — click "Full explanation" for the complete topic page with
examples, common mistakes, and production context.

## Linux basics

<a id="what-is-linux"></a>
**Q: Is Linux an operating system or a kernel?**
<details><summary>Show answer</summary>
Strictly, Linux is the kernel — the program that manages hardware, processes, and memory. "A Linux
system" (like Ubuntu or RHEL) is the kernel bundled with GNU utilities and other software into a
full distribution. Precision matters here because interviewers use this to check you understand
the layering, not just the marketing term.

Full explanation → [What is Linux](../docs/01-linux-basics/what-is-linux.md)
</details>

<a id="kernel-vs-os"></a>
**Q: What's the difference between the kernel, the OS, and a distribution?**
<details><summary>Show answer</summary>
The kernel manages hardware/processes/memory; the OS layer adds GNU utilities and system services
on top of the kernel; a distribution (Ubuntu, RHEL, Arch) packages the kernel, OS layer, package
manager, and defaults into something installable and usable.

Full explanation → [Kernel vs OS vs Distro](../docs/01-linux-basics/kernel-vs-os-vs-distro.md)
</details>

<a id="distro-differences"></a>
**Q: How do Linux distributions actually differ from each other, if they all share the same kernel?**
<details><summary>Show answer</summary>
They differ in package manager and format (`apt`/.deb vs `dnf`/.rpm vs `pacman`), release model
(fixed vs rolling), default configuration, and target use case (server stability vs desktop
polish vs minimalism) — not in the kernel itself, which is largely shared.

Full explanation → [Linux Distributions](../docs/01-linux-basics/linux-distributions.md)
</details>

<a id="gui-vs-cli"></a>
**Q: Why do Linux servers typically run without a GUI?**
<details><summary>Show answer</summary>
A GUI consumes CPU, memory, and disk that a server needs for its actual workload, and it adds
attack surface and packages that need patching for no operational benefit — the CLI does
everything a server needs, remotely, over SSH, with far less overhead.

Full explanation → [GUI vs CLI](../docs/01-linux-basics/gui-vs-cli.md)
</details>

<a id="linux-in-industry"></a>
**Q: Where is Linux actually used in production, beyond desktops?**
<details><summary>Show answer</summary>
The overwhelming majority of cloud servers, container hosts, embedded devices, and the world's
top supercomputers run Linux — it dominates server-side and infrastructure computing even where
it's nearly invisible on personal desktops.

Full explanation → [Linux in Industry](../docs/01-linux-basics/linux-in-industry.md)
</details>

## Terminal and shell

<a id="terminal-vs-shell"></a>
**Q: What's the difference between a terminal, a shell, and a console?**
<details><summary>Show answer</summary>
The terminal is the window/interface you type into; the shell is the program (bash, zsh) that
actually reads and runs your commands inside it; the console traditionally refers to the physical
or virtual text display directly attached to the machine.

Full explanation → [Terminal vs Shell vs Console](../docs/03-terminal/terminal-vs-shell-vs-console.md)
</details>

<a id="pwd-ls-cd"></a>
**Q: What do `pwd`, `ls`, and `cd` do, and why are they usually the first three commands taught?**
<details><summary>Show answer</summary>
`pwd` prints your current directory, `ls` lists its contents, and `cd` changes into another
directory — together they're the minimum needed to navigate a filesystem you can't see, which is
why every other command builds on being able to do this first.

Full explanation → [pwd, ls, cd](../docs/03-terminal/pwd-ls-cd.md)
</details>

<a id="man-pages"></a>
**Q: How do you find out what a command's flags do without searching online?**
<details><summary>Show answer</summary>
Run `man {command}` for the full manual page, or `{command} --help` for a quick summary — man
pages are installed locally and are the authoritative, always-available reference for GNU/Linux
commands.

Full explanation → [man, help, tldr](../docs/03-terminal/man-help-tldr.md)
</details>

<a id="bash-vs-sh"></a>
**Q: What's the difference between `bash` and `sh`?**
<details><summary>Show answer</summary>
`sh` refers to the POSIX-standard shell (often a minimal shell like `dash` on Ubuntu); `bash` is a
specific, feature-rich shell that's a superset of POSIX `sh` with extras like arrays and `[[ ]]`
tests. Scripts written for `bash` can break silently if actually executed under `sh`.

Full explanation → [Shells Overview](../docs/04-bash/shells-overview.md)
</details>

<a id="path-variable"></a>
**Q: What is the `PATH` environment variable, and what happens if a command "isn't found"?**
<details><summary>Show answer</summary>
`PATH` is a colon-separated list of directories the shell searches, in order, to locate an
executable when you type its name. "Command not found" means the binary isn't in any directory
listed in `PATH` (or doesn't exist at all).

Full explanation → [The PATH Variable](../docs/04-bash/path-variable.md)
</details>

<a id="redirection-operators"></a>
**Q: What's the difference between `>`, `>>`, and `2>` in the shell?**
<details><summary>Show answer</summary>
`>` overwrites a file with a command's standard output, `>>` appends to it instead of overwriting,
and `2>` redirects standard error specifically (file descriptor 2) rather than standard output
(file descriptor 1).

Full explanation → [I/O Redirection](../docs/04-bash/io-redirection.md)
</details>

<a id="pipes"></a>
**Q: What does a pipe (`|`) do, and why is it fundamental to the Unix philosophy?**
<details><summary>Show answer</summary>
A pipe connects one command's standard output directly to the next command's standard input,
letting small single-purpose tools be chained into a larger pipeline (`ps aux | grep nginx`)
instead of needing one monolithic tool that does everything.

Full explanation → [Pipes](../docs/04-bash/pipes.md)
</details>

<a id="environment-variables"></a>
**Q: What's the difference between a shell variable and an environment variable?**
<details><summary>Show answer</summary>
A shell variable only exists in the current shell session; an environment variable (created with
`export`) is passed down into every child process that shell spawns — that's the actual test of
"environment" vs "shell-local."

Full explanation → [Environment Variables](../docs/04-bash/environment-variables.md)
</details>

## Files and the filesystem

<a id="etc-vs-var-vs-usr"></a>
**Q: What's the practical difference between `/etc`, `/var`, and `/usr`?**
<details><summary>Show answer</summary>
`/etc` holds system-wide configuration files, `/var` holds data that changes at runtime (logs,
caches, spool files), and `/usr` holds the bulk of installed program binaries and their static
resources — the FHS separates them so backup/config-management tools can target each differently.

Full explanation → [Core Directories Explained](../docs/05-file-system/core-directories-explained.md)
</details>

<a id="symlink-vs-hardlink"></a>
**Q: What's the difference between a symbolic link and a hard link?**
<details><summary>Show answer</summary>
A hard link is another directory entry pointing at the same inode — the data is genuinely shared,
and deleting the original doesn't remove the data while the hard link exists. A symbolic link is
a separate file that just stores a path to the target, and breaks ("dangling") if that target is
moved or deleted.

Full explanation → [Symbolic vs Hard Links](../docs/05-file-system/symbolic-vs-hard-links.md)
</details>

<a id="inodes"></a>
**Q: What is an inode?**
<details><summary>Show answer</summary>
An inode is a filesystem data structure storing a file's metadata (permissions, owner, size,
timestamps, and pointers to its actual data blocks) — everything except the filename itself, which
lives in the containing directory's entry.

Full explanation → [Inodes Explained](../docs/05-file-system/inodes-explained.md)
</details>

<a id="file-types"></a>
**Q: How many file types does Linux actually distinguish, and how do you check one?**
<details><summary>Show answer</summary>
Linux distinguishes regular files, directories, symbolic links, block/character devices, named
pipes (FIFOs), and sockets — visible as the first character of `ls -l`'s permission string (`-`,
`d`, `l`, `b`, `c`, `p`, `s` respectively).

Full explanation → [File Types in Linux](../docs/06-files/file-types-in-linux.md)
</details>

<a id="grep-basics"></a>
**Q: What does `grep` do, and what's a common beginner mistake with it?**
<details><summary>Show answer</summary>
`grep` searches text (files or piped input) for lines matching a pattern. A common mistake is
forgetting it's regex-aware by default, so unescaped special characters (`.`, `*`, `[`) don't mean
what a beginner expects — literal searches usually need `grep -F` or escaping.

Full explanation → [grep](../docs/06-files/grep.md)
</details>

<a id="find-vs-locate"></a>
**Q: What's the difference between `find` and `locate`?**
<details><summary>Show answer</summary>
`find` searches the live filesystem in real time, so it's always accurate but slower on large
trees; `locate` searches a prebuilt index (updated periodically by `updatedb`), so it's much
faster but can miss very recently created or deleted files.

Full explanation → [Finding Files](../docs/06-files/finding-files.md)
</details>

<a id="rm-safety"></a>
**Q: Why is `rm` considered dangerous, and how do experienced engineers protect against it?**
<details><summary>Show answer</summary>
`rm` deletes immediately with no recycle bin/undo, and `rm -rf` on the wrong path (especially with
a stray space, e.g. `rm -rf / path`) can be catastrophic. Engineers guard against this with `rm -i`
aliases, double-checking `pwd` first, and preferring `trash-cli` for anything not scripted.

Full explanation → [Creating, Copying, Moving, Removing Files](../docs/06-files/creating-copying-moving-removing-files.md)
</details>

## Permissions and users

<a id="owner-group-other"></a>
**Q: What are the three permission classes every Linux file has, and what does each control independently?**
<details><summary>Show answer</summary>
Owner (the user who owns the file), group (a single associated group), and other (everyone else)
— each gets its own independent read/write/execute bits, which is why the same file can be fully
writable by its owner and completely inaccessible to everyone else.

Full explanation → [The Permission Model](../docs/07-permissions/permission-model.md)
</details>

<a id="chmod-755-vs-644"></a>
**Q: What's the practical difference between `chmod 755` and `chmod 644` on a file?**
<details><summary>Show answer</summary>
`755` gives the owner full read/write/execute and group/other read+execute only (typical for
directories and scripts); `644` gives the owner read/write and group/other read-only, with no
execute bit for anyone — the standard mode for regular data files that shouldn't be run.

Full explanation → [chmod (Symbolic and Octal)](../docs/07-permissions/chmod-symbolic-and-octal.md)
</details>

<a id="suid-explained"></a>
**Q: What does the SUID bit do, and why is it a security concern?**
<details><summary>Show answer</summary>
SUID makes an executable run with its owner's privileges rather than the invoking user's — `passwd`
famously runs as root via SUID so any user can update their own password entry. It's a concern
because a SUID binary with a vulnerability effectively hands root access to whoever exploits it.

Full explanation → [SUID, SGID, and the Sticky Bit](../docs/07-permissions/suid-sgid-sticky-bit.md)
</details>

<a id="umask-explained"></a>
**Q: What does `umask` control, and what's its default effect on a new file vs a new directory?**
<details><summary>Show answer</summary>
`umask` is a mask subtracted from the maximum default permissions when a new file or directory is
created. With the common `umask 022`, new files default to `644` (max `666` minus execute) and new
directories default to `755` (max `777`), because files never get execute by default while
directories need it to be enterable.

Full explanation → [umask](../docs/07-permissions/umask.md)
</details>

<a id="passwd-vs-shadow"></a>
**Q: Why does Linux store password hashes in `/etc/shadow` instead of `/etc/passwd`?**
<details><summary>Show answer</summary>
`/etc/passwd` must stay world-readable so every process can resolve usernames/UIDs, but that would
expose password hashes to offline cracking; `/etc/shadow` is readable only by root, so the hashes
are kept out of reach of unprivileged users entirely.

Full explanation → [/etc/passwd and /etc/shadow](../docs/08-users/etc-passwd-and-etc-shadow.md)
</details>

<a id="su-vs-sudo"></a>
**Q: What's the difference between `su` and `sudo`?**
<details><summary>Show answer</summary>
`su` switches you to another user's full login shell (typically root), requiring that user's own
password; `sudo` runs a single command with elevated privileges using *your own* password, and
every invocation is logged — which is why `sudo` is the production-standard approach.

Full explanation → [su vs sudo](../docs/08-users/su-vs-sudo.md)
</details>

<a id="what-is-a-group"></a>
**Q: What is a Linux group, and why do systems use them instead of only per-user permissions?**
<details><summary>Show answer</summary>
A group is a named collection of users that can be granted permissions together — instead of
setting file permissions for every individual user, you grant them once to a group and add/remove
members as team membership changes, without touching the file at all.

Full explanation → [Groups Overview](../docs/09-groups/groups-overview.md)
</details>

## Processes

<a id="ps-aux-vs-ps-ef"></a>
**Q: What's the difference between `ps aux` and `ps -ef`?**
<details><summary>Show answer</summary>
Both list running processes with similar information; they differ only in output format and
option syntax (`aux` is BSD-style, `-ef` is UNIX/POSIX-style) — same underlying data, different
column layout and history, and either is fine to use.

Full explanation → [ps, top, htop](../docs/10-processes/ps-top-htop.md)
</details>

<a id="sigterm-vs-sigkill"></a>
**Q: What's the difference between `SIGTERM` and `SIGKILL`, and which should you try first?**
<details><summary>Show answer</summary>
`SIGTERM` (`kill -15`, the default) politely asks a process to shut down, letting it clean up open
files and connections; `SIGKILL` (`kill -9`) terminates it immediately at the kernel level with no
chance to clean up. Always try `SIGTERM` first and only escalate to `SIGKILL` if the process
ignores it.

Full explanation → [kill, killall, pkill, and Signals](../docs/10-processes/kill-killall-pkill-and-signals.md)
</details>

## Related topics

- [Intermediate Interview Questions](intermediate.md)
- [Senior/Expert Interview Questions](senior.md)
- [Scenario-Based Interview Questions](scenario-based.md)
- [Company-Wise Interview Questions](company-wise/startup.md)
- [Interview Questions Hub](index.md)
