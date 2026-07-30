---
title: "Linux Interview Questions — Scenario-Based"
description: "Scenario and incident-style Linux interview questions — the 'you get paged, what do you do' format covering disk, performance, services, SSH, networking, logs, cron, containers, Kubernetes, web servers, and security incidents — each with a concise troubleshooting answer and a link to the full topic explanation."
type: "interview"
tier: "scenario-based"
updatedAt: "2026-07-29"
keywords: ["scenario based linux interview questions", "linux troubleshooting interview questions", "linux incident response interview questions", "linux sysadmin scenario questions"]
canonicalUrl: "/interview-questions/scenario-based"
---

# Linux Interview Questions — Scenario-Based

Incident and ticket-style questions — the interviewer describes a symptom and wants your
diagnostic process, not a definition. Each answer is a concise pointer — click "Full explanation"
for the full topic page.

## Storage and performance

<a id="disk-full-troubleshooting-steps"></a>
**Q: A server alerts on 100% disk usage but you don't know what's consuming the space. Walk through your diagnostic steps.**
<details><summary>Show answer</summary>
Start broad with `df -h` to confirm which filesystem is full, then narrow with `du -sh */` from
likely directories (`/var/log`, `/var/lib/docker`, home directories) to find the largest
consumer. Also check for deleted-but-open files holding space via `lsof +L1`, a common case
`df` and `du` disagree on since `du` can't see space held by a file with no remaining link.

Full explanation → [df, du (Disk Usage)](../docs/15-storage/disk-usage-df-du.md)
</details>

<a id="high-load-average-low-cpu-utilization"></a>
**Q: `uptime` shows a load average of 40 on an 8-core box, but `top` shows CPU sitting mostly idle. What's going on?**
<details><summary>Show answer</summary>
Load average counts processes waiting for *any* resource, not just CPU — a high load with idle
CPU almost always points to processes stuck in uninterruptible sleep (`D` state) waiting on disk
or network I/O. Confirm with `ps -eo stat,pid,comm | grep " D"` and cross-check disk I/O, not CPU
tuning.

Full explanation → [CPU Performance (mpstat, top)](../docs/21-performance/cpu-performance-mpstat-top.md)
</details>

<a id="disk-io-saturated-app-slow-but-cpu-fine"></a>
**Q: An application is slow under load, but CPU and memory graphs both look fine. What do you check next?**
<details><summary>Show answer</summary>
Check disk I/O next — `iostat -xz 1` for per-device `%util` and average wait time, or `iotop` to
identify which process is driving it. An app can be fully I/O-bound (waiting on disk reads/writes)
while CPU and memory metrics stay flat, which is exactly why those two graphs alone don't rule out
a performance problem.

Full explanation → [Disk I/O Performance (iostat, iotop)](../docs/21-performance/disk-io-performance-iostat-iotop.md)
</details>

<a id="oom-killer-terminated-wrong-process"></a>
**Q: The OOM killer terminated your database process instead of a memory-hogging batch job running alongside it. Why, and how do you prevent it next time?**
<details><summary>Show answer</summary>
The kernel's OOM killer scores processes by memory footprint and adjustability, not by importance
to you — a large but "expected" database cache footprint can outscore a smaller runaway process.
Set `oom_score_adj` lower (harder to kill) for the process that must survive, and investigate the
actual memory pressure source with `free -h`/`vmstat` rather than only reacting after the kill.

Full explanation → [Memory Performance (free, vmstat)](../docs/21-performance/memory-performance-free-vmstat.md)
</details>

## Services and boot

<a id="systemd-service-wont-start-after-reboot"></a>
**Q: A service starts fine when you run it manually but fails to come up after a server reboot. How do you debug this?**
<details><summary>Show answer</summary>
Check `systemctl status {service}` and `journalctl -u {service} -b` first — reboot-only failures
are usually an ordering problem (the service started before a dependency like the network or a
mounted volume was ready) rather than a config error, since the config didn't change between the
manual run and the boot.

Full explanation → [systemctl: start, stop, enable, disable, status](../docs/11-services/systemctl-start-stop-enable-disable-status.md)
</details>

