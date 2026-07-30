# Roadmap — All 34 Modules

Legend: 🟢 Must Know · 🟡 Good to Know · 🔴 Expert

Progression: **Beginner → Intermediate → Professional → Production Engineer**, left to right below.
Modules are sequential — each assumes everything before it. Modules 25–28 branch by career track
and can be studied in any order once 01–24 are complete. Modules 29–34 are cross-cutting and
referenced from every module (not a linear "read last" block, though numbered last).

Each sub-topic below becomes one documentation page. Concepts are taught before the commands that
implement them (e.g. "Processes" as a concept precedes `ps`/`top`/`htop`/`kill`).

---

## Stage: Beginner (Modules 01–09)

### 01 Linux Basics — 🟢 module
- What is Linux 🟢
- Linux Distributions overview (Debian/Ubuntu, RHEL/CentOS/Rocky, Arch, SUSE) 🟢
- Kernel vs OS vs Distro 🟢
- GUI vs CLI 🟢
- Linux Philosophy & the Filesystem Hierarchy Standard (brief) 🟢
- Linux in industry: servers, cloud, mobile, embedded 🟢
- Choosing a distro for your career path 🟡

### 02 Installing Linux — 🟢 module
- Virtualization options: VirtualBox, VMware, WSL2 🟢
- Installing Ubuntu/Debian 🟢
- Installing RHEL/CentOS/Rocky 🟡
- Spinning up a cloud VM (AWS EC2, GCP Compute Engine, Azure VM) 🟢
- Dual boot considerations 🟡
- Post-install setup checklist 🟢

### 03 Terminal — 🟢 module
- Terminal vs Shell vs Console 🟢
- Navigating the terminal 🟢
- `pwd`, `ls`, `cd` 🟢
- Command syntax and structure 🟢
- `man`, `--help`, `tldr` 🟢
- Keyboard shortcuts and history 🟢
- Terminal multiplexers: `tmux`, `screen` 🟡
- Customizing the shell prompt 🟡

### 04 Bash — 🟢 module
- Shells overview: `sh`, `bash`, `zsh`, `fish` 🟢
- Environment variables 🟢
- Shell config files: `.bashrc`, `.bash_profile`, `/etc/profile` 🟢
- Aliases and functions 🟢
- The `PATH` variable 🟢
- I/O redirection (`>`, `>>`, `<`, `2>`) 🟢
- Pipes (`|`) 🟢
- Command substitution 🟡
- Job control: `bg`, `fg`, `jobs`, `&`, `nohup` 🟡
- Bash history tricks 🟡

### 05 File System — 🟢 module
- Filesystem Hierarchy Standard (FHS) deep dive 🟢
- `/etc`, `/var`, `/home`, `/usr`, `/bin`, `/opt`, `/tmp`, `/proc`, `/sys` explained 🟢
- Filesystem types: ext4, xfs, btrfs, ntfs 🟡
- Mounting and unmounting 🟢
- `/proc` and `/sys` virtual filesystems 🔴
- inodes explained 🟡
- Symbolic vs hard links 🟢

### 06 Files — 🟢 module
- File types in Linux 🟢
- Create/copy/move/remove: `touch`, `cp`, `mv`, `rm` 🟢
- Viewing files: `cat`, `less`, `more`, `head`, `tail` 🟢
- Editing files: `nano`, `vim` basics 🟢
- Finding files: `find`, `locate`, `which`, `whereis` 🟢
- Compression and archiving: `tar`, `gzip`, `zip` 🟢
- Comparing files: `diff`, `cmp` 🟡
- Text processing: `grep` 🟢, `sed`/`awk` 🟡, `cut`/`sort`/`uniq`/`wc` 🟢
- Regular expressions basics 🟡

### 07 Permissions — 🟢 module
- The permission model: owner / group / other 🟢
- `rwx` explained 🟢
- `chmod` (symbolic and octal) 🟢
- `chown`, `chgrp` 🟢
- `umask` 🟡
- SUID, SGID, sticky bit 🔴
- ACLs: `setfacl`, `getfacl` 🔴
- Common permission errors and how to diagnose them 🟢

### 08 Users — 🟢 module
- User accounts overview 🟢
- `/etc/passwd` and `/etc/shadow` explained 🟢
- `useradd`, `usermod`, `userdel` 🟢
- `passwd` command 🟢
- `su` vs `sudo` 🟢
- The sudoers file and `visudo` 🟢
- Root account best practices 🟡
- User account security hardening 🔴

### 09 Groups — 🟢 module
- Groups overview 🟢
- Primary vs secondary groups 🟢
- `groupadd`, `groupmod`, `groupdel` 🟢
- The `/etc/group` file 🟢
- Adding users to groups: `usermod -aG` 🟢
- Group-based permission strategy 🟡

---

## Stage: Intermediate (Modules 10–18)

