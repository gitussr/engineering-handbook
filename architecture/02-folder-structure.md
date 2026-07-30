# Folder Structure

Content-first, framework-agnostic: plain Markdown + frontmatter. Any static site generator
(Astro, Docusaurus, Next.js/MDX, Eleventy) can be pointed at `/content` without rewriting text.

```
linux-doc/
├── architecture/                  # Phase 1 planning docs (this folder)
│
├── content/                       # ALL documentation content lives here
│   ├── docs/
│   │   ├── 01-linux-basics/
│   │   │   ├── index.md                       # module landing page
│   │   │   ├── what-is-linux.md
│   │   │   ├── linux-distributions.md
│   │   │   ├── kernel-vs-os-vs-distro.md
│   │   │   ├── gui-vs-cli.md
│   │   │   ├── linux-philosophy-and-fhs.md
│   │   │   ├── linux-in-industry.md
│   │   │   └── choosing-a-distro.md
│   │   ├── 02-installing-linux/
│   │   │   └── ...
│   │   ├── 03-terminal/
│   │   ├── 04-bash/
│   │   ├── 05-file-system/
│   │   ├── 06-files/
│   │   ├── 07-permissions/
│   │   ├── 08-users/
│   │   ├── 09-groups/
│   │   ├── 10-processes/
│   │   ├── 11-services/
│   │   ├── 12-networking/
│   │   ├── 13-ssh/
│   │   ├── 14-package-managers/
│   │   ├── 15-storage/
│   │   ├── 16-logs/
│   │   ├── 17-cron/
│   │   ├── 18-shell-scripting/
│   │   ├── 19-security/
│   │   ├── 20-monitoring/
│   │   ├── 21-performance/
│   │   ├── 22-containers/
│   │   ├── 23-docker-basics/
│   │   ├── 24-kubernetes-basics/
│   │   ├── 25-linux-for-cloud/
│   │   ├── 26-linux-for-devops/
│   │   ├── 27-linux-for-cybersecurity/
│   │   ├── 28-linux-for-web-servers/
│   │   ├── 33-troubleshooting/
│   │   └── 34-final-revision/
│   │
│   ├── developer-essentials/       # sibling to /docs, NOT nested inside it — see
│   │   ├── index.md                # architecture/09-developer-essentials.md
│   │   ├── git-basics.md
│   │   ├── github-basics.md
│   │   ├── ssh-keys-for-github.md
│   │   ├── git-workflow.md
│   │   └── git-cheat-sheet.md
│   │
│   ├── commands/                  # ONE canonical page per Linux command
│   │   ├── ps.md
│   │   ├── top.md
│   │   ├── chmod.md
│   │   ├── grep.md
│   │   └── ...                    # every command referenced in /docs links here
│   │
│   ├── careers/                   # Module 29 area — career guide
│   │   ├── index.md                        # career hub / comparison table
│   │   ├── devops-engineer.md
│   │   ├── cloud-engineer.md
│   │   ├── site-reliability-engineer.md
│   │   ├── platform-engineer.md
│   │   ├── backend-engineer.md
│   │   ├── linux-system-administrator.md
│   │   ├── infrastructure-engineer.md
│   │   ├── cybersecurity-analyst.md
│   │   ├── penetration-tester.md
│   │   ├── soc-analyst.md
│   │   ├── cloud-security-engineer.md
│   │   └── ml-ops-ai-infrastructure-engineer.md
│   │
│   ├── cheatsheets/                # Module 30 — Git Cheat Sheet lives in developer-essentials/
│   │   ├── index.md                # and is cross-listed here, not duplicated (see 09-developer-essentials.md)
│   │   ├── linux-commands.md
│   │   ├── networking.md
│   │   ├── permissions.md
│   │   ├── regex.md
│   │   ├── ssh.md
│   │   ├── systemctl.md
│   │   ├── cron.md
│   │   ├── package-managers.md
│   │   └── docker-basics.md
│   │
│   ├── interview-questions/        # Module 29 (question banks)
│   │   ├── index.md
│   │   ├── beginner.md
│   │   ├── intermediate.md
│   │   ├── senior.md
│   │   ├── scenario-based.md
│   │   └── company-wise/           # categorized by company TYPE, not named companies
│   │       ├── index.md            # (disclaimer required on every page — see 08-...)
│   │       ├── startup.md
│   │       ├── product-companies.md
│   │       ├── service-companies.md
│   │       ├── faang-style.md
│   │       ├── cloud-companies.md
│   │       ├── devops-roles.md
│   │       ├── linux-administrator-roles.md
│   │       └── cybersecurity-roles.md
│   │
│   ├── projects/                   # Module 31
│   │   ├── index.md
│   │   ├── beginner/
│   │   ├── intermediate/
│   │   └── advanced/
│   │
│   └── labs/                       # Module 32
│       ├── index.md
│       ├── beginner/
│       ├── intermediate/
│       ├── advanced/
│       └── production/
│
├── data/                           # Structured data consumed by the site build
│   ├── roadmap.json                # generated from 01-roadmap.md — drives sidebar + roadmap page
│   ├── search-index.json           # generated — see 05-search-taxonomy.md
│   ├── careers.json                # generated from 06-career-paths.md
│   └── navigation.json             # generated from 03-navigation-sidebar.md
│
├── static/                         # images, diagrams (SVG), downloadable cheat-sheet PDFs
│   ├── diagrams/
│   └── downloads/
│
└── README.md                       # project-level readme (build instructions, once a Phase 2
                                     # framework is chosen)
```

## Conventions

- **One concept or one command per file.** No mega-pages. A module's `index.md` is the only page
  allowed to summarize multiple topics (it's the module landing page + topic list).
- **Commands live once, are linked everywhere.** `ps`, `top`, `htop`, `kill` used in Module 10
  each get a single canonical page under `/content/commands/`, cross-linked from every module
  that uses them (Processes, Monitoring, Performance, Troubleshooting, etc.) instead of being
  re-documented per module. This avoids duplicate/conflicting command docs.
- **Numeric module prefixes (`01-`, `02-`...) are folder-only**, stripped from the public URL
  (see [04-url-structure.md](04-url-structure.md)) — they exist purely to keep the repo sorted in
  roadmap order for contributors.
- **`data/*.json` files are generated, not hand-authored** — they're derived from the Markdown
  frontmatter (difficulty label, related topics, prerequisites) at build time, so there is a
  single source of truth per page (the Markdown file itself) and no risk of the sidebar/roadmap
  drifting out of sync with content.
