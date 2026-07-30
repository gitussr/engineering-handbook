---
title: "Command Reference"
description: "Every Linux command documented in this site, one canonical page each, cross-linked from every module that uses it."
type: "hub"
updatedAt: "2026-07-29"
canonicalUrl: "/commands"
---

# Command Reference

One canonical page per command — full Purpose, Syntax, Arguments, Options, Examples, Expected
Output, Exit Status, Common Errors, Security/Performance Considerations, Production Usage, and
Related Commands/Concepts for each. Commands are documented here once and linked from every
module and topic page that uses them, rather than re-explained per module (see
[architecture/02-folder-structure.md](../../architecture/02-folder-structure.md)).

This reference grows as each command's owning module is reached in the roadmap — it is not
generated all at once up front. A command is added here the first time a module needs to teach it
in full.

## Commands documented so far

| Command | Purpose | Introduced in |
|---|---|---|
| [`pwd`](pwd.md) | Print working directory | [Module 03: Terminal](../docs/03-terminal/index.md) |
| [`ls`](ls.md) | List directory contents | [Module 03: Terminal](../docs/03-terminal/index.md) |
| [`cd`](cd.md) | Change directory | [Module 03: Terminal](../docs/03-terminal/index.md) |
| [`man`](man.md) | Display manual pages | [Module 03: Terminal](../docs/03-terminal/index.md) |
| [`export`](export.md) | Mark a variable for export to child processes | [Module 04: Bash](../docs/04-bash/index.md) |
| [`alias`](alias.md) | Create a command shortcut | [Module 04: Bash](../docs/04-bash/index.md) |
| [`jobs`](jobs.md) | List background/stopped jobs | [Module 04: Bash](../docs/04-bash/index.md) |
| [`history`](history.md) | List and manage command history | [Module 04: Bash](../docs/04-bash/index.md) |
| [`mount`](mount.md) | Attach a filesystem | [Module 05: File System](../docs/05-file-system/index.md) |
| [`umount`](umount.md) | Detach a filesystem | [Module 05: File System](../docs/05-file-system/index.md) |
| [`stat`](stat.md) | Display file status and inode metadata | [Module 05: File System](../docs/05-file-system/index.md) |
| [`ln`](ln.md) | Create hard/symbolic links | [Module 05: File System](../docs/05-file-system/index.md) |
| [`touch`](touch.md) | Create a file or update its timestamp | [Module 06: Files](../docs/06-files/index.md) |
| [`cp`](cp.md) | Copy files and directories | [Module 06: Files](../docs/06-files/index.md) |
| [`mv`](mv.md) | Move or rename files | [Module 06: Files](../docs/06-files/index.md) |
| [`rm`](rm.md) | Remove files and directories | [Module 06: Files](../docs/06-files/index.md) |
| [`cat`](cat.md) | Concatenate and display files | [Module 06: Files](../docs/06-files/index.md) |
| [`find`](find.md) | Search for files by criteria | [Module 06: Files](../docs/06-files/index.md) |
| [`tar`](tar.md) | Archive and compress files | [Module 06: Files](../docs/06-files/index.md) |
| [`grep`](grep.md) | Search text by pattern | [Module 06: Files](../docs/06-files/index.md) |
| [`chmod`](chmod.md) | Change file permissions | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`chown`](chown.md) | Change file owner and group | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`chgrp`](chgrp.md) | Change file group | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`umask`](umask.md) | View or set the default permission mask | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`setfacl`](setfacl.md) | Set file access control lists | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`getfacl`](getfacl.md) | Display file access control lists | [Module 07: Permissions](../docs/07-permissions/index.md) |
| [`useradd`](useradd.md) | Create a new user account | [Module 08: Users](../docs/08-users/index.md) |
| [`usermod`](usermod.md) | Modify an existing user account | [Module 08: Users](../docs/08-users/index.md) |
| [`userdel`](userdel.md) | Delete a user account | [Module 08: Users](../docs/08-users/index.md) |
| [`passwd`](passwd.md) | Change a user's password | [Module 08: Users](../docs/08-users/index.md) |
| [`su`](su.md) | Switch user | [Module 08: Users](../docs/08-users/index.md) |
| [`sudo`](sudo.md) | Execute a command as another user | [Module 08: Users](../docs/08-users/index.md) |
| [`groupadd`](groupadd.md) | Create a new group | [Module 09: Groups](../docs/09-groups/index.md) |
| [`groupmod`](groupmod.md) | Modify an existing group | [Module 09: Groups](../docs/09-groups/index.md) |
| [`groupdel`](groupdel.md) | Delete a group | [Module 09: Groups](../docs/09-groups/index.md) |
| [`groups`](groups.md) | List a user's group memberships | [Module 09: Groups](../docs/09-groups/index.md) |
| [`ps`](ps.md) | Report a snapshot of current processes | [Module 10: Processes](../docs/10-processes/index.md) |
| [`top`](top.md) | Display live process activity | [Module 10: Processes](../docs/10-processes/index.md) |
| [`kill`](kill.md) | Send a signal to a process | [Module 10: Processes](../docs/10-processes/index.md) |
| [`killall`](killall.md) | Signal processes by exact name | [Module 10: Processes](../docs/10-processes/index.md) |
| [`pkill`](pkill.md) | Signal processes by name pattern | [Module 10: Processes](../docs/10-processes/index.md) |
| [`nice`](nice.md) | Run a command with a modified priority | [Module 10: Processes](../docs/10-processes/index.md) |
| [`renice`](renice.md) | Change priority of a running process | [Module 10: Processes](../docs/10-processes/index.md) |
| [`systemctl`](systemctl.md) | Control the systemd system and service manager | [Module 11: Services](../docs/11-services/index.md) |
| [`journalctl`](journalctl.md) | Query the systemd journal | [Module 11: Services](../docs/11-services/index.md) |
| [`ip`](ip.md) | Show/configure addresses, routes, and links | [Module 12: Networking](../docs/12-networking/index.md) |
| [`ping`](ping.md) | Test network reachability | [Module 12: Networking](../docs/12-networking/index.md) |
| [`traceroute`](traceroute.md) | Show the hop-by-hop path to a host | [Module 12: Networking](../docs/12-networking/index.md) |
| [`ss`](ss.md) | Investigate sockets | [Module 12: Networking](../docs/12-networking/index.md) |
| [`curl`](curl.md) | Transfer data to/from a URL | [Module 12: Networking](../docs/12-networking/index.md) |
| [`wget`](wget.md) | Download files non-interactively | [Module 12: Networking](../docs/12-networking/index.md) |
| [`dig`](dig.md) | Query DNS directly | [Module 12: Networking](../docs/12-networking/index.md) |
| [`nslookup`](nslookup.md) | Query DNS (legacy) | [Module 12: Networking](../docs/12-networking/index.md) |
| [`iptables`](iptables.md) | Configure kernel packet-filtering rules | [Module 12: Networking](../docs/12-networking/index.md) |
| [`firewall-cmd`](firewall-cmd.md) | Manage firewalld zones and rules | [Module 12: Networking](../docs/12-networking/index.md) |
| [`ufw`](ufw.md) | Uncomplicated Firewall front end | [Module 12: Networking](../docs/12-networking/index.md) |
| [`ssh`](ssh.md) | Log into or run commands on a remote host | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`ssh-keygen`](ssh-keygen.md) | Generate an SSH key pair | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`ssh-copy-id`](ssh-copy-id.md) | Install a public key on a remote server | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`scp`](scp.md) | Copy files over SSH | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`sftp`](sftp.md) | Interactive file transfer over SSH | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`rsync`](rsync.md) | Synchronize files efficiently | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`ssh-agent`](ssh-agent.md) | Hold decrypted keys in memory for a session | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`ssh-add`](ssh-add.md) | Load a private key into a running agent | [Module 13: SSH](../docs/13-ssh/index.md) |
| [`apt`](apt.md) | Debian/Ubuntu package manager | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`dpkg`](dpkg.md) | Install and query .deb package files directly | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`dnf`](dnf.md) | RHEL-family package manager | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`rpm`](rpm.md) | Install and query .rpm package files directly | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`snap`](snap.md) | Install universal, sandboxed packages | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`flatpak`](flatpak.md) | Install universal, sandboxed packages (Flathub) | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`make`](make.md) | Build software from a Makefile | [Module 14: Package Managers](../docs/14-package-managers/index.md) |
| [`lsblk`](lsblk.md) | List block devices | [Module 15: Storage](../docs/15-storage/index.md) |
| [`fdisk`](fdisk.md) | Create or modify a partition table | [Module 15: Storage](../docs/15-storage/index.md) |
| [`parted`](parted.md) | GPT-native partition editor | [Module 15: Storage](../docs/15-storage/index.md) |
| [`mkfs`](mkfs.md) | Create a filesystem on a partition | [Module 15: Storage](../docs/15-storage/index.md) |
| [`df`](df.md) | Report filesystem disk space usage | [Module 15: Storage](../docs/15-storage/index.md) |
| [`du`](du.md) | Estimate file and directory space usage | [Module 15: Storage](../docs/15-storage/index.md) |
| [`logrotate`](logrotate.md) | Rotate, compress, and prune log files | [Module 16: Logs](../docs/16-logs/index.md) |
| [`crontab`](crontab.md) | Manage a user's cron jobs | [Module 17: Cron](../docs/17-cron/index.md) |
| [`at`](at.md) | Schedule a one-time command | [Module 17: Cron](../docs/17-cron/index.md) |
| [`anacron`](anacron.md) | Run missed recurring jobs after downtime | [Module 17: Cron](../docs/17-cron/index.md) |
| [`set`](set.md) | Configure shell/script behavior options (-e, -u, -x, pipefail) | [Module 18: Shell Scripting](../docs/18-shell-scripting/index.md) |
| [`shellcheck`](shellcheck.md) | Static analysis for shell scripts | [Module 18: Shell Scripting](../docs/18-shell-scripting/index.md) |
| [`auditd`](auditd.md) | The Linux Audit daemon (managed via `auditctl`) | [Module 19: Security](../docs/19-security/index.md) |
| [`ausearch`](ausearch.md) | Query the Linux Audit trail | [Module 19: Security](../docs/19-security/index.md) |
| [`vmstat`](vmstat.md) | Report virtual memory, CPU, and swap statistics | [Module 20: Monitoring](../docs/20-monitoring/index.md) |
| [`iostat`](iostat.md) | Report per-device disk I/O statistics | [Module 20: Monitoring](../docs/20-monitoring/index.md) |
| [`mpstat`](mpstat.md) | Report per-processor CPU statistics | [Module 21: Performance](../docs/21-performance/index.md) |
| [`free`](free.md) | Display memory usage | [Module 21: Performance](../docs/21-performance/index.md) |
| [`iotop`](iotop.md) | Display per-process disk I/O usage | [Module 21: Performance](../docs/21-performance/index.md) |
| [`iperf`](iperf.md) | Measure network throughput | [Module 21: Performance](../docs/21-performance/index.md) |
| [`sar`](sar.md) | Report historical system activity | [Module 21: Performance](../docs/21-performance/index.md) |
| [`sysctl`](sysctl.md) | Read and change kernel runtime parameters | [Module 21: Performance](../docs/21-performance/index.md) |
| [`docker`](docker.md) | Build, run, and manage containers | [Module 23: Docker Basics](../docs/23-docker-basics/index.md) |
| [`kubectl`](kubectl.md) | Control a Kubernetes cluster | [Module 24: Kubernetes Basics](../docs/24-kubernetes-basics/index.md) |
| [`cloud-init`](cloud-init.md) | Inspect and debug a cloud instance's first-boot configuration | [Module 25: Linux for Cloud](../docs/25-linux-for-cloud/index.md) |
| [`nmap`](nmap.md) | Network discovery and port scanner | [Module 27: Linux for Cybersecurity](../docs/27-linux-for-cybersecurity/index.md) |
| [`tcpdump`](tcpdump.md) | Capture and inspect network traffic | [Module 27: Linux for Cybersecurity](../docs/27-linux-for-cybersecurity/index.md) |
| [`tshark`](tshark.md) | Wireshark's command-line packet analyzer | [Module 27: Linux for Cybersecurity](../docs/27-linux-for-cybersecurity/index.md) |

