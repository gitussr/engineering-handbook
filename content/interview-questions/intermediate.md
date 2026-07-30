---
title: "Linux Interview Questions — Intermediate"
description: "Intermediate-level Linux interview questions covering cron and scheduling, shell scripting, security hardening, monitoring, performance tuning, containers/Docker, Kubernetes basics, and cloud/DevOps roles — each with a concise answer and a link to the full topic explanation."
type: "interview"
tier: "intermediate"
updatedAt: "2026-07-29"
keywords: ["intermediate linux interview questions", "linux interview questions 2-5 years experience", "devops linux interview questions", "docker kubernetes linux interview questions"]
canonicalUrl: "/interview-questions/intermediate"
---

# Linux Interview Questions — Intermediate

Questions for a candidate with roughly 2-5 years of experience: scheduling and shell scripting,
security hardening, monitoring, performance tuning, containers, Kubernetes fundamentals, and the
cloud/DevOps/cybersecurity/web-server skills built in this roadmap's career-branch modules. Each
answer is a concise pointer — click "Full explanation" for the full topic page.

## Cron and shell scripting

<a id="cron-job-not-running"></a>
**Q: A cron job works fine when run manually but silently fails when run by cron. Why?**
<details><summary>Show answer</summary>
Cron runs jobs with a minimal environment (no interactive shell's `PATH`, no aliases, no
`.bashrc`), so a script relying on tools resolved only via an interactive `PATH`, or on unset
environment variables, breaks under cron even though it works fine manually. Always use absolute
paths and set `PATH` explicitly inside cron scripts.

Full explanation → [Cron Troubleshooting and Logging](../docs/17-cron/cron-troubleshooting-and-logging.md)
</details>

<a id="systemd-timers-vs-cron"></a>
**Q: Why would you choose a systemd timer over cron?**
<details><summary>Show answer</summary>
Systemd timers integrate with journald for logging, can express dependencies on other units, log
a job's actual exit status, and can catch up on missed runs (`Persistent=true`) — cron does none
of these natively and fails silently by comparison.

Full explanation → [Systemd Timers](../docs/17-cron/systemd-timers.md)
</details>

<a id="debugging-bash-scripts"></a>
**Q: How do you debug a Bash script that's behaving unexpectedly?**
<details><summary>Show answer</summary>
Run it with `bash -x script.sh` to print every command as it executes with substituted variable
values, and run it through `shellcheck` first to catch quoting and logic mistakes before you even
run it.

Full explanation → [Debugging Scripts (set -x, ShellCheck)](../docs/18-shell-scripting/debugging-scripts-set-x-shellcheck.md)
</details>

<a id="set-e-pitfalls"></a>
**Q: What's a common pitfall with `set -e` in Bash scripts?**
<details><summary>Show answer</summary>
`set -e` exits on any command's non-zero status, but it's silently ignored inside conditionals,
`&&`/`||` chains, and inside a pipeline's non-final command by default — engineers assume "the
script stops on any error" when it actually doesn't in several common cases.

Full explanation → [Exit Codes and Error Handling](../docs/18-shell-scripting/exit-codes-and-error-handling.md)
</details>

## Security

<a id="dac-vs-mac"></a>
**Q: What's the difference between DAC and MAC in Linux security?**
<details><summary>Show answer</summary>
DAC (Discretionary Access Control) is the standard owner/group/other permission model — the file
owner decides who gets access. MAC (Mandatory Access Control, via SELinux/AppArmor) enforces
system-wide policy that even the file owner can't override, confining what a process can do
regardless of standard file permissions.

Full explanation → [Linux Security Model Overview](../docs/19-security/linux-security-model-overview.md)
</details>

<a id="selinux-enforcing-vs-permissive"></a>
**Q: What's the difference between SELinux "enforcing" and "permissive" mode?**
<details><summary>Show answer</summary>
Enforcing mode actively blocks and logs policy violations; permissive mode only logs what *would*
have been blocked without actually blocking it — permissive is used to test a policy change safely
before switching to enforcing in production.

Full explanation → [SELinux and AppArmor](../docs/19-security/selinux-and-apparmor.md)
</details>

