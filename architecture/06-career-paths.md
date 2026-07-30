# Career Paths — Structure

12 roles, each a page at `/careers/{role-slug}`. Every page uses the **same fixed template** so
roles are comparable side by side (per [03-navigation-sidebar.md](03-navigation-sidebar.md)).

## Fixed page template (identical fields, every role)

1. **Role summary** — one paragraph, what this role actually does
2. **Daily responsibilities** — bulleted, concrete (not generic "manages infrastructure")
3. **Linux knowledge required** — mapped explicitly to roadmap modules, each tagged with the
   difficulty label that role needs for it (a role may only need 🟢 in a module another role
   needs 🔴 in)
4. **Tools used** — real tool names, grouped by category
5. **Salary ranges** — rendered from a separate, independently-updatable data record (see below),
   never hand-written into the page body
6. **Learning order** — which of the mapped modules to study first, in what sequence, deep-linked
   into `/roadmap`
7. **Recommended certifications** — real, current certs only
8. **Next step** — link to the first Practice Project / Lab suited to this role

## Salary section design (approved — sourced, independently updatable)

Salary figures are never fabricated and never hand-written directly into a career page's
Markdown body. They live in one small data record per role, `data/salaries/{role-slug}.json`,
that the career page renders via an include/component — so refreshing salary data is a data-file
edit, not a career-page rewrite:

```json
{
  "role": "devops-engineer",
  "lastUpdated": "2026-07-25",
  "disclaimer": "Salary varies by location, experience, company size, and market conditions. Figures below are directional, not guarantees.",
  "ranges": [
    {
      "region": "United States",
      "currency": "USD",
      "period": "annual",
      "junior": null,
      "mid": null,
      "senior": null,
      "source": "{cite the actual source used}",
      "sourceUrl": "{link}",
      "retrievedOn": "{date data was pulled}"
    },
    {
      "region": "India",
      "currency": "INR",
      "period": "annual",
      "junior": null,
      "mid": null,
      "senior": null,
      "source": "{cite}",
      "sourceUrl": "{link}",
      "retrievedOn": "{date}"
    }
  ]
}
```

Rules for Phase 2 content generation:
- `junior`/`mid`/`senior` numeric fields are populated **only** from a real, named, current public
  source (e.g. levels.fyi, Glassdoor aggregate, BLS, PayScale, a national statistics body) —
  never estimated or invented. If no credible source exists for a region, that region is omitted
  rather than guessed.
