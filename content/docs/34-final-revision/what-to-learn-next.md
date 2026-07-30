---
title: "What to Learn Next"
description: "Where to go after finishing this roadmap — Ansible, Terraform, and a Kubernetes deep dive as the natural next layer, plus Developer Essentials and career-specific next steps."
module: "34-final-revision"
moduleTitle: "Final Revision"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["34-final-revision/readiness-self-assessment-mock-interview"]
relatedTopics: ["26-linux-for-devops/infrastructure-as-code-basics-terraform-on-linux", "24-kubernetes-basics/which-linux-skills-k8s-depends-on"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "platform", "sre", "cybersecurity", "backend"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#terraform-vs-ansible"]
relatedCheatsheet: ""
furtherReading: [{"label": "Kubernetes official documentation", "url": "https://kubernetes.io/docs/home/"}, {"label": "Terraform official documentation", "url": "https://developer.hashicorp.com/terraform/docs"}, {"label": "Ansible official documentation", "url": "https://docs.ansible.com/"}]
nextTopic: ""
prevTopic: "34-final-revision/readiness-self-assessment-mock-interview"
estimatedReadingTime: 7
updatedAt: "2026-07-30"
keywords: ["what to learn after linux", "ansible vs terraform next steps", "kubernetes deep dive next", "linux career next steps"]
canonicalUrl: "/docs/final-revision/what-to-learn-next"
---

# What to Learn Next

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform · SRE · Cybersecurity · Backend

> **TL;DR:** This roadmap deliberately stops at Linux fundamentals plus an introduction to
> containers, Kubernetes, and cloud/DevOps concepts — Ansible, Terraform, and a real Kubernetes
> deep dive are the natural next layer, chosen based on which career path you're actually headed toward.

## What is it?

A map of what to study after this roadmap, organized by the fact that "what's next" genuinely
differs by career path — there's no single correct next step for everyone who finishes 34 modules
of Linux fundamentals.

## Why does it exist?

This roadmap intentionally teaches Modules 22-24 (Containers, Docker, Kubernetes) and Modules
25-28 (the career branches) at an introductory depth — enough to understand the concepts and be
functional, not enough to be an expert in any one of them. Finishing the roadmap is a real
milestone, but it's a foundation, not a ceiling, and this page exists to point at the specific next
layer rather than leaving "now what?" unanswered.

## Where is it used?

Right after finishing this roadmap, or anytime the [Full Roadmap Recap](full-roadmap-recap.md)
reveals you're solid on fundamentals and ready for the next layer of depth in a specific direction.

## How it works

**If you're headed toward DevOps or Platform Engineering:**
Go deep on [Terraform](https://developer.hashicorp.com/terraform/docs) (infrastructure as code —
this roadmap's [Module 26](../26-linux-for-devops/infrastructure-as-code-basics-terraform-on-linux.md)
covers it at an introductory level only) and [Ansible](https://docs.ansible.com/) (configuration
management). Terraform provisions infrastructure; Ansible configures what's running on it — most
real DevOps roles use both together.

**If you're headed toward Cloud or Platform Engineering specifically:**
A genuine [Kubernetes](https://kubernetes.io/docs/home/) deep dive — this roadmap's
[Module 24](../24-kubernetes-basics/index.md) covers Pods/Deployments/Services/kubectl at an
introductory level; real depth means Helm charts, custom resources, operators, and running (not
just using) a cluster, ideally on a managed service (EKS/GKE/AKS) to also pick up the cloud-provider
layer this roadmap's [Module 25](../25-linux-for-cloud/index.md) only introduces.

**If you're headed toward Cybersecurity, SOC, or Pentesting:**
This roadmap's [Module 27](../27-linux-for-cybersecurity/index.md) is intentionally an
introduction. Real depth means a dedicated penetration testing curriculum (which this
documentation explicitly does not teach beyond defensive-framing basics), deeper SIEM tooling
experience, and treating the [Cybersecurity Roles Interview Questions](../../interview-questions/company-wise/cybersecurity-roles.md)
as a floor, not a ceiling.

**If you're headed toward Backend or Software Engineering:**
This roadmap deliberately taught Linux as the platform your code runs on, not programming itself —
the natural next step is depth in your actual language/framework, plus
[Developer Essentials](/developer-essentials) (Git, GitHub, and workflow) if that section is newer
to you than the Linux material was.

**Regardless of path:**
[Developer Essentials](/developer-essentials) (Git basics, GitHub, branching workflows) sits
alongside this roadmap rather than inside it, and is worth a look if you haven't covered it
elsewhere — nearly every path benefits from real Git fluency.

## Real-world example

An engineer finishes this roadmap and takes a DevOps role. Module 26 gave them enough Terraform
vocabulary to follow code reviews and make small changes confidently, but the team's actual
infrastructure-as-code practice (modules, remote state, workspaces) required a dedicated Terraform
deep dive in the first month on the job — exactly the kind of next step this page points toward
rather than assuming the roadmap alone was sufficient for that specific responsibility.

## Commands

No new command example on this page — see [Module 26](../26-linux-for-devops/index.md) and
[Module 24](../24-kubernetes-basics/index.md) for this roadmap's introductory coverage of the
tools discussed above.

## Common mistakes

- Assuming this roadmap's introductory Kubernetes/Terraform/Ansible coverage is sufficient depth
  for a role that specifically centers on one of them.
- Picking a "what's next" direction generically instead of based on your actual target career
  path — the right next step genuinely differs between a DevOps role and a cybersecurity role.
- Treating "next steps" as a one-time decision instead of revisiting it as your actual role and
  interests become clearer.

## Best practices

- Match your next-step choice to your actual target role, not to whichever tool sounds most
  interesting in isolation.
- Go deep on one direction at a time rather than shallowly across several — Terraform, Ansible,
  and a Kubernetes deep dive are each substantial enough to warrant focused study.
- Revisit [Full Roadmap Recap](full-roadmap-recap.md) periodically even after moving on — Linux
  fundamentals stay relevant underneath whatever you specialize into next.

## Exercises

1. Identify which of the five directions above matches your actual current or target role.
2. Pick one specific next resource (a course, official docs, a project) for that direction and
   commit to a first concrete milestone.
3. Revisit [Full Roadmap Recap](full-roadmap-recap.md) in three months and confirm the
   fundamentals underneath your new specialization haven't decayed.

## Quiz

**Q: Why does this roadmap treat Kubernetes, Terraform, and Ansible as introductory rather than teaching them in depth?**
<details><summary>Show answer</summary>
Because each is substantial enough to warrant its own dedicated deep dive, and which one matters
most genuinely depends on the learner's specific career direction — this roadmap's job is the
shared Linux foundation underneath all of them, not full mastery of every specialization built on
top of it.
</details>

**Q: Why might "what to learn next" differ meaningfully between a DevOps-track and a cybersecurity-track learner?**
<details><summary>Show answer</summary>
Because the natural next layer of depth is different for each — infrastructure-as-code tooling
(Terraform/Ansible) for DevOps versus deeper pentesting/SIEM skills for cybersecurity — even
though both built on the exact same Linux fundamentals in this roadmap.
</details>

## Interview questions

- What's the difference between Terraform and Ansible? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md#terraform-vs-ansible)

## Key Takeaways

- This roadmap is a foundation, not a ceiling — Modules 22-28 are intentionally introductory.
- The right "next step" depends on your actual target career path, not a single universal answer.
- Terraform + Ansible for DevOps/Platform, a real Kubernetes deep dive for Cloud/Platform, deeper
  SIEM/pentesting for Cybersecurity, language/framework depth for Backend/Software Engineering.
- [Developer Essentials](/developer-essentials) (Git) is worth covering regardless of path.

## Related topics

- [Readiness Self-Assessment / Mock Interview](readiness-self-assessment-mock-interview.md)
- [Linux for DevOps](../26-linux-for-devops/index.md)
- [Kubernetes Basics](../24-kubernetes-basics/index.md)
- [Developer Essentials](/developer-essentials)

---

This is the final topic in the 34-module Linux roadmap. Return to the
[Full Roadmap Recap](full-roadmap-recap.md) for the complete overview, or head to
[Practice Projects](/projects), [Real Production Labs](/labs), or the
[Interview Questions Hub](../../interview-questions/index.md) to keep applying what you've learned.
