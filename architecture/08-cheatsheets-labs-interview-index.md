# Cheat Sheets, Practice Labs, Projects & Interview Question Bank — Inventory

**Decision (approved):** Git does not become a numbered Linux module and is not folded into
Shell Scripting. It lives in a standalone **Developer Essentials** section, structurally parallel
to — but outside — the 34-module core roadmap. See
[09-developer-essentials.md](09-developer-essentials.md) for its full structure. This keeps the
Linux learning path "pure" (every core module is a Linux OS topic) while still shipping
everything a modern developer needs.

Site-level structure is now:

```
Linux Roadmap (34 core modules)   ← Modules 01–34, unchanged
Developer Essentials              ← new, sits alongside the roadmap, not inside it
```

## Cheat sheets (10 total, per the brief) — `/cheatsheets/{slug}`

9 pull from core modules; the 10th (Git) now belongs to Developer Essentials rather than the core
module list, but is still counted in — and linked from — the cheat sheet hub so the "10 cheat
sheets" promise on the landing page still holds.

| Sheet | Slug | Source |
|---|---|---|
| Linux Commands | `linux-commands` | 03 Terminal, 06 Files, all command pages |
| Networking | `networking` | 12 Networking |
| Permissions | `permissions` | 07 Permissions |
| Regex | `regex` | 06 Files (regex basics) |
| SSH | `ssh` | 13 SSH |
| systemctl | `systemctl` | 11 Services |
| Cron | `cron` | 17 Cron |
| Package Managers | `package-managers` | 14 Package Managers |
| Docker Basics | `docker-basics` | 23 Docker Basics |
| Git Cheat Sheet | `git` (served at `/developer-essentials/git-cheat-sheet`, cross-listed at `/cheatsheets`) | Developer Essentials |

Each sheet: single scrollable page, copy-button on every command block, downloadable as PDF
(brief's "Download Resources" section), grouped by sub-task within the sheet (e.g. the SSH sheet
groups Connecting / Key Management / Tunneling / Config).

## Practice Labs (4 tiers, per the brief) — `/labs/{tier}/{slug}`

Each lab "simulates real company work" (brief's requirement) — framed as a ticket/incident, not
a tutorial. Fixed lab page template: Scenario → Environment Setup → Tasks → Hints (collapsed) →
Solution (collapsed) → What You Learned → Related Modules.

| Tier | Example labs (illustrative — full list authored in Phase 2) |
|---|---|
| Beginner | Set up SSH key access to a new server; Create a user with sudo access; Fix a "permission denied" script error |
| Intermediate | Write a cron-based backup script with log rotation; Deploy and reverse-proxy a Node app with Nginx; Diagnose high disk usage and free space safely |
| Advanced | Debug a memory leak using `top`/`vmstat`/`journalctl`; Set up centralized logging for 3 servers; Harden a public-facing server against a CIS benchmark checklist |
| Production | On-call simulation: service down at 2am, diagnose via `journalctl` + `systemctl` + `ss`; SSH into an AWS EC2 instance and recover from a full disk; Roll back a bad Docker deployment |

## Practice Projects (3 tiers, per the brief's "Beginner/Intermediate/Advanced") —
`/projects/{tier}/{slug}`

Longer-form than labs (multi-session builds vs. single-incident labs). Fixed template: Goal →
Requirements → Suggested Approach → Stretch Goals → Related Modules.

| Tier | Example projects (illustrative) |
|---|---|
| Beginner | Build a personal dotfiles repo with aliases and a custom prompt |
| Intermediate | Build a Bash-based server health-check + alerting script |
| Advanced | Containerize and deploy a full app stack (Nginx + app + DB) with Docker Compose, then port it to a single-node Kubernetes cluster |

## Interview Question Bank (Module 29) — "Top 500 Linux Interview Questions" per the brief

Delivered as **grouped question-bank pages**, not 500 individual URLs (matches
[04-url-structure.md](04-url-structure.md) and keeps the FAQPage JSON-LD from
[07-seo-jsonld.md](07-seo-jsonld.md) meaningful per page):

| Page | Slug | Approx. question count |
|---|---|---|
| Beginner | `/interview-questions/beginner` | 150 |
| Intermediate | `/interview-questions/intermediate` | 150 |
| Senior/Expert | `/interview-questions/senior` | 100 |
| Scenario-based | `/interview-questions/scenario-based` | 75 |

Each question links back to the source topic page where the answer is taught in full (per the
"cheat sheets/labs link back to source topics, never the reverse" rule in
[07-seo-jsonld.md](07-seo-jsonld.md)) — the interview page gives question + concise answer +
"Full explanation →" link, it doesn't duplicate the full topic content. **Approved decision:**
questions are weighted toward scenario-based/practical over memorization-style Q&A across all
four tiers, not just the dedicated scenario-based page.

### Company-wise sets (approved, with mandatory disclaimer)

Categorized by **company type**, not by individual named companies — `/interview-questions/company-wise/{category-slug}`:

| Category | Slug |
|---|---|
| Startup | `startup` |
| Product Companies | `product-companies` |
| Service Companies | `service-companies` |
| FAANG-style | `faang-style` |
| Cloud Companies | `cloud-companies` |
| DevOps Roles | `devops-roles` |
| Linux Administrator Roles | `linux-administrator-roles` |
| Cybersecurity Roles | `cybersecurity-roles` |

Every page in this group carries this disclaimer, verbatim, directly under the H1 (not buried in
a footer):

> These interview questions are compiled from publicly shared interview experiences, official
> documentation, community discussions, and common industry practices. They are representative
> examples and not confidential or leaked interview material.

`data/search-index.json` records (see [05-search-taxonomy.md](05-search-taxonomy.md)) for these
pages get `type: "interview"` and a `companyCategory` facet so search/filtering can narrow by
category independent of difficulty tier.