<a id="vuln-scan-vs-pentest"></a>
**Q: What's the difference between a vulnerability scan and a penetration test?**
<details><summary>Show answer</summary>
A vulnerability scan is automated and breadth-first — it identifies known CVEs and
misconfigurations across many hosts quickly. A penetration test is manual, goal-directed, and
depth-first — a tester actively tries to chain findings into real exploitation, which automated
scanning alone can't demonstrate.

Full explanation → [Vulnerability Scanning Basics](../docs/19-security/vulnerability-scanning-basics.md)
</details>

## Monitoring

<a id="prometheus-vs-nagios"></a>
**Q: What's the architectural difference between Prometheus and Nagios-style monitoring?**
<details><summary>Show answer</summary>
Nagios is push/check-based and alert-centric — it runs checks and fires alerts on failure.
Prometheus pulls (scrapes) time-series metrics continuously and stores them, letting you query
historical trends, not just current up/down state — alerting is a separate layer (Alertmanager)
built on top of that metric data.

Full explanation → [Monitoring Stacks Overview](../docs/20-monitoring/monitoring-stacks-overview.md)
</details>

<a id="alert-fatigue"></a>
**Q: What is alert fatigue, and how do you design thresholds to avoid it?**
<details><summary>Show answer</summary>
Alert fatigue happens when too many low-value or flapping alerts train responders to ignore
notifications, including real ones. Avoid it by alerting on symptoms that matter to users (not
every metric fluctuation), using sustained-duration thresholds instead of instant triggers, and
tuning severity so only actionable alerts page someone at 2am.

Full explanation → [Alerting Concepts and Thresholds](../docs/20-monitoring/alerting-concepts-and-thresholds.md)
</details>

## Performance

<a id="load-average-vs-cpu-utilization"></a>
**Q: What's the difference between load average and CPU utilization?**
<details><summary>Show answer</summary>
CPU utilization measures how busy the CPU actually is; load average measures the number of
processes running or waiting for a resource (CPU or I/O) averaged over 1/5/15 minutes. A high load
average with low CPU utilization usually points to processes blocked on disk I/O, not CPU
contention.

Full explanation → [CPU Performance (mpstat, top)](../docs/21-performance/cpu-performance-mpstat-top.md)
</details>

<a id="free-vs-available-memory"></a>
**Q: Why does "free" memory look low on a healthy Linux server?**
<details><summary>Show answer</summary>
Linux aggressively uses spare RAM for disk caching to speed up future reads, so `free`'s "free"
column looks small on a healthy system by design — the `available` column is the number that
actually matters, since cached memory is instantly reclaimable if an application needs it.

Full explanation → [Memory Performance (free, vmstat)](../docs/21-performance/memory-performance-free-vmstat.md)
</details>

<a id="iotop-vs-iostat"></a>
**Q: What's the difference between `iostat` and `iotop`?**
<details><summary>Show answer</summary>
`iostat` reports disk I/O statistics per device (throughput, utilization, wait times) but doesn't
show which process is responsible; `iotop` shows I/O usage per process in real time, which is what
you need to identify exactly which process is saturating disk I/O.

Full explanation → [Disk I/O Performance (iostat, iotop)](../docs/21-performance/disk-io-performance-iostat-iotop.md)
</details>

## Containers and Docker

<a id="namespaces-vs-cgroups"></a>
**Q: What's the difference between namespaces and cgroups, and why does a container need both?**
<details><summary>Show answer</summary>
Namespaces isolate *what a process can see* (its own process list, network stack, mounts, hostname);
cgroups limit *how much of the host's resources* it can consume (CPU, memory, I/O). A container
needs namespaces for isolation and cgroups for resource limits — neither alone makes a container.

Full explanation → [Namespaces and cgroups Explained](../docs/22-containers/namespaces-and-cgroups-explained.md)
</details>

<a id="docker-vs-containerd-vs-cri-o"></a>
**Q: What's the difference between Docker, containerd, and CRI-O?**
<details><summary>Show answer</summary>
containerd is a low-level container runtime; Docker is a full toolchain built on top of it, adding
a CLI, image build tooling, and networking; CRI-O is a minimal runtime built specifically to
implement Kubernetes' Container Runtime Interface, with no extra tooling Kubernetes doesn't need.