## Commands referenced but not yet fully documented

Several earlier modules reference commands illustratively (`uname`,
`hostnamectl`, `sha256sum`, `tmux`, `bg`, `fg`, `nohup`, `env`, `echo`, `source`,
`which`, `date`, `lsof`, `fuser`, `less`, `more`, `head`, `tail`, `nano`, `vim`,
`locate`, `whereis`, `gzip`, `zip`, `diff`, `cmp`, `sed`, `awk`, `cut`, `sort`, `uniq`, `wc`,
`chage`, `visudo`, `getent`, `htop`, `pstree`, `pgrep`, `service`, `systemd-analyze`, `ifconfig`,
`mtr`, `netstat`, `sshd`, `yum`, `add-apt-repository`,
`rpm --checksig`, `dpkg-sig`, `pvcreate`, `vgcreate`, `lvcreate`, `lvextend`, `resize2fs`,
`xfs_growfs`, `mdadm`, `mkswap`, `swapon`, `swapoff`, `blkid`, `dmesg`, `getenforce`,
`setenforce`, `sestatus`, `aa-status`, `aa-enforce`, `aa-complain`, `fail2ban-client`, `lynis`,
`node_exporter`, `fio`, `sysbench`, `containerd`, `ctr`, `crictl`, `runc`, `docker compose`,
`kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`, `kubelet`, `kube-proxy`,
`aws`, `gcloud`, `az`, `s3fs`, `blobfuse`, `packer`, `ansible`, `ansible-playbook`, `terraform`,
`last`, `who`, `oscap`, `nginx`, `apache2ctl`, `apachectl`, `certbot`)
ahead of
their own dedicated module, or are taught in full within a topic page without yet having a
separate canonical command page. Their full canonical pages are written when
that owning module is reached.
`ifconfig`, `mtr`, `netstat`, and `yum` are deliberately never given separate canonical pages —
they're covered as legacy/alternative tools within their owning module's topic pages instead (the
same treatment `htop` received in Module 10; `yum` is covered in `dnf`'s Compatibility Notes).
`sshd` (the SSH server daemon/config side) is covered as directives within Module 13's hardening
topic rather than as a standalone command page, since it's configuration-file-driven rather than
flag-driven. `add-apt-repository`, `rpm --checksig`, and `dpkg-sig` are narrow, situational tools
referenced in Module 14 without dedicated pages. The LVM command family (`pvcreate`, `vgcreate`,
`lvcreate`, `lvextend`, `resize2fs`), RAID's `mdadm`, and the remaining swap commands (`mkswap`,
`swapon`, `swapoff`) are covered in full within their Module 15 topic pages but don't get separate
canonical pages — the roadmap scopes LVM, RAID, and swap as concepts, not named-command bullets.
`free`, also introduced in that same Module 15 swap topic, later got its own canonical page in
Module 21, once a roadmap bullet finally named it explicitly in backticks.
`rsyslog` (Module 16) is a service managed via `systemctl` rather than a standalone command; ELK
and Grafana Loki are external systems, not Linux shell commands. `atq` and `atrm` (Module 17) are
documented as options on the [`at`](at.md) page rather than as separate pages, since they're thin
wrappers around the same job queue `at` creates. systemd timers (Module 17) reuse `systemctl` and
`journalctl`, both already documented, rather than introducing a new command page. SELinux
(`getenforce`, `setenforce`, `sestatus`), AppArmor (`aa-status`, `aa-enforce`, `aa-complain`), and
`fail2ban-client` (Module 19) are covered illustratively within their topic pages rather than
given separate canonical pages, since those roadmap bullets name concepts, not specific commands
in backticks — the same treatment LVM/RAID/swap received in Module 15. `lynis` (Module 19) is
shown illustratively as one example scanning tool; dedicated vulnerability/network scanning tools
(`nmap` and similar) remain deferred to Module 27. [`top`](top.md) (Module 20) was extended with
batch-mode flags (`-b`, `-n`) for scripted/monitoring use rather than duplicated into a new page;
`htop` remains without a canonical page (an existing Module 10 gap — its interactive UI doesn't
suit this documentation's flag-driven format). `node_exporter` (Module 20) is a third-party
monitoring agent binary shown illustratively, not a core Linux command; Prometheus, Grafana,
Nagios, and Zabbix are external platforms, covered conceptually the same way ELK and Grafana Loki
were in Module 16. `fio` and `sysbench` (Module 21) are specialized benchmarking tools shown
illustratively without canonical pages, since "Benchmarking tools" names a concept, not specific
commands in backticks — the same treatment LVM/RAID/swap received in Module 15. `docker`, also
referenced illustratively in Module 22 ahead of its own dedicated module, got its full canonical
page in [Module 23: Docker Basics](../docs/23-docker-basics/index.md) — created during
`docker run, ps, exec, logs` and extended with `build`/`network`/`volume` across that module's
later topics, the same pattern used for `iptables`/`firewall-cmd`/`ufw` in Module 19. `containerd`,
CRI-O, and their low-level CLIs (`ctr`, `crictl`, `runc`) are covered conceptually in Module 22 as
runtime implementations, not as commands, and don't get canonical pages. `docker compose` (Module
23) is likewise covered conceptually — its bullet names a concept, not a command in backticks —
with `up`/`down`/`logs` shown illustratively rather than as a canonical page. [`kubectl`](kubectl.md)
(Module 24) got its own canonical page, created during `kubectl basics` and extended with `apply`,
Namespace scoping, and `create configmap`/`create secret` across that module's later topics — the
same pattern as `docker`. The control-plane and node components it manages (`kube-apiserver`,
`etcd`, `kube-scheduler`, `kube-controller-manager`, `kubelet`, `kube-proxy`) are administered as
part of cluster setup, not everyday interactive commands, and don't get canonical pages — this
documentation teaches cluster interaction through `kubectl` exclusively. This is a
deliberate, documented sequencing
choice, not a gap to fix out of
order — see each module's "Known, intentional gaps" note.
[`cloud-init`](cloud-init.md) (Module 25) got its own canonical page — the only command Module 25's
roadmap bullets name explicitly in backticks. `aws`, `gcloud`, and `az` (Module 25) are shown
illustratively throughout that module but never get canonical pages — they're cloud provider CLIs
for automation, not Linux shell commands, and fall outside this documentation's core Linux command
scope, the same boundary drawn around Terraform/Ansible in Module 26. `s3fs`, `blobfuse`/
`blobfuse2`, and `packer` (Module 25) are likewise shown illustratively without canonical pages,
since their roadmap bullets ("S3FS, blob mounts," "custom Linux images") name concepts, not
specific commands in backticks — the same treatment LVM/RAID received in Module 15. Module 26
(Linux for DevOps) has zero roadmap bullets naming a specific command in backticks, so it produced
no canonical command pages at all — the same treatment as Module 22. `ansible`/`ansible-playbook`
and `terraform` are shown illustratively throughout; both are candidates for a future sibling
section outside the core 34-module roadmap (the same treatment planned for Git in Developer
Essentials) rather than canonical pages folded into Module 26. CI/CD platforms themselves (GitHub
Actions, GitLab CI, Jenkins) are external systems covered conceptually, the same treatment given to
Prometheus/Grafana (Module 20) and ELK/Loki (Module 16). [`nmap`](nmap.md), [`tcpdump`](tcpdump.md),
and [`tshark`](tshark.md) (Module 27) all got canonical pages — the roadmap's `wireshark` backtick
was resolved specifically to `tshark`, Wireshark's actual command-line component, since the
graphical Wireshark application has no meaningful CLI mode of its own; the full reasoning is
documented on
[Module 27's network security tools page](../docs/27-linux-for-cybersecurity/network-security-tools-nmap-tcpdump-wireshark-cli.md).
`last` and `who` (Module 27) are shown illustratively for SOC-analyst login-history checks without
canonical pages. `oscap`/OpenSCAP (Module 27) is shown illustratively for CIS Benchmark scanning
without a canonical page, since that roadmap bullet names a compliance concept, not a specific
command in backticks — the same treatment `lynis` received in Module 19. Module 28 (Linux for Web
Servers) has zero roadmap bullets naming a specific command in backticks, so it produced no
canonical command pages at all — the same treatment as Modules 22 and 26. `nginx`,
`apache2ctl`/`apachectl`, and `certbot` are shown illustratively throughout Module 28's topic
pages without canonical pages as a result.

## FAQ

**Why isn't every command documented yet?**
Commands are written in full when their owning module is reached, so each command page can link
back to the concept page that actually teaches it in context, rather than existing as an
isolated, unlinked reference.

**Why is there only one page per command instead of one per module that uses it?**
So a command's syntax, flags, and gotchas are documented once and stay consistent — every module
that uses a command links to the same canonical page instead of maintaining its own copy that
could drift out of sync.

## Related topics

- [Module 03: Terminal](../docs/03-terminal/index.md)
- [Cheat Sheet: Linux Commands](../cheatsheets/linux-commands.md)