### 10 Processes — 🟢 module
- What is a process 🟢
- Process lifecycle and states 🟡
- PID, PPID, and the process tree 🟢
- `ps`, `top`, `htop` 🟢
- `kill`, `killall`, `pkill`, and signals 🟢
- Foreground vs background processes 🟢
- `nice` and `renice` 🟡
- Zombie and orphan processes 🔴
- How systemd manages processes (preview of Module 11) 🟡

### 11 Services — 🟢 module
- What is a service / daemon 🟢
- systemd overview 🟢
- `systemctl`: start, stop, enable, disable, status 🟢
- Writing a systemd unit file 🟡
- `journalctl` basics 🟢
- Service dependencies and targets 🔴
- init systems: SysV vs systemd (brief history, practical relevance only) 🟡

### 12 Networking — 🟢 module
- Networking fundamentals: IP, subnet, gateway, DNS 🟢
- `ip addr`, `ip route` (and legacy `ifconfig`) 🟢
- `ping`, `traceroute`, `mtr` 🟢
- `netstat`, `ss` 🟢
- `curl`, `wget` 🟢
- `/etc/hosts`, `/etc/resolv.conf` 🟢
- Firewalls: `iptables`, `firewalld`, `ufw` 🟡
- DNS troubleshooting: `dig`, `nslookup` 🟡
- Network interfaces and bonding 🔴
- Ports and sockets explained 🟢

### 13 SSH — 🟢 module
- What is SSH and how it works 🟢
- `ssh` command usage 🟢
- SSH key generation and key-based auth 🟢
- `scp`, `sftp`, `rsync` 🟢
- SSH config file: `~/.ssh/config` 🟡
- SSH tunneling and port forwarding 🔴
- SSH hardening: disable root login, key-only auth 🟡
- SSH agent and agent forwarding 🔴

### 14 Package Managers — 🟢 module
- Package management overview 🟢
- `apt` / `dpkg` (Debian/Ubuntu) 🟢
- `yum` / `dnf` / `rpm` (RHEL/CentOS/Rocky) 🟢
- `snap`, `flatpak` 🟡
- Building from source: `make`, `./configure` 🟡
- Repository management 🟡
- Package signing and verification 🔴

### 15 Storage — 🟢 module
- Disks and partitions overview 🟢
- `fdisk`, `parted`, `lsblk` 🟢
- Creating filesystems: `mkfs` 🟢
- Mounting and `/etc/fstab` 🟢
- LVM (Logical Volume Management) 🔴
- RAID basics 🔴
- Disk usage: `df`, `du` 🟢
- Swap space 🟡

### 16 Logs — 🟢 module
- Linux logging overview 🟢
- `/var/log` directory structure 🟢
- `journalctl` deep dive 🟢
- syslog and rsyslog 🟡
- `logrotate` 🟡
- Centralized logging concepts (ELK stack, Grafana Loki) 🔴
- Reading logs during a production incident 🟢

### 17 Cron — 🟡 module
- Task scheduling overview 🟢
- crontab syntax 🟢
- `crontab -e`, `-l`, `-r` 🟢
- System-wide cron: `/etc/cron.d`, `/etc/crontab` 🟡
- `at` and `anacron` 🟡
- systemd timers (modern alternative to cron) 🔴
- Cron troubleshooting and logging 🟡

### 18 Shell Scripting — 🟢 module
- Writing your first script 🟢
- Variables and data types 🟢
- Conditionals: `if`/`else`/`case` 🟢
- Loops: `for`/`while`/`until` 🟢
- Functions 🟢
- Script arguments: `$1`, `$@`, `$#` 🟢
- Exit codes and error handling 🟡
- Debugging scripts: `set -x`, `shellcheck` 🟡
- Real automation scripts: backup, deploy, health-check 🔴

---

## Stage: Professional (Modules 19–24)

### 19 Security — 🟡 module
- Linux security model overview 🟢
- Server hardening checklist 🟢
- Firewall configuration in depth 🟡
- SELinux and AppArmor 🔴
- Fail2ban 🟡
- Auditing: `auditd`, `ausearch` 🔴
- Vulnerability scanning basics 🟡
- Security workflows for SOC/pentest roles 🔴

### 20 Monitoring — 🟡 module
- Monitoring fundamentals: what to watch and why 🟢
- `top`, `htop`, `vmstat`, `iostat` 🟢
- Monitoring stacks overview: Prometheus, Grafana, Nagios, Zabbix 🟡
- Setting up a basic monitoring agent 🔴
- Alerting concepts and thresholds 🟡
- Log-based monitoring 🟡

### 21 Performance — 🔴 module
- Performance tuning overview 🟡
- CPU performance: `mpstat`, `top` 🟡
- Memory performance: `free`, `vmstat` 🟡
- Disk I/O performance: `iostat`, `iotop` 🔴
- Network performance: `iperf`, `sar` 🔴
- Kernel tuning: `sysctl` 🔴
- Benchmarking tools 🔴

