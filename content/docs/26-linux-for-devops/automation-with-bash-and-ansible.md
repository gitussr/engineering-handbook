---
title: "Automation with Bash and Ansible"
description: "Bash scripts automate one machine well; Ansible automates a fleet declaratively and idempotently over SSH — when to reach for each, and why Ansible doesn't replace Bash so much as orchestrate it."
module: "26-linux-for-devops"
moduleTitle: "Linux for DevOps"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["26-linux-for-devops/linux-role-in-ci-cd"]
relatedTopics: ["linux-role-in-ci-cd", "infrastructure-as-code-basics-terraform-on-linux"]
relatedCommands: ["ssh"]
careerRelevance: ["devops", "sre", "platform", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#ansible-vs-bash-script"]
relatedCheatsheet: ""
furtherReading: [{"label": "Ansible Documentation", "url": "https://docs.ansible.com/"}]
nextTopic: "26-linux-for-devops/infrastructure-as-code-basics-terraform-on-linux"
prevTopic: "26-linux-for-devops/linux-role-in-ci-cd"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["ansible vs bash script", "ansible playbook explained", "idempotent automation", "ansible ssh automation", "bash automation limits"]
canonicalUrl: "/docs/linux-for-devops/automation-with-bash-and-ansible"
---

# Automation with Bash and Ansible

🟡 Good to Know · Relevant for: DevOps · SRE · Platform · Linux Administrator

> **TL;DR:** A Bash script (Module 18) automates one machine well; Ansible automates many machines
> declaratively and idempotently over plain SSH — describing the desired end state instead of a
> sequence of imperative steps, and safely re-runnable without re-checking what already happened.

## What is it?

Ansible is a configuration management and automation tool that connects to remote Linux hosts over
standard SSH (no agent installed on the target) and applies configuration described in YAML
"playbooks" — extending the Bash automation skills from
[Module 18](../18-shell-scripting/index.md) to many machines at once, with idempotency guarantees
a raw Bash loop doesn't provide by default.

## Why does it exist?

A Bash script that installs a package and starts a service works fine run once, on one machine. Run
it again, and a naive script might error trying to re-install an already-installed package, or
restart a service unnecessarily. Ansible playbooks describe the *desired state* ("nginx should be
installed and running") rather than a sequence of steps, so running the same playbook repeatedly
is safe — a property called idempotency — and the same playbook applies identically across an
entire fleet.

## Where is it used?

Configuration management across server fleets: ensuring every web server has the same package
versions, the same configuration files, and the same running services, without manually SSHing
into each one — the same fleet-management problem introduced in
[Managing Cloud VMs over SSH at Scale](../25-linux-for-cloud/managing-cloud-vms-over-ssh-at-scale.md),
solved here with a purpose-built tool instead of ad hoc scripting.

## How it works

> 📊 Diagram: a control machine running `ansible-playbook`, connecting over plain SSH (the same
> mechanism from Module 13) to a list of target hosts (an "inventory"), applying the same YAML
> playbook to each in parallel — contrasted with a Bash `for` loop doing the same SSH connections
> manually, without idempotency checks.

```yaml
# playbook.yml
- hosts: web_servers
  become: true
  tasks:
    - name: Ensure nginx is installed
      apt:
        name: nginx
        state: present
    - name: Ensure nginx is running
      service:
        name: nginx
        state: started
        enabled: true
```

Running this against an inventory of hosts connects over SSH (using the exact key-based auth from
[Module 13](../13-ssh/ssh-key-generation-and-key-based-auth.md)) and only makes changes where
the actual state differs from the desired state — a host that already has nginx installed and
running reports "no changes," not an error.

## Real-world example

A team maintains a Bash script that SSHes into each of 30 servers in a loop to deploy a
configuration change, and the script has grown fragile — it doesn't handle a single unreachable
host gracefully, and re-running it after a partial failure risks re-applying steps that already
succeeded. Replacing it with an Ansible playbook targeting the same 30 hosts (defined in an
inventory file) handles unreachable hosts individually without aborting the whole run, and safely
re-applies only what's actually needed on retry — exactly the idempotency the hand-rolled script
lacked.

## Syntax

```
ansible-playbook -i INVENTORY PLAYBOOK.yml
```

## Commands

No new canonical command page — `ansible`/`ansible-playbook` are shown illustratively above,
building on [`ssh`](../../commands/ssh.md) (Module 13) as the underlying transport; per
[architecture/01-roadmap.md's](../../../architecture/01-roadmap.md) scalability rule, full
Ansible usage is a candidate for a future sibling section outside the core 34-module roadmap, the
same treatment planned for Terraform.

## Production example

```
$ ansible-playbook -i inventory.ini playbook.yml

PLAY [web_servers] ****************************************************

TASK [Ensure nginx is installed] ***************************************
ok: [web-01]
changed: [web-02]

TASK [Ensure nginx is running] ******************************************
ok: [web-01]
ok: [web-02]

PLAY RECAP ****************************************************************
web-01   : ok=2  changed=0  unreachable=0  failed=0
web-02   : ok=2  changed=1  unreachable=0  failed=0
```

`web-01` reports `changed=0` because it already matched the desired state; `web-02` shows
`changed=1` because nginx actually needed installing — idempotency visible directly in the output.

## Do / Don't

| Do | Don't |
|---|---|
| Use Ansible for fleet-wide, repeatable configuration | Loop a Bash script over SSH for the same job at scale |
| Describe desired state in a playbook | Script an imperative sequence that assumes a clean starting state |
| Reuse existing SSH key-based auth | Set up separate credentials just for Ansible |

## Common mistakes

- Continuing to hand-loop Bash scripts over SSH for fleet-wide changes long after the fleet has
  grown large enough that a purpose-built tool would be safer and faster.
- Writing an Ansible task that isn't actually idempotent (e.g. a raw shell command that always
  reports "changed"), losing the safety idempotency is supposed to provide.
- Assuming Ansible replaces Bash entirely — Bash remains the right tool for a single-machine script
  or a task inside an Ansible playbook itself (the `command`/`shell` modules still run Bash).

## Best practices

- Reach for Ansible once a script needs to run consistently across more than a handful of hosts,
  not before — a single-host script doesn't need the added complexity.
- Prefer Ansible's built-in modules (`apt`, `service`, `copy`) over raw `shell`/`command` tasks
  whenever a module exists, since modules are idempotent by design and `shell` isn't automatically.
- Keep playbooks in version control alongside other infrastructure code, the same discipline
  applied to `cloud-init` user-data in Module 25.

## Exercises

1. Write a Bash `for` loop that SSHes into three hosts and checks whether a package is installed,
   then describe what would need to change to make it safely re-runnable.
2. Write a simple Ansible playbook (or read one) and identify which tasks are idempotent by
   default versus which use a raw `shell`/`command` module.
3. Explain in your own words why "changed=0" in an Ansible run is a meaningful, desirable result.

## Quiz

**Q: What does "idempotent" mean in the context of Ansible playbooks?**
<details><summary>Show answer</summary>
Running the same playbook repeatedly is safe — it only makes changes where the actual state
differs from the desired state, rather than blindly re-running every step.
</details>

**Q: What transport does Ansible use to reach target hosts, and does it require an agent installed there?**
<details><summary>Show answer</summary>
Plain SSH, the same mechanism from Module 13 — no agent needs to be installed on target hosts.
</details>

**Q: Does Ansible replace the need for Bash scripting knowledge?**
<details><summary>Show answer</summary>
No — Bash remains the right tool for single-machine scripts, and Ansible's `shell`/`command`
modules still execute Bash under the hood when no purpose-built module exists for a task.
</details>

## Interview questions

- Why would you choose Ansible over a Bash script looping over SSH for a fleet of servers? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- Ansible automates configuration across a fleet declaratively and idempotently, over plain SSH,
  with no agent required on target hosts.
- Idempotency means safe re-runs — only actual differences from the desired state produce changes.
- Ansible doesn't replace Bash; it orchestrates it, and Bash remains right for single-machine
  scripts.
- Reach for Ansible once fleet size makes hand-looped Bash-over-SSH fragile, not before.

## Further Reading

- [Ansible Documentation](https://docs.ansible.com/)

## Related topics

- [Linux's Role in CI/CD](linux-role-in-ci-cd.md)
- [Infrastructure as Code Basics: Terraform on Linux](infrastructure-as-code-basics-terraform-on-linux.md)