<a id="deploy-script-fails-permission-denied"></a>
**Q: A deploy script that has run successfully for months suddenly fails with "Permission denied" — nobody changed the script. What do you check?**
<details><summary>Show answer</summary>
Something in the environment changed even if the script didn't: check the target file/directory's
current ownership and permissions (a prior deploy or manual `chown` may have altered them), whether
the script is being run as a different user (CI runner change, cron user mismatch), and whether a
mount changed to `noexec` or read-only.

Full explanation → [Common Permission Errors](../docs/07-permissions/common-permission-errors.md)
</details>

## Networking, SSH, and web

<a id="locked-out-of-ssh-access"></a>
**Q: You've been locked out of SSH access to a production server — key auth is failing and there's no other active session. What are your options?**
<details><summary>Show answer</summary>
Use out-of-band access first — cloud provider console/serial access or physical/IPMI access —
rather than repeatedly retrying SSH, since the fastest fix is checking `sshd_config` and
`authorized_keys` directly from that console. This is also the reason production hardening
guidance always says keep a break-glass console access path, not just SSH keys.

Full explanation → [SSH Hardening (Disable Root Login, Key-Only Auth)](../docs/13-ssh/ssh-hardening-disable-root-login-key-only-auth.md)
</details>

<a id="intermittent-dns-resolution-failures"></a>
**Q: An application intermittently fails to resolve a hostname it usually resolves fine — the failures aren't constant. How do you approach this?**
<details><summary>Show answer</summary>
Intermittent (not total) DNS failure usually means one resolver in the configured list is flaky
rather than DNS being broken outright — check `/etc/resolv.conf` for multiple nameservers, then
run repeated `dig` queries against each resolver individually to isolate which one is dropping
requests, instead of testing resolution as a single pass/fail check.

Full explanation → [DNS Troubleshooting (dig, nslookup)](../docs/12-networking/dns-troubleshooting-dig-nslookup.md)
</details>

<a id="new-firewall-rule-blocks-legitimate-traffic"></a>
**Q: You added a new firewall rule to block a suspicious IP range, and now legitimate customer traffic is being dropped too. How do you find and fix this safely?**
<details><summary>Show answer</summary>
Check rule *order* first — a broad deny rule placed before a more specific allow rule wins, so the
new block may be shadowing existing legitimate-traffic rules rather than the range itself being
too wide. List active rules with their order (`iptables -L -n --line-numbers` or the `firewalld`/
`ufw` equivalent), fix the ordering or scope, and test before assuming the IP range itself was the
mistake.

Full explanation → [Firewall Configuration in Depth](../docs/19-security/firewall-configuration-in-depth.md)
</details>

<a id="intermittent-502-errors-from-load-balancer"></a>
**Q: Users report intermittent 502 errors, but the application logs show nothing wrong at the time of the errors. Where do you look?**
<details><summary>Show answer</summary>
A 502 means the *proxy* couldn't get a valid response from the backend — it can originate entirely
at the reverse proxy/load balancer layer (backend timeout, connection reset, health check
flapping) without the application ever logging anything, since the app never actually failed to
handle a request it did receive. Check the reverse proxy's own error log first, not just the app.

Full explanation → [Troubleshooting Web Server Errors](../docs/28-linux-for-web-servers/troubleshooting-web-server-errors.md)
</details>

<a id="tls-certificate-expiring-in-production"></a>
**Q: You discover a production TLS certificate expires in 6 hours and the auto-renewal didn't fire. What's your immediate plan?**
<details><summary>Show answer</summary>
Attempt a manual renewal immediately (`certbot renew --force-renewal` or equivalent) and confirm
it actually reloads the web server config, since a renewed cert that's never reloaded still serves
the old one. In parallel, check why the renewal automation (cron/systemd timer) silently failed, so
the same gap doesn't repeat at the next renewal window.