Full explanation → [Container Runtimes Overview](../docs/22-containers/container-runtimes-overview.md)
</details>

<a id="docker-volume-vs-bind-mount"></a>
**Q: What's the difference between a Docker volume and a bind mount?**
<details><summary>Show answer</summary>
A named volume is managed entirely by Docker and stored in Docker's own storage area, portable
across hosts via Docker tooling; a bind mount maps a specific host filesystem path directly into
the container, useful for development but tying the container to that host's exact directory
layout.

Full explanation → [Docker Volumes](../docs/23-docker-basics/docker-volumes.md)
</details>

<a id="docker-compose-vs-manual-run"></a>
**Q: Why use Docker Compose instead of a series of `docker run` commands?**
<details><summary>Show answer</summary>
Compose declares an entire multi-container application (services, networks, volumes, dependencies)
in one version-controlled YAML file, brought up or down with a single command — reproducing the
equivalent by hand means remembering and re-typing every flag across every `docker run` correctly,
every time.

Full explanation → [Docker Compose Intro](../docs/23-docker-basics/docker-compose-intro.md)
</details>

## Kubernetes basics

<a id="pod-vs-deployment-vs-service"></a>
**Q: What's the difference between a Pod, a Deployment, and a Service in Kubernetes?**
<details><summary>Show answer</summary>
A Pod is the smallest deployable unit (one or more tightly-coupled containers); a Deployment
manages a desired number of Pod replicas and handles rolling updates/self-healing; a Service gives
a stable network identity/IP in front of a changing set of Pods, since Pods themselves are
ephemeral and get replaced with new IPs constantly.

Full explanation → [Pods, Deployments, Services](../docs/24-kubernetes-basics/pods-deployments-services.md)
</details>

<a id="control-plane-vs-node"></a>
**Q: What's the difference between the Kubernetes control plane and a worker node?**
<details><summary>Show answer</summary>
The control plane (API server, scheduler, controller manager, etcd) makes cluster-wide decisions
and stores desired state; worker nodes are where your actual application Pods run, each running a
kubelet that talks to the control plane and enforces what it's told to run.

Full explanation → [Kubernetes Architecture (Control Plane and Nodes)](../docs/24-kubernetes-basics/k8s-architecture-control-plane-and-nodes.md)
</details>

<a id="kubectl-get-vs-describe"></a>
**Q: What's the difference between `kubectl get` and `kubectl describe`?**
<details><summary>Show answer</summary>
`kubectl get` gives a compact summary/table view of resources (good for a quick overview or
scripting); `kubectl describe` gives the full detail on one resource including recent events —
`describe` is what you reach for when something's actually broken and you need to know why.

Full explanation → [kubectl Basics](../docs/24-kubernetes-basics/kubectl-basics.md)
</details>

## Cloud and DevOps

<a id="what-is-cloud-init"></a>
**Q: What is cloud-init, and why does almost every cloud VM image ship with it?**
<details><summary>Show answer</summary>
`cloud-init` is the industry-standard tool that configures a fresh cloud VM on first boot —
injecting SSH keys, setting the hostname, running startup scripts — from metadata the cloud
provider supplies, so images stay generic and get customized per-launch instead of needing a
custom image per configuration.

Full explanation → [cloud-init](../docs/25-linux-for-cloud/cloud-init.md)
</details>

<a id="instance-role-vs-hardcoded-credentials"></a>
**Q: Why should a cloud VM use an instance role instead of hardcoded cloud credentials?**
<details><summary>Show answer</summary>
An instance role (IAM role/managed identity) hands the VM temporary, auto-rotating credentials
scoped to exactly what it needs, with nothing long-lived stored on disk to leak; hardcoded
credentials are static, over-broad by default, and become a standing liability the moment they're
committed or exposed.

Full explanation → [IAM and Instance Roles](../docs/25-linux-for-cloud/iam-and-instance-roles.md)
</details>

