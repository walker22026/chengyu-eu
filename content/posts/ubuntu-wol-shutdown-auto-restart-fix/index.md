---
title: "Tracking Down Why My Ubuntu Server Kept Auto-Restarting After Shutdown, Post Wake-on-LAN Setup"
date: 2026-04-18T09:00:08+00:00
summary: "A GPU server configured for Wake-on-LAN would shut down cleanly, then reboot itself ~10 seconds later — the culprit was ACPI wakeup left enabled on the network card, letting ordinary LAN broadcast traffic trigger a wake, not just the magic packet."
tags: ["Self-Hosting", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/17/Pasted-image-20260417211314.jpg"
---

**Background**

I have a GPU server at home running Ubuntu (hostname: gpu-nvdia), normally configured with Wake-on-LAN (WOL) — when I need to run a large model, I send a magic packet remotely to wake it, and shut it down via SSH once I'm done.

Recently a strange issue showed up: after running the shutdown command, the machine would power off successfully, but then reboot itself on its own about ten-odd seconds later.

**Troubleshooting**

**Step 1: confirm the shutdown command itself wasn't the issue**

There's a common pitfall with remote SSH shutdowns: when the SSH connection drops, the shutdown process gets interrupted by a SIGHUP signal, leaving the shutdown sequence incomplete. I switched to a method that keeps the shutdown process running regardless of the SSH disconnect. After running it, the machine did shut down cleanly — but it still rebooted itself ten-odd seconds later. So the issue wasn't the shutdown command itself.

**Step 2: check the boot logs and wakeup devices**

The query results showed both of today's shutdowns auto-rebooting within about a minute. The output of `/proc/acpi/wakeup` showed several devices in the `enabled` state, with the network-card-related device looking suspicious.

**Step 3: rule out RTC scheduled wakeup**

AWAC is the ACPI real-time clock — `enabled` could mean a scheduled wakeup is set. The query came back empty, ruling out an RTC scheduled wakeup.

**Step 4: check the suspicious PCI device**

Checking the device showed it was the network card (Realtek Semiconductor Co., Ltd. RTL8111/8168/8411). Checking that card's WOL status showed `Wake-on: g` — there's the root cause.

**Root cause**

The network card had WOL enabled (magic packet mode), and its corresponding ACPI power/wakeup was also `enabled`. With both conditions stacked, once the machine shut down, ordinary network broadcast packets from other devices on the LAN — not just magic packets — could trigger a wakeup. Rebooting ten-odd seconds after shutdown lined up exactly with the LAN broadcast window.

**Fix**

Magic packet wakeup is handled at the network card's firmware level and doesn't depend on ACPI's power/wakeup. So the ACPI-level wakeup permission on the network card can be disabled while keeping magic packet wakeup intact.

Made this persistent across reboots by creating a systemd service.

**Summary**

The key was understanding that when a network card's ACPI power/wakeup is `enabled`, LAN broadcast traffic can trigger a wakeup — whereas magic packet wakeup is handled by the card's firmware and doesn't depend on ACPI wakeup permissions at all.