### 22 Containers — 🟡 module
- What is containerization 🟢
- Containers vs VMs 🟢
- Namespaces and cgroups explained 🔴
- Container runtimes overview (containerd, CRI-O) 🟡
- Why containers matter for DevOps/Cloud roles 🟢

### 23 Docker Basics — 🟡 module
- Docker architecture 🟢
- Installing Docker 🟢
- Images vs containers 🟢
- Dockerfile basics 🟢
- `docker run`, `ps`, `exec`, `logs` 🟢
- Docker networking basics 🟡
- Docker volumes 🟡
- Docker Compose intro 🟡
- Docker best practices 🔴

### 24 Kubernetes Basics — 🟡 module
- What is Kubernetes and why it exists 🟢
- K8s architecture: control plane and nodes 🟡
- Pods, Deployments, Services 🟡
- `kubectl` basics 🟡
- Writing YAML manifests 🟡
- Namespaces 🟡
- ConfigMaps and Secrets intro 🔴
- Which Linux skills K8s actually depends on 🟡

---

## Stage: Production Engineer (Modules 25–34)

### 25 Linux for Cloud — 🟡 module (career branch)
- Linux on AWS / GCP / Azure — what's actually different 🟢
- `cloud-init` 🟡
- Managing cloud VMs over SSH at scale 🟢
- IAM and instance roles (Linux-relevant parts) 🟡
- Mounting cloud storage: S3FS, blob mounts 🔴
- Auto-scaling and custom Linux images (AMIs) 🔴

### 26 Linux for DevOps — 🟡 module (career branch)
- Linux's role in CI/CD 🟢
- Automation with Bash and Ansible 🟡
- Infrastructure as Code basics: Terraform on Linux 🟡
- The DevOps toolchain on Linux, end to end 🟢
- Building a CI/CD pipeline on Linux servers 🔴

### 27 Linux for Cybersecurity — 🟡 module (career branch)
- Linux for SOC analysts 🟢
- Linux for penetration testing (Kali overview) 🟡
- Log analysis for security incidents 🟡
- Privilege escalation basics (defensive framing) 🔴
- Network security tools: `nmap`, `tcpdump`, `wireshark` (CLI) 🟡
- Hardening for compliance: CIS benchmarks 🔴

### 28 Linux for Web Servers — 🟡 module (career branch)
- Nginx install and config 🟢
- Apache install and config 🟢
- Reverse proxy basics 🟡
- SSL/TLS with Let's Encrypt / certbot 🟡
- Hosting a website end-to-end 🟢
- Deploying PHP/Python/Node apps on Linux 🟡
- Troubleshooting web server errors 🟢

### 29 Linux Interview Questions — cross-cutting
- Beginner question bank 🟢
- Intermediate question bank 🟡
- Senior/expert question bank 🔴
- Scenario-based questions 🟡
- Company-wise question sets 🟡
- Full inventory: [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)

### 30 Linux Cheat Sheets — cross-cutting
- 10 sheets: Commands, Networking, Permissions, Regex, SSH, systemctl, Cron, Package Managers,
  Git Basics, Docker Basics
- Full inventory: [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)

### 31 Practice Projects — cross-cutting
- Beginner projects 🟢
- Intermediate projects 🟡
- Advanced projects 🔴
- Full inventory: [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)

### 32 Real Production Labs — cross-cutting
- Beginner labs 🟢
- Intermediate labs 🟡
- Advanced labs 🔴
- Production incident-simulation labs 🔴
- Full inventory: [08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)

### 33 Troubleshooting — 🟢 module
- Troubleshooting methodology (a repeatable framework) 🟢
- Disk full issues 🟢
- High CPU / memory issues 🟡
- Service won't start 🟢
- Permission denied issues 🟢
- Network connectivity issues 🟡
- SSH connection issues 🟢
- Boot issues and rescue mode 🔴

### 34 Final Revision — cross-cutting
- Full roadmap recap 🟢
- Command reference recap 🟢
- Concept map recap 🟡
- Readiness self-assessment / mock interview 🟡
- What to learn next: Ansible, Terraform, Kubernetes deep dive 🟡

---

## Page count estimate

| Stage | Modules | Approx. topic pages |
|---|---|---|
| Beginner | 01–09 | ~62 |
| Intermediate | 10–18 | ~66 |
| Professional | 19–24 | ~44 |
| Production Engineer | 25–34 | ~46 + cheat sheets/labs/interview banks |
| **Total core topic pages** | **34 modules** | **~218** |

Plus: 10 cheat sheets, ~4 lab tiers × multiple labs each, 12 career pages, 500 interview
questions (grouped into question-bank pages, not 500 separate pages — see
[08-cheatsheets-labs-interview-index.md](08-cheatsheets-labs-interview-index.md)).
