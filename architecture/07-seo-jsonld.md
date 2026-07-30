# SEO Metadata, JSON-LD Schema & Internal Linking Strategy

## Required frontmatter fields (every content page)

```yaml
---
title: "chmod — Change File Permissions"
description: "How to use chmod to change Linux file permissions with symbolic and octal notation, including real production examples and common mistakes."
module: "07-permissions"
moduleTitle: "Permissions"
stage: "beginner"
difficulty: "must-know"        # must-know | good-to-know | expert
type: "command"                 # command | concept | career | interview | lab | cheatsheet | project
prerequisites: ["06-files/finding-files", "07-permissions/permission-model"]
relatedTopics: ["chown", "umask", "setfacl"]
relatedCommands: ["chown", "chgrp", "setfacl"]
careerRelevance: ["linux-administrator", "devops", "cybersecurity"]   # from the 10-tag taxonomy, see 06-career-paths.md
relatedLabs: ["intermediate/fix-a-permission-denied-deploy-failure"]
relatedInterviewQuestions: ["intermediate#chmod-vs-chown"]
relatedCheatsheet: "permissions"
nextTopic: "07-permissions/chown-chgrp"
prevTopic: "07-permissions/permission-model"
estimatedReadingTime: 8
updatedAt: "2026-07-25"
keywords: ["chmod", "permissions", "rwx", "octal", "755", "644", "linux file permissions"]
canonicalUrl: "/docs/permissions/chmod-symbolic-and-octal"
---
```

These fields drive: the sidebar, the search index (05), breadcrumbs (03), the page header
(Title/Description/Prerequisites/Reading Time/Difficulty per the brief's "every page should
have" list), the knowledge-graph auto-linking required by the approval (Prerequisites, Related
Concepts, Related Commands, Career Relevance, Practice Labs, Interview Questions, Cheat Sheets,
Next/Previous Topic — every one of those is a frontmatter field here, not a manually-written
link), and the SEO tags below.

## Page `<head>` output (generated from frontmatter, not hand-written per page)

```html
<title>chmod — Change File Permissions | Linux Documentation</title>
<meta name="description" content="{{description}}">
<link rel="canonical" href="https://{domain}{canonicalUrl}">
<meta property="og:title" content="{{title}}">
<meta property="og:description" content="{{description}}">
<meta property="og:type" content="article">
<meta property="og:url" content="https://{domain}{canonicalUrl}">
<meta name="twitter:card" content="summary_large_image">
```

## JSON-LD schema types by content type

| Content type | Schema.org type | Notes |
|---|---|---|
| Docs topic / command page | `TechArticle` | `proficiencyLevel` set from `difficulty` (Beginner/Intermediate/Expert) |
| Module landing page | `CollectionPage` containing `ItemList` of topic pages | Powers rich "course module" style result |
| Career page | `Occupation` (fallback `Article` if fields don't map cleanly) | `educationRequirements` links to mapped modules |
| Interview question page | `FAQPage` with `Question`/`Answer` pairs | One `FAQPage` block per question bank page |
| Cheat sheet | `TechArticle` | Marked `about` the parent module |
| Lab / Project | `LearningResource` (`Article` fallback) | `educationalLevel` from tier |
| Roadmap page | `ItemList` of the 34 modules in order | Enables "course outline" rich result |
| Whole site | `WebSite` + `SearchAction` on the homepage only | Enables sitelinks search box in results |

Example — `TechArticle` for a command page:

```json
{
  "@context": "https://schema.org",
  "@type": "TechArticle",
  "headline": "chmod — Change File Permissions",
  "description": "...",
  "proficiencyLevel": "Beginner",
  "dateModified": "2026-07-25",
  "about": { "@type": "Thing", "name": "Linux File Permissions" },
  "isPartOf": { "@type": "CollectionPage", "name": "Permissions", "url": "/docs/permissions" }
}
```

## Internal linking strategy

1. **Prerequisites → forward links.** Every topic's `prerequisites` frontmatter renders as
   in-page links at the top ("Before this: ..."); this is also how the build validates the
   roadmap has no forward-references (a topic can't require something taught later).
2. **Concept → command linking.** Concept pages (e.g. "Processes") link out to every command page
   they introduce (`ps`, `top`, `htop`, `kill`); command pages link back to the concept page(s)
   that use them ("Used in: Processes, Monitoring, Troubleshooting").
3. **Prev/Next chain.** Every topic page has exactly one `prevTopic`/`nextTopic`, forming a single
   linear path through the whole 218-page roadmap — this is the "never need to search Google
   again" backbone: a learner can hit Next through the entire site start to finish.
4. **Cross-module "Related Topics."** Independent of Prev/Next, `relatedTopics` links sideways
   (e.g. `chmod` → `setfacl` even though ACLs are a different, later sub-topic) — these are
   curated per-page, not auto-generated, to keep them genuinely useful.
5. **Career pages link into docs, not the reverse.** Docs pages stay career-agnostic; career pages
   are the layer that says "this module matters for you." Avoids cluttering command pages with
   12 different "relevant if you're a ___" callouts.
6. **Cheat sheets/labs/interview questions link back to source topics.** A cheat sheet entry for
   `chmod` links to `/docs/permissions/chmod-symbolic-and-octal`; a lab that uses `chmod` links
   the same way. Never the reverse (topic pages don't enumerate every lab that touches them, to
   avoid link rot as labs are added).

## Sitemap & robots

- `sitemap.xml` generated from `data/roadmap.json` + `data/careers.json` + the cheatsheet/lab/
  interview/project indexes — every canonical URL from [04-url-structure.md](04-url-structure.md)
  included once.
- `robots.txt` allows all crawl by default; `/search?q=` results pages are excluded via
  `noindex` meta (dynamic query pages, not canonical content) — the static topic/career/cheat
  sheet pages are what should rank, not search result pages.
