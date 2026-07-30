---
title: "Project: Build a File Organization and Backup Script"
description: "A beginner Linux project: build a script that automatically organizes a messy directory by file type and backs up important files on a schedule using cron."
type: "project"
tier: "beginner"
careerRelevance: ["linux-administrator", "devops"]
updatedAt: "2026-07-30"
keywords: ["bash backup script project", "file organization script linux", "cron backup project beginner", "linux automation project"]
canonicalUrl: "/projects/beginner/file-organization-and-backup-script"
---

# Project: Build a File Organization and Backup Script

🟢 Beginner · Relevant for: Linux Administrator, DevOps

## Goal

Build a script that automatically sorts files in a messy directory (like `~/Downloads`) into
subfolders by type, and a second script that backs up a chosen directory on a schedule using cron.

## Requirements

- An organizer script that moves files into subfolders (`images/`, `documents/`, `archives/`,
  etc.) based on file extension.
- The organizer must not overwrite an existing file with the same name — handle the collision
  instead of silently destroying data.
- A backup script that copies a target directory into a timestamped archive.
- A cron job that runs the backup script on a schedule (e.g. nightly).
- Basic logging: both scripts should write what they did to a log file, not just to the terminal.

## Suggested Approach

1. Write the organizer first as a plain script, testing against a throwaway directory full of
   sample files — never test file-moving logic against real data until it's proven safe.
2. Handle the "file already exists at destination" case explicitly (append a number/timestamp to
   the filename) before you consider the organizer done.
3. Write the backup script separately — use `tar` to create a timestamped compressed archive of
   the target directory.
4. Add logging to both scripts: redirect output to a log file with a timestamp on each run.
5. Only once both scripts work correctly when run manually, wire the backup script into cron —
   debugging cron-specific environment issues is much easier once you know the script itself works.

## Stretch Goals

- Add a `--dry-run` flag to the organizer that prints what it *would* move without moving anything.
- Rotate old backups automatically, keeping only the last N.
- Email or otherwise notify yourself if the backup script fails (non-zero exit code).
- Add a config file so file-type-to-folder mappings aren't hardcoded in the script.

## Related Modules

- [Files](../../docs/06-files/index.md) — moving files, compression and archiving
- [Shell Scripting](../../docs/18-shell-scripting/index.md) — conditionals, loops, exit codes
- [Cron](../../docs/17-cron/index.md) — scheduling the backup job

## Related topics

- [Cron Cheat Sheet](../../cheatsheets/cron.md)
- [Dotfiles Repo Project](dotfiles-repo-with-aliases-and-custom-prompt.md)
- [Projects Hub](../index.md)
