# Navigation, Sidebar & Landing Page Structure

## Top navigation bar

```
[Logo]  Docs   Developer Essentials   Careers   Cheat Sheets   Labs   Interview Prep   [Search]   [Dark/Light Toggle]
```

- `Docs` → roadmap/module hub (default landing for "start learning")
- `Developer Essentials` → Git/GitHub section, sibling to Docs, not nested inside it — see
  [09-developer-essentials.md](09-developer-essentials.md)
- `Careers` → career hub (12 role pages)
- `Cheat Sheets` → 10-sheet index
- `Labs` → 4-tier lab index
- `Interview Prep` → question bank hub
- Search bar is global, always visible (not just on a search page) — see
  [05-search-taxonomy.md](05-search-taxonomy.md)

## Landing page (`/`) section order

1. Hero — one-line value prop + primary CTA ("Start Learning" → Module 01) + secondary CTA
   ("View Roadmap")
2. Search bar (large, prominent — searches entire documentation)
3. Learning Roadmap — visual 4-stage progression (Beginner → Intermediate → Professional →
   Production Engineer), links into `/roadmap`
4. Career Paths — 12 role cards, links into `/careers`
5. Documentation Categories — the 34 modules grouped by stage, as cards
6. Recently Updated — last N changed pages (build-time generated from frontmatter `updatedAt`)
7. Popular Topics — curated/analytics-driven list (manually seeded pre-launch, e.g. Permissions,
   SSH, Processes, Cron, Docker Basics)
8. Linux Cheat Sheets — 10-sheet grid
9. Command Reference — search/browse into `/commands`
10. Download Resources — PDF cheat sheets, offline roadmap
11. Practice Labs — 4-tier preview
12. Interview Questions — question bank preview
13. Troubleshooting Guide — links into Module 33

## Docs sidebar (left nav, persistent on every `/docs/*` page)

Grouped by stage, collapsible per module, difficulty dot shown next to every topic link:

```
BEGINNER
 ▸ 01 Linux Basics
 ▸ 02 Installing Linux
 ▸ 03 Terminal
 ▸ 04 Bash
 ▸ 05 File System
 ▸ 06 Files
 ▸ 07 Permissions
 ▸ 08 Users
 ▸ 09 Groups

INTERMEDIATE
 ▸ 10 Processes
 ▸ 11 Services
 ▸ 12 Networking
 ▸ 13 SSH
 ▸ 14 Package Managers
 ▸ 15 Storage
 ▸ 16 Logs
 ▸ 17 Cron
 ▸ 18 Shell Scripting

PROFESSIONAL
 ▸ 19 Security
 ▸ 20 Monitoring
 ▸ 21 Performance
 ▸ 22 Containers
 ▸ 23 Docker Basics
 ▸ 24 Kubernetes Basics

PRODUCTION ENGINEER
 ▸ 25 Linux for Cloud
 ▸ 26 Linux for DevOps
 ▸ 27 Linux for Cybersecurity
 ▸ 28 Linux for Web Servers
 ▸ 33 Troubleshooting
 ▸ 34 Final Revision

DEVELOPER ESSENTIALS (standalone group, not a 5th stage)
 ▸ Git Basics
 ▸ GitHub Basics
 ▸ SSH Keys for GitHub
 ▸ Git Workflow
 ▸ Git Cheat Sheet

REFERENCE (always visible, outside the 4 stages)
 ▸ Interview Questions (29)
 ▸ Cheat Sheets (30)
 ▸ Practice Projects (31)
 ▸ Production Labs (32)
 ▸ Command Reference (A–Z)
```

A module (`▸`) expands to its topic list on click; the active topic is highlighted; a persistent
"Prev / Next" pair at the bottom of the sidebar mirrors the in-page Prev/Next footer so keyboard
users can move through the roadmap without opening the sidebar tree each time.

## Breadcrumb structure

Every page shows a breadcrumb reflecting the folder hierarchy, independent of the flattened
public URL (see [04-url-structure.md](04-url-structure.md) for why URLs are flatter than the
breadcrumb):

```
Home / Docs / 07 Permissions / chmod
Home / Careers / DevOps Engineer
Home / Cheat Sheets / SSH
Home / Labs / Intermediate / Debug a Failing Nginx Deployment
Home / Interview Questions / Senior / Scenario-Based
Home / Commands / chmod
```

Rule: breadcrumb always shows **stage-free module context** for docs pages (module name, not
"Beginner/Intermediate/...") since stage is roadmap-only grouping, not a URL/content grouping.

## Career page internal nav

Each career page (`/careers/{role}`) gets a fixed on-page table of contents, identical structure
across all 12 roles (see [06-career-paths.md](06-career-paths.md) for the field list) — this
consistency is what lets users compare roles side by side.