- Every populated region carries `source`, `sourceUrl`, and `retrievedOn`.
- `lastUpdated` on the record (not the career page's own `updatedAt`) is what the UI displays
  next to the salary block, so a stale salary block is visibly stale even if the rest of the
  career page was recently edited.
- The rendered salary block always shows the `disclaimer` line — variation by location,
  experience, company, and market conditions — directly beside the figures, not in a footnote.
- Multiple regions per role are expected (at minimum: United States and India, given the brief's
  "country-specific examples"); add more regions opportunistically where a credible source exists.

## The 12 roles and their module mapping

| Role | Slug | Primary modules (🟢-level for this role) | Also draws on |
|---|---|---|---|
| DevOps Engineer | `devops-engineer` | 10 Processes, 11 Services, 13 SSH, 17 Cron, 18 Shell Scripting, 23 Docker, 26 Linux for DevOps | 12 Networking, 24 Kubernetes, 20 Monitoring |
| Cloud Engineer | `cloud-engineer` | 12 Networking, 13 SSH, 15 Storage, 25 Linux for Cloud | 14 Package Managers, 19 Security, 23 Docker |
| Site Reliability Engineer | `site-reliability-engineer` | 16 Logs, 20 Monitoring, 21 Performance, 33 Troubleshooting | 11 Services, 18 Shell Scripting, 24 Kubernetes |
| Platform Engineer | `platform-engineer` | 22 Containers, 23 Docker, 24 Kubernetes, 26 Linux for DevOps | 12 Networking, 15 Storage, 19 Security |
| Backend Engineer | `backend-engineer` | 06 Files, 10 Processes, 12 Networking, 28 Linux for Web Servers | 07 Permissions, 13 SSH, 14 Package Managers |
| Linux System Administrator | `linux-system-administrator` | 07 Permissions, 08 Users, 09 Groups, 14 Package Managers, 15 Storage, 16 Logs, 17 Cron | 11 Services, 19 Security, 33 Troubleshooting |
| Infrastructure Engineer | `infrastructure-engineer` | 12 Networking, 15 Storage, 21 Performance, 25 Linux for Cloud | 11 Services, 20 Monitoring, 26 Linux for DevOps |
| Cybersecurity Analyst | `cybersecurity-analyst` | 16 Logs, 19 Security, 27 Linux for Cybersecurity | 07 Permissions, 08 Users, 12 Networking |
| Penetration Tester | `penetration-tester` | 12 Networking, 13 SSH, 27 Linux for Cybersecurity | 07 Permissions, 18 Shell Scripting, 19 Security |
| SOC Analyst | `soc-analyst` | 16 Logs, 19 Security, 27 Linux for Cybersecurity | 10 Processes, 12 Networking, 33 Troubleshooting |
| Cloud Security Engineer | `cloud-security-engineer` | 19 Security, 25 Linux for Cloud, 27 Linux for Cybersecurity | 12 Networking, 13 SSH, 15 Storage |
| ML Ops / AI Infrastructure Engineer | `ml-ops-ai-infrastructure-engineer` | 21 Performance, 22 Containers, 23 Docker, 24 Kubernetes, 25 Linux for Cloud | 15 Storage, 18 Shell Scripting, 20 Monitoring |

Every role page's "Linux knowledge required" section pulls its bullet list directly from this
table's "Primary" and "Also draws on" columns, each linking to the matching module page — this
mapping is the single source of truth so a module's difficulty label can differ by role context
(the module page itself keeps one global label from [01-roadmap.md](01-roadmap.md); the career
page reframes relevance, it does not override the label).

## Career hub page (`/careers`)

Grid of all 12 role cards. Each card: role name, one-line summary, 3–4 top tools as icons/badges,
"View path" CTA. A filter/compare toggle lets a learner select 2–3 roles to see overlapping vs.
unique modules side by side (drives the "which role should I pick" decision the brief's career
guide is meant to answer).

## Career relevance tags (per-page, distinct from the 12 career pages above)

Every content page (topic, command, cheat sheet, lab, interview question) additionally carries a
`careerRelevance` frontmatter array — a lighter-weight tagging taxonomy of **10 tracks**, used to
answer "why does this page matter to someone in my track" inline, without sending the reader to a
full career page. This is separate from, and coarser than, the 12 role pages:

| Track tag | Relates to career page(s) |
|---|---|
| DevOps | `devops-engineer` |
| Cloud | `cloud-engineer`, `cloud-security-engineer` |
| Cybersecurity | `cybersecurity-analyst`, `penetration-tester`, `soc-analyst`, `cloud-security-engineer` |
| Backend | `backend-engineer` |
| SRE | `site-reliability-engineer` |
| Platform | `platform-engineer` |
| Linux Administrator | `linux-system-administrator` |
| Software Engineering | no dedicated career page — general-purpose tag for topics relevant to any software engineer regardless of specialization |
| AI/ML Infrastructure | `ml-ops-ai-infrastructure-engineer` |
| WordPress/Web Hosting | no dedicated career page — maps to Module 28 (Linux for Web Servers); tag exists because the brief explicitly calls out WordPress/PHP/web developers as a target audience even though they don't get a full 12-role career page |

Rendering: a small badge row under the page header — e.g. a `chmod` page shows
`Relevant for: Linux Administrator · DevOps · Cybersecurity` — each badge linking to that track's
filtered view (`/careers?track=devops`) rather than a single career page, since one page is
usually relevant to several tracks at once. This is what satisfies the Phase 2 requirement that
"every page should indicate relevance to careers" without duplicating the full career-page content
inline.
