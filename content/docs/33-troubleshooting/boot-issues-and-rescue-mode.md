---
title: "Boot Issues and Rescue Mode"
description: "How a Linux boot sequence actually proceeds, what commonly breaks it, and how to use GRUB, single-user mode, and a rescue/live environment to recover a system that won't boot."
module: "33-troubleshooting"
moduleTitle: "Troubleshooting"
stage: "production-engineer"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["02-installing-linux/post-install-checklist"]
relatedTopics: ["33-troubleshooting/service-wont-start", "33-troubleshooting/disk-full-issues"]
relatedCommands: []
careerRelevance: ["linux-administrator", "platform"]
relatedLabs: []
relatedInterviewQuestions: []
relatedCheatsheet: "linux-commands"
furtherReading: [{"label": "GRUB manual (GNU Project)", "url": "https://www.gnu.org/software/grub/manual/grub/grub.html"}]
nextTopic: "34-final-revision/full-roadmap-recap"
prevTopic: "33-troubleshooting/ssh-connection-issues"
estimatedReadingTime: 9
updatedAt: "2026-07-30"
keywords: ["linux wont boot", "grub rescue mode", "single user mode linux", "fix broken boot linux", "emergency mode systemd"]
canonicalUrl: "/docs/troubleshooting/boot-issues-and-rescue-mode"
---

# Boot Issues and Rescue Mode

🔴 Expert · Relevant for: Linux Administrator · Platform

> **TL;DR:** Know the boot sequence (firmware → bootloader → kernel → initramfs → init/systemd)
> well enough to tell which stage failed. GRUB's rescue prompt and systemd's emergency/rescue
> targets get you a shell even when the normal boot path is broken.

## What is it?

An understanding of the Linux boot sequence in enough detail to diagnose where it's breaking, plus
the recovery tools for each stage: GRUB's boot menu and rescue prompt, systemd's rescue and
emergency targets, and booting from external rescue/live media as a last resort.

## Why does it exist?

Every other topic in this module assumes the system boots and you can log in. Boot failures are
categorically different: there's no running shell to diagnose from yet, so the diagnostic tools
are different — GRUB's own prompt, kernel boot parameters, and rescue targets that start the
system with the bare minimum needed to get a shell, rather than the full normal boot.

## Where is it used?

A server that hangs, drops to an unexpected prompt, or fails entirely on boot — after a kernel
update, a bad `/etc/fstab` entry, a corrupted filesystem, or a misconfigured bootloader.

## How it works

> 📊 Diagram: a linear sequence — "Firmware (BIOS/UEFI)" → "Bootloader (GRUB)" → "Kernel loads" →
> "initramfs (early userspace, mounts real root)" → "init/systemd takes over" → "targets/services
> start" — with a note at each stage of what typically breaks there and the corresponding recovery
> tool (GRUB menu edit, kernel parameter, rescue/emergency target, live/rescue media).

1. **Firmware (BIOS/UEFI)** hands off to the bootloader. Rarely the cause of a Linux-specific boot
   problem, but a wrong boot device order can look like one.
2. **GRUB (the bootloader)** loads the kernel and initial RAM filesystem. If GRUB's own config is
   broken, you'll see a `grub rescue>` prompt instead of a menu — recoverable by manually
   specifying the boot partition and kernel from that prompt.
3. **The kernel loads and hands off to `initramfs`**, a minimal early userspace whose job is
   mounting the real root filesystem. A corrupted filesystem or a wrong entry in `/etc/fstab` can
   fail here, often dropping to an `(initramfs)` shell.
4. **`init`/systemd takes over** once the real root is mounted, and starts services according to
   the default target. A boot that gets this far but then hangs or drops to `emergency.target` or
   `rescue.target` usually points to a specific failing service or a bad fstab entry blocking
   normal startup — systemd deliberately drops to a minimal shell rather than continuing to boot
   into a broken state.
5. **If none of the above gets you a shell**, boot from external rescue/live media, mount the
   installed system's root filesystem manually, and fix the problem from there (edit `/etc/fstab`,
   reinstall a bootloader with `grub-install` run in a `chroot`, or restore a corrupted file).

## Real-world example