Full explanation → [SSL/TLS with Let's Encrypt (Certbot)](../docs/28-linux-for-web-servers/ssl-tls-with-lets-encrypt-certbot.md)
</details>

## Automation, packages, containers, and Kubernetes

<a id="cron-backup-job-produces-empty-file"></a>
**Q: A cron backup job has been "running successfully" every night according to its exit status, but the backup file it produces is empty. What's the likely cause?**
<details><summary>Show answer</summary>
A pipeline's exit status often only reflects the *last* command — if an earlier command in the
pipeline (e.g. a database dump) fails silently but a later command (e.g. `gzip` on empty input)
still exits 0, cron sees success. Check the script for `set -o pipefail`, and verify the actual
output file's size/contents as a health check, not just the process exit code.

Full explanation → [Cron Troubleshooting and Logging](../docs/17-cron/cron-troubleshooting-and-logging.md)
</details>

<a id="log-files-filling-up-disk"></a>
**Q: `/var/log` filled the root disk overnight and took down the server. How do you both fix it now and prevent a repeat?**
<details><summary>Show answer</summary>
Immediately free space by truncating (not deleting, to avoid breaking a process still holding the
file open) the largest active log files, then confirm `logrotate` is actually configured and
running for whichever service produced the runaway log — an unrotated or misconfigured log for one
noisy service is the usual root cause, not disk sizing.

Full explanation → [logrotate](../docs/16-logs/logrotate.md)
</details>

<a id="package-manager-broken-dependencies-mid-upgrade"></a>
**Q: A system upgrade was interrupted partway through and now the package manager reports broken dependencies on every command. How do you recover?**
<details><summary>Show answer</summary>
Don't fight it with forced installs — run the package manager's built-in repair path first (`apt
--fix-broken install` / `dpkg --configure -a` on Debian-based systems, or the equivalent `dnf`/`yum`
repair), which resolves partially-unpacked packages in the correct dependency order instead of
guessing at which package to reinstall manually.

Full explanation → [apt and dpkg](../docs/14-package-managers/apt-dpkg.md)
</details>

<a id="docker-container-exits-immediately-after-start"></a>
**Q: `docker run` starts your container but it exits immediately with no obvious error. How do you debug it?**
<details><summary>Show answer</summary>
A container exits as soon as its main process (PID 1) exits — check `docker logs {container}`
first for what that process printed before dying, then `docker ps -a` to see the exit code, which
usually distinguishes an application crash (non-zero, app-specific) from a config problem like a
missing `CMD`/entrypoint or the process backgrounding itself (exit 0 with nothing left running in
the foreground).

Full explanation → [docker run, ps, exec, logs](../docs/23-docker-basics/docker-run-ps-exec-logs.md)
</details>

<a id="kubernetes-pod-stuck-in-pending-state"></a>
**Q: A Kubernetes pod has been stuck in `Pending` state for several minutes. How do you find out why?**
<details><summary>Show answer</summary>
`Pending` means the scheduler hasn't placed the pod on a node yet — `kubectl describe pod
{name}` shows the scheduling events at the bottom, which almost always reveal the cause directly:
insufficient CPU/memory on available nodes, an unsatisfied node selector/affinity rule, or no
node matching a required toleration.

Full explanation → [Pods, Deployments, Services](../docs/24-kubernetes-basics/pods-deployments-services.md)
</details>

## Security incidents

<a id="suspicious-login-activity-in-auth-logs"></a>
**Q: You spot repeated failed SSH login attempts from an unfamiliar IP in the auth logs, followed by one successful login. What's your immediate response?**
<details><summary>Show answer</summary>
Treat the successful login as a suspected compromise, not a coincidence: isolate the account
(disable it or rotate its key immediately), check what that session actually did (`last`, shell
history, recently modified files, new cron entries or SSH keys), and only restore access after
confirming the entry vector — reacting only to the failed attempts and ignoring the one success is
the common mistake.

Full explanation → [Log Analysis for Security Incidents](../docs/27-linux-for-cybersecurity/log-analysis-for-security-incidents.md)
</details>

## Related topics

- [Beginner Interview Questions](beginner.md)
- [Intermediate Interview Questions](intermediate.md)
- [Senior/Expert Interview Questions](senior.md)
- [Company-Wise Interview Questions](company-wise/devops-roles.md)
- [Interview Questions Hub](index.md)
