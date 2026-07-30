---
title: "cloud-init"
description: "The industry-standard tool that configures a cloud VM on first boot — hostname, users, SSH keys, packages, and networking — from provider-supplied metadata, no manual login required."
module: "25-linux-for-cloud"
moduleTitle: "Linux for Cloud"
stage: "production-engineer"
difficulty: "good-to-know"
foundational: false
type: "concept"
prerequisites: ["25-linux-for-cloud/linux-on-aws-gcp-azure"]
relatedTopics: ["linux-on-aws-gcp-azure", "auto-scaling-and-custom-linux-images-amis"]
relatedCommands: ["cloud-init"]
careerRelevance: ["devops", "cloud", "platform", "sre"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#what-is-cloud-init"]
relatedCheatsheet: ""
furtherReading: [{"label": "cloud-init Documentation", "url": "https://cloudinit.readthedocs.io/"}]
nextTopic: "25-linux-for-cloud/managing-cloud-vms-over-ssh-at-scale"
prevTopic: "25-linux-for-cloud/linux-on-aws-gcp-azure"
estimatedReadingTime: 8
updatedAt: "2026-07-29"
keywords: ["cloud-init explained", "cloud-init user-data", "cloud-init yaml example", "what does cloud-init do", "cloud-init first boot"]
canonicalUrl: "/docs/linux-for-cloud/cloud-init"
---

# cloud-init

🟡 Good to Know · Relevant for: DevOps · Cloud · Platform · SRE

> **TL;DR:** `cloud-init` is the industry-standard tool nearly every cloud image ships with — it
> reads provider metadata and a user-supplied config on first boot to set the hostname, create
> users, install SSH keys, run commands, and configure networking, with no manual login required.

## What is it?

`cloud-init` is a boot-time service that initializes a fresh cloud instance using metadata the
provider supplies and an optional YAML configuration ("user data") the person launching the
instance provides. It runs automatically as part of early boot on nearly every cloud-provided
Linux image.

## Why does it exist?

Launching a fleet of instances one at a time and manually configuring each — setting a hostname,
adding an SSH key, installing packages — doesn't scale past a handful of servers. `cloud-init`
turns that manual process into a declarative config file applied automatically the moment an
instance boots, making instance creation fully automatable via API or infrastructure-as-code
tooling.

## Where is it used?

Every time an instance launches on AWS, GCP, or Azure from a standard image — whether launched by
hand through a console, by a script calling the provider's API, or by infrastructure-as-code
tooling (Terraform, covered in Module 26). It's also how container-adjacent VM images and
auto-scaling groups (see [Auto-Scaling and Custom Linux Images](auto-scaling-and-custom-linux-images-amis.md))
configure each new instance identically without a human touching it.

## How it works

> 📊 Diagram: a VM boot sequence showing `cloud-init` running in four stages — Generator (detects
> the datasource/provider), Local (network config), Config (users, packages, SSH keys from
> user-data YAML), and Final (runs user-supplied scripts) — each stage pulling from the provider's
> metadata service.

`cloud-init` runs in four ordered stages during boot, each handling a different category of setup:
detecting which cloud provider it's running on, configuring networking, applying the main
configuration (users, packages, files), and finally running any custom scripts. The configuration
itself — "user data" — is YAML, supplied when the instance is launched:

```yaml
#cloud-config
hostname: web-prod-03
users:
  - name: deploy
    ssh-authorized-keys:
      - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... deploy@bastion
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
packages:
  - nginx
  - curl
runcmd:
  - systemctl enable --now nginx
```

This single file, passed at launch time, produces a fully configured, ready-to-serve instance with
no manual login step.

## Real-world example

A team manually launches 20 EC2 instances for a batch job, SSHing into each one to install the
same three packages and add the same monitoring agent — a process that takes an afternoon and
inevitably produces at least one inconsistently configured instance. Switching to a `cloud-init`
user-data script that installs the same packages and agent turns the same 20-instance launch into
a single API call, with every instance guaranteed identical because they all ran the exact same
configuration.

## Syntax

```
cloud-init COMMAND [OPTIONS]
```

## Commands

See [`cloud-init`](../../commands/cloud-init.md) for the CLI used to inspect and debug
`cloud-init`'s own run status — separate from the YAML user-data config shown above, which isn't
invoked directly but supplied at instance launch time.

## Production example

```
$ cloud-init status
status: done

$ cloud-init analyze show
-- Boot Record 01 --
The total time elapsed since completing an event is 12.443 seconds

Startup finished in 3.201 seconds (kernel) + 9.242 seconds (userspace) = 12.443 seconds
```

Confirming `cloud-init` completed successfully and how long first-boot configuration took — the
first thing to check when a newly launched instance doesn't look the way its user-data config
expected.

## Do / Don't

| Do | Don't |
|---|---|
| Configure hostname/users/networking through `cloud-init` user-data | Hand-configure these after manually logging in |
| Check `cloud-init status`/logs when an instance doesn't boot as expected | Assume a misconfigured instance means `cloud-init` itself is broken |
| Keep user-data version-controlled alongside infrastructure code | Treat user-data as a one-off, unrepeatable snippet typed at launch time |

## Common mistakes

- Manually configuring something (hostname, network, users) that `cloud-init` already manages,
  then being confused when it's overwritten on the next boot — see
  [Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md).
- Writing invalid YAML in user-data and not noticing until the instance boots with none of the
  intended configuration applied — `cloud-init status` and its logs are the first troubleshooting
  step.
- Assuming `cloud-init` only runs once ever, when by default it reruns relevant stages on every
  boot unless the instance ID is unchanged (cached as "already run").

## Best practices

- Keep user-data files in version control alongside other infrastructure configuration, not typed
  ad hoc into a console at launch time.
- Use `cloud-init status --wait` in automation scripts that need to confirm an instance has
  finished booting before proceeding (e.g. before running a health check).
- Check `/var/log/cloud-init.log` and `/var/log/cloud-init-output.log` first when an instance
  doesn't come up configured as expected.

## Exercises

1. Write a `cloud-init` user-data YAML file that creates a user, installs one package, and starts
   a service.
2. Run `cloud-init status` and `cloud-init analyze show` on a running cloud instance (or a local
   VM using `cloud-init`) and interpret the output.
3. Identify which log file to check when an instance's user-data doesn't seem to have applied.

## Quiz

**Q: What triggers `cloud-init` to run, and when?**
<details><summary>Show answer</summary>
It runs automatically during early boot on cloud instances, reading provider metadata and any
user-supplied YAML configuration to set up the instance before it's fully available.
</details>

**Q: Why does manually configuring the hostname on a `cloud-init`-managed instance often not stick?**
<details><summary>Show answer</summary>
`cloud-init` re-applies its configured hostname (and other settings) on subsequent boots by
default, overwriting manual changes that weren't expressed through user-data.
</details>

**Q: Where would you look first if an instance didn't apply its intended `cloud-init` configuration?**
<details><summary>Show answer</summary>
`cloud-init status` for a quick pass/fail, then `/var/log/cloud-init.log` and
`/var/log/cloud-init-output.log` for detail on what ran and any errors.
</details>

## Interview questions

- What is `cloud-init` and why does nearly every cloud image ship with it? →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- `cloud-init` automates first-boot configuration — hostname, users, SSH keys, packages, and
  commands — from a YAML user-data file supplied at instance launch.
- It reruns relevant stages on subsequent boots by default, which is why manual configuration
  changes to `cloud-init`-managed settings don't reliably persist.
- `cloud-init status` and its log files are the standard troubleshooting entry point.
- User-data belongs in version control, the same as any other infrastructure configuration.

## Further Reading

- [cloud-init Documentation](https://cloudinit.readthedocs.io/)

## Related topics

- [Linux on AWS/GCP/Azure](linux-on-aws-gcp-azure.md)
- [Auto-Scaling and Custom Linux Images](auto-scaling-and-custom-linux-images-amis.md)
