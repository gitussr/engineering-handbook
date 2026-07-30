---
title: "Shell Scripting"
description: "Module 18 of the Linux roadmap — writing scripts, variables, conditionals, loops, functions, arguments, exit codes and error handling, debugging, and real production automation."
module: "18-shell-scripting"
moduleTitle: "Shell Scripting"
stage: "intermediate"
type: "module-index"
nextTopic: "18-shell-scripting/writing-your-first-script"
updatedAt: "2026-07-28"
canonicalUrl: "/docs/shell-scripting"
---

# Shell Scripting

Module 18 of 34 · Stage: Intermediate · Previous: [17 Cron](../17-cron/index.md)

Every prior intermediate module taught commands run one at a time, often scheduled via
[Module 17's cron](../17-cron/index.md). This module teaches the language those commands are
assembled into: the shebang and execution mechanics, variables, branching, loops, functions,
argument handling, exit-code discipline, and debugging — closing with three realistic scripts
(backup, deploy, health-check) that combine everything into production-shaped automation.

## Topics in this module

| Topic | Difficulty |
|---|---|
| [Writing Your First Script](writing-your-first-script.md) | 🟢 Must Know |
| [Variables and Data Types](variables-and-data-types.md) | 🟢 Must Know |
| [Conditionals: if/else/case](conditionals-if-else-case.md) | 🟢 Must Know |
| [Loops: for/while/until](loops-for-while-until.md) | 🟢 Must Know |
| [Functions](functions.md) | 🟢 Must Know |
| [Script Arguments: $1, $@, $#](script-arguments.md) | 🟢 Must Know |
| [Exit Codes and Error Handling](exit-codes-and-error-handling.md) | 🟡 Good to Know |
| [Debugging Scripts: set -x, shellcheck](debugging-scripts-set-x-shellcheck.md) | 🟡 Good to Know |
| [Real Automation Scripts: Backup, Deploy, Health-Check](real-automation-scripts.md) | 🔴 Expert |

## What you should be able to do after this module

- Write, make executable, and run a Bash script, understanding the shebang line's role.
- Declare and safely quote variables, and explain why unquoted expansions cause bugs.
- Branch logic with `if`/`elif`/`else` and `case`, using `[[ ]]` test operators correctly.
- Write bounded `for`, `while`, and `until` loops, including retry patterns.
- Write functions with parameters, `local` scoping, and correct use of `return` vs. captured
  output.
- Read and validate script arguments (`$1`, `$#`, `"$@"`) before using them.
- Apply exit-code discipline (`$?`, `exit N`, `set -e`, `trap`) so scripts fail safely and
  visibly.
- Debug a misbehaving script with `set -x` and catch common mistakes ahead of time with
  `shellcheck`.
- Assemble all of the above into realistic backup, deploy, and health-check scripts.

## Known, intentional gaps in this module

- Canonical command pages exist for [`set`](../../commands/set.md) (covering `-e`, `-u`, `-x`,
  `-o pipefail`) and [`shellcheck`](../../commands/shellcheck.md) — the two tools explicitly
  named in this module's roadmap bullets.
- [Aliases and Functions](../04-bash/aliases-and-functions.md) in Module 04 already introduced
  the alias-vs-function distinction at a basic interactive-shell level; this module's
  [Functions](functions.md) page is the canonical owner of full function syntax (parameters,
  `return`, `local`), consistent with the forward reference Module 04 already made.
- This module intentionally does not introduce advanced text-processing tools (`sed`, `awk`,
  `cut`, `sort`) in depth — they're referenced illustratively where relevant but remain deferred,
  as noted in the [commands hub](../../commands/index.md).

**Previous module:** [17 Cron](../17-cron/index.md)
**Next module:** [19 Security →](../19-security/index.md)
