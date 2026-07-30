---
title: "Installing Docker"
description: "Getting Docker Engine running on a Linux host via the distro's package manager, and the post-install group membership step that's easy to forget."
module: "23-docker-basics"
moduleTitle: "Docker Basics"
stage: "professional"
difficulty: "must-know"
foundational: false
type: "concept"
prerequisites: ["23-docker-basics/docker-architecture"]
relatedTopics: ["docker-architecture", "images-vs-containers"]
relatedCommands: []
careerRelevance: ["devops", "cloud", "linux-administrator"]
relatedLabs: []
relatedInterviewQuestions: ["beginner#docker-post-install-group"]
relatedCheatsheet: "docker-basics"
furtherReading: [{"label": "Docker Engine Install Documentation", "url": "https://docs.docker.com/engine/install/"}]
nextTopic: "23-docker-basics/images-vs-containers"
prevTopic: "23-docker-basics/docker-architecture"
estimatedReadingTime: 6
updatedAt: "2026-07-28"
keywords: ["install docker linux", "docker post install steps", "add user to docker group", "docker permission denied socket"]
canonicalUrl: "/docs/docker-basics/installing-docker"
---

# Installing Docker

🟢 Must Know · Relevant for: DevOps · Cloud · Linux Administrator

> **TL;DR:** Install Docker Engine via the distro's package manager, start/enable the `docker`
> service, then add your user to the `docker` group — skipping that last step means every command
> needs `sudo`, and forgetting to log out/in after means the group change doesn't yet apply.

## What is it?

The practical steps to get Docker Engine running on a Linux host, using the package-management
skills from [Module 14](../14-package-managers/index.md) and the service-management skills from
[Module 11](../11-services/index.md) directly.

## Why does it exist?

Docker isn't pre-installed on most distros — it needs to be installed and configured like any
other service, and the [architecture](docker-architecture.md) from the previous page (a daemon
that needs to be running) means installation isn't complete until that daemon is up and the
current user can actually talk to it.

## Where is it used?

Every fresh server or workstation that will run containers — provisioning automation typically
includes Docker installation as a standard step for hosts that need it, the same way it would
include [hardening](../19-security/server-hardening-checklist.md) or a
[monitoring agent](../20-monitoring/setting-up-a-basic-monitoring-agent.md).

## How it works

> 📊 Diagram: an installation sequence — package manager installs Docker Engine packages →
> systemd enables and starts the `docker` service (the daemon from the
> [previous page](docker-architecture.md)) → the current user is added to the `docker` group →
> a fresh shell session picks up that new group membership, completing the setup.

**The installation sequence, using Debian/Ubuntu as the concrete example** (RHEL-family follows the
same shape with `dnf` instead of `apt`, Module 14):

```bash
sudo apt update
sudo apt install docker.io          # or Docker's own repository package, per official docs
sudo systemctl enable --now docker  # Module 11 pattern
```

**The easy-to-forget post-install step — group membership:**

```bash
sudo usermod -aG docker $USER
```

By default, the Docker daemon's socket is only accessible to root and members of the `docker`
group — without this step, every `docker` command requires `sudo`. Critically, this group change
doesn't take effect in the current shell session; a fresh login (or `newgrp docker`) is required
before it applies.

**Verifying the install:**

```bash
docker run hello-world
```

A minimal, purpose-built test image that pulls, runs, prints a confirmation message, and exits —
the standard way to confirm the entire chain (client, daemon, registry access) is working.

## Real-world example

An engineer installs Docker, runs `docker ps`, and gets a confusing "permission denied" error
referencing a socket path — despite having just added themselves to the `docker` group. The fix is
simply logging out and back in (or running `newgrp docker`) — group membership changes don't
retroactively apply to already-running shell sessions, a detail easy to miss and a common source of
"I did everything right but it still doesn't work" confusion right after installation.

## Syntax

```
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

## Commands

See [`systemctl`](../../commands/systemctl.md) (Module 11) and the package manager commands from
[Module 14](../14-package-managers/index.md), both already covered — no new command page is
introduced by installation itself; [Docker Run, ps, exec, logs](docker-run-ps-exec-logs.md)
introduces the first `docker` command page.

## Production example

```
$ sudo systemctl enable --now docker
$ sudo usermod -aG docker $USER
$ newgrp docker
$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

The complete sequence: enable the service, grant group access, apply it immediately with
`newgrp` (rather than logging out), then verify with the standard test image.

## Do / Don't

| Do | Don't |
|---|---|
| Add your user to the `docker` group after installing | Prefix every `docker` command with `sudo` indefinitely as a workaround |
| Start a fresh session (or `newgrp docker`) after the group change | Expect group membership changes to apply to an already-open shell |
| Verify with `docker run hello-world` before assuming installation succeeded | Assume installation worked just because the package manager reported success |

## Common mistakes

- Forgetting the `usermod -aG docker` step, and either being confused by constant permission
  errors or routinely prefixing every command with `sudo` as an unnecessary workaround.
- Adding the group but not starting a fresh session, then being confused that the permission error
  persists despite "fixing" it.
- Assuming a successful package install means Docker is fully ready, without verifying the daemon
  is actually running and reachable.

## Best practices

- Always complete the group-membership step and verify with `docker run hello-world` — package
  installation alone isn't a complete, usable setup.
- Bake Docker installation, service enablement, and group membership into provisioning automation
  rather than performing these steps manually and inconsistently per host.
- Be aware that adding a user to the `docker` group is effectively equivalent to granting root
  access on that host (a container can mount the host filesystem) — treat `docker` group membership
  with the same care as `sudo` access, a security consideration worth flagging even at the
  installation stage.

## Exercises

1. Install Docker on a system you have access to (or describe the steps if you don't).
2. Explain why `docker run hello-world` is a meaningful verification step, not just an arbitrary
   test.
3. Describe why a fresh login is needed after `usermod -aG docker`, even though the command itself
   succeeded immediately.

## Quiz

**Q: What group must a user be added to in order to run docker commands without sudo?**
<details><summary>Show answer</summary>
The `docker` group — by default, only root and members of this group can access the Docker
daemon's socket.
</details>

**Q: Why doesn't a permission fix from usermod -aG docker apply immediately in the current shell?**
<details><summary>Show answer</summary>
Group membership is evaluated when a session starts — an already-running shell doesn't pick up a
new group membership until a fresh login (or `newgrp docker`) re-evaluates it.
</details>

**Q: What does docker run hello-world verify?**
<details><summary>Show answer</summary>
That the entire chain — the client, the daemon, and registry access to pull an image — is working
correctly, by running a minimal purpose-built test image end to end.
</details>

## Interview questions

- Why might docker ps fail with a permission error immediately after installing Docker? →
  [Full answer in Beginner Interview Questions](../../interview-questions/beginner.md)

## Key Takeaways

- Install via the distro's package manager, then enable/start the daemon service (Module 11
  pattern).
- Add the current user to the `docker` group to avoid needing `sudo` for every command — this
  requires a fresh session to take effect.
- Verify the complete setup with `docker run hello-world`.
- `docker` group membership is effectively root-equivalent access — treat it as a real security
  boundary, not just a convenience setting.

## Related topics

- [Docker Architecture](docker-architecture.md)
- [Images vs Containers](images-vs-containers.md)
