# Terminology

Canonical word choices, locked in during the Module 01 audit (finding #6 — inconsistent
terminology). When a term isn't listed here and a genuine ambiguity comes up while writing a new
module, add it here rather than deciding silently page by page.

## Core terms

| Term | Rule |
|---|---|
| **distribution / distro** | Use "distribution" on first mention per page, "distro" for every subsequent mention on that page. Both are acceptable; the rule is about consistency within a page, not banning one form. |
| **GNU/Linux** | Use only when specifically distinguishing the OS layer (kernel + GNU utilities) from the kernel alone. Its full explanation lives on [Kernel vs OS vs Distro](content/docs/01-linux-basics/kernel-vs-os-vs-distro.md) — don't introduce it unexplained on other pages; link there instead. |
| **kernel** | Always lowercase unless starting a sentence. Never "Kernel" as a proper noun. |
| **Linux** | Capitalized always. Refers to the kernel specifically when precision matters (see [What is Linux](content/docs/01-linux-basics/what-is-linux.md)); refers to "a Linux system" loosely otherwise — pages should be clear from context which sense is meant. |
| **headless** | A server with no monitor/display stack present or expected. Define inline on first use per page (one clause is enough: "headless — no monitor or display stack"), don't assume it's known. |
| **LTS (Long Term Support)** | Define inline on first use per page: "a release with an extended, predictable support window." Don't assume prior knowledge. |
| **shell** | "The program that reads and runs your commands." First full teaching in Module 04 — pages before that module define it inline in one clause if the word is used at all, rather than assuming it. |
| **CLI** | Command Line Interface. Spelled out on first use per page, "CLI" after. |
| **GUI** | Graphical User Interface. Spelled out on first use per page, "GUI" after. |
| **FHS** | Filesystem Hierarchy Standard. Spelled out on first use per page, "FHS" after. |

## Difficulty labels

Always exactly one of: **Must Know** (🟢), **Good to Know** (🟡), **Expert** (🔴) — capitalized
this way, every time. Frontmatter values are lowercase-hyphenated: `must-know`, `good-to-know`,
`expert`. Never invent a fourth tier.

## Career track names

Exactly these 10, exactly this casing, per
[architecture/06-career-paths.md](architecture/06-career-paths.md):
DevOps, Cloud, Cybersecurity, Backend, SRE, Platform, Linux Administrator, Software Engineering,
AI/ML Infrastructure, WordPress/Web Hosting.

Frontmatter slugs (lowercase-hyphenated): `devops`, `cloud`, `cybersecurity`, `backend`, `sre`,
`platform`, `linux-administrator`, `software-engineering`, `ai-ml-infrastructure`,
`wordpress-web-hosting`.

## Component names (for headings)

Use these exact heading strings — they're referenced by name in WRITING_RULES.md,
STYLE_GUIDE.md, and the templates, and inconsistent headings break any tooling that parses pages
by section name:

`TL;DR`, `What is it?`, `Why does it exist?`, `Where is it used?`, `How it works`,
`Real-world example`, `Syntax`, `Commands`, `Production example`, `Do / Don't`,
`Common mistakes`, `Best practices`, `Exercises`, `Quiz`, `Interview questions`,
`Key Takeaways` (not "Summary" — renamed during the Module 01 audit, see
[architecture/11-module-01-audit.md](architecture/11-module-01-audit.md) finding #5),
`Further Reading`, `Related topics`.

Command pages: `Purpose`, `Syntax`, `Arguments`, `Options`, `Examples`, `Expected Output` (not
"Example Output"), `Exit Status` (not "Exit Codes"), `Common Errors`, `Security Considerations`,
`Performance Considerations`, `Compatibility Notes`, `Production Usage`, `Related Commands`,
`Related Concepts`.

## Naming and slugs

- File/folder slugs: lowercase, hyphen-separated, no numbers except module folder prefixes
  (`07-permissions/`), which are stripped from public URLs — see
  [architecture/04-url-structure.md](architecture/04-url-structure.md).
- Module names in prose: "Module 07: Permissions" on first reference in a page, "Module 07" or
  "Permissions" after.
- Command names: always in inline code formatting (`` `chmod` ``), never plain text, everywhere
  including headings.