<a id="terraform-vs-ansible"></a>
**Q: What's the difference between Terraform and Ansible?**
<details><summary>Show answer</summary>
Terraform is a declarative infrastructure-provisioning tool — it creates and tracks the actual
cloud resources (VMs, networks, load balancers) via state. Ansible is a configuration-management
tool — it configures software and state *inside* machines that already exist. They're commonly
used together: Terraform provisions the VM, Ansible configures it.

Full explanation → [Infrastructure as Code Basics (Terraform on Linux)](../docs/26-linux-for-devops/infrastructure-as-code-basics-terraform-on-linux.md)
</details>

<a id="what-actually-runs-ci-cd-pipeline-steps"></a>
**Q: When a CI/CD pipeline "runs a step," what is actually executing it?**
<details><summary>Show answer</summary>
Every pipeline step ultimately runs as ordinary Linux shell commands inside a runner/agent
process — the YAML pipeline definition is a thin orchestration layer describing which shell
commands to run, in what order, and Linux itself (processes, exit codes, environment variables) is
doing the real work underneath.

Full explanation → [Linux's Role in CI/CD](../docs/26-linux-for-devops/linux-role-in-ci-cd.md)
</details>

## Cybersecurity

<a id="nmap-vs-tcpdump-use-cases"></a>
**Q: When would you use `nmap` versus `tcpdump`?**
<details><summary>Show answer</summary>
`nmap` actively probes hosts to discover what's open (ports, services, OS fingerprints) — it
generates traffic. `tcpdump` passively captures traffic already flowing on the wire, for analyzing
what's actually happening rather than what's reachable. Reconnaissance reaches for `nmap`; incident
investigation reaches for `tcpdump`.

Full explanation → [Network Security Tools (nmap, tcpdump, Wireshark CLI)](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md)
</details>

<a id="soc-analyst-linux-skills"></a>
**Q: What Linux skills does a SOC analyst rely on daily that a general sysadmin might not use as often?**
<details><summary>Show answer</summary>
Heavy log analysis (`grep`/`awk`/`journalctl` across auth, syslog, and application logs), building
a timeline of events across multiple log sources, and recognizing normal vs anomalous process/
network activity — less about configuring systems, more about interpreting what already happened
on one.

Full explanation → [Linux for SOC Analysts](../docs/27-linux-for-cybersecurity/linux-for-soc-analysts.md)
</details>

## Web servers

<a id="apache-vs-nginx-architecture"></a>
**Q: What's the core architectural difference between Apache and Nginx?**
<details><summary>Show answer</summary>
Apache traditionally spawns a process or thread per connection, which scales less efficiently
under very high concurrency; Nginx uses a single-threaded, event-driven architecture that handles
thousands of concurrent connections per worker with far less memory overhead — the reason Nginx is
the default choice as a reverse proxy/load balancer.

Full explanation → [Apache Install and Config](../docs/28-linux-for-web-servers/apache-install-and-config.md)
</details>

<a id="reverse-proxy-vs-forward-proxy"></a>
**Q: What's the difference between a reverse proxy and a forward proxy?**
<details><summary>Show answer</summary>
A forward proxy sits in front of clients, making requests on their behalf to the wider internet
(hiding the client from the server); a reverse proxy sits in front of servers, receiving requests
on their behalf from the wider internet (hiding the backend server from the client).

Full explanation → [Reverse Proxy Basics](../docs/28-linux-for-web-servers/reverse-proxy-basics.md)
</details>

<a id="502-vs-504-vs-500-difference"></a>
**Q: Explain the difference between a 500, 502, and 504 HTTP error.**
<details><summary>Show answer</summary>
A 500 means the backend received the request and threw an internal error; a 502 means the proxy
got no response at all (backend down/unreachable); a 504 means the proxy got no response within
its timeout window (backend reachable but too slow) — three distinct failures pointing at
different investigations.

Full explanation → [Troubleshooting Web Server Errors](../docs/28-linux-for-web-servers/troubleshooting-web-server-errors.md)
</details>

## Related topics

- [Beginner Interview Questions](beginner.md)
- [Senior/Expert Interview Questions](senior.md)
- [Scenario-Based Interview Questions](scenario-based.md)
- [Company-Wise Interview Questions](company-wise/devops-roles.md)
- [Interview Questions Hub](index.md)