A server fails to boot after an engineer edits `/etc/fstab` to add a new mount and makes a typo in
the device path. The boot process gets through GRUB and the kernel fine, but systemd can't mount
the referenced filesystem and drops into `emergency.target` — a minimal shell with the root
filesystem mounted read-only. Recognizing this as a fstab problem (rather than something deeper)
means the fix is: remount root read-write, correct the typo in `/etc/fstab`, and reboot — not a
full reinstall or restore from backup.

## Commands

No canonical command reference pages exist yet for `grub-install`, `fsck`, or `dmesg` — shown
illustratively below, the same treatment given to Ansible/Terraform in
[Module 26](../26-linux-for-devops/index.md).

## Production example

```
# At the GRUB rescue prompt, after a broken GRUB config:
grub rescue> ls
(hd0) (hd0,gpt2) (hd0,gpt1)
grub rescue> ls (hd0,gpt2)/
boot/  etc/  home/ ...        <- this is the root partition
grub rescue> set root=(hd0,gpt2)
grub rescue> set prefix=(hd0,gpt2)/boot/grub
grub rescue> insmod normal
grub rescue> normal
```

```
# Dropped into emergency mode after a bad /etc/fstab entry:
Give root password for maintenance
(or press Control-D to continue):

# mount -o remount,rw /
# nano /etc/fstab
# (fix the typo'd entry, save)
# systemctl reboot
```

## Do / Don't

| Do | Don't |
|---|---|
| Identify which boot stage failed before attempting a fix | Reach for a full reinstall/restore before diagnosing the actual stage |
| Use GRUB's rescue prompt to manually point at the correct root partition | Assume a `grub rescue>` prompt means the data is lost |
| Remount root read-write before editing files in emergency mode | Try to edit files while root is still mounted read-only |
| Keep external rescue/live media accessible for worst-case recovery | Assume you'll always have a working in-OS shell to fix a boot problem |

## Common mistakes

- Assuming a `grub rescue>` prompt or emergency-mode shell means data loss, when it's usually a
  config problem fully recoverable from that same prompt.
- Forgetting that emergency mode mounts root **read-only** — file edits silently fail (or the
  editor refuses to save) until you remount read-write.
- Not keeping rescue/live media (or your cloud provider's equivalent recovery mode) available and
  tested before you actually need it under pressure.
- Editing `/etc/fstab` without testing (`mount -a` catches most typos before a reboot forces the
  issue).

## Best practices

- Test `/etc/fstab` changes with `sudo mount -a` before rebooting — it surfaces most typos
  immediately instead of at the next boot.
- Know your specific environment's rescue/recovery path before an incident — a cloud provider's
  console-based rescue mode differs from physical rescue media, and the time to learn which is at
  hand is not during an outage.
- Keep a known-good previous kernel available in the GRUB boot menu after updates, so a bad kernel
  update has an immediate fallback.

## Exercises

1. Identify, from memory, the five stages of the boot sequence covered on this page in order.
2. Explain in one sentence why emergency mode mounts the root filesystem read-only.
3. Write the single command that tests `/etc/fstab` for mount errors without requiring a reboot.

## Quiz

**Q: A server drops into `emergency.target` during boot. What is the most common cause, and what's the first thing to check?**
<details><summary>Show answer</summary>
A bad or typo'd entry in `/etc/fstab` is the most common cause — check `/etc/fstab` first, after
remounting root read-write so you can actually edit it.
</details>

**Q: Why test `/etc/fstab` changes with `mount -a` before rebooting?**
<details><summary>Show answer</summary>
Because `mount -a` surfaces most fstab typos and errors immediately, in a session where you can
still fix them easily — waiting until the next reboot to discover the same error means diagnosing
it from a much more constrained rescue/emergency shell instead.
</details>

## Interview questions

No interview question bank entries cover boot/rescue-mode scenarios yet — this is a known gap,
noted on the [module index](index.md).

## Key Takeaways

- The boot sequence proceeds firmware → GRUB → kernel → initramfs → init/systemd, and each stage
  has its own typical failure mode and recovery tool.
- A `grub rescue>` prompt is usually a fixable config problem, not data loss.
- Emergency/rescue mode mounts root read-only by default — remount read-write before editing.
- Test `/etc/fstab` changes with `mount -a` before rebooting, not after.

## Related topics

- [Service Won't Start](service-wont-start.md)
- [Disk Full Issues](disk-full-issues.md)
- [Installing Linux](../02-installing-linux/index.md)
