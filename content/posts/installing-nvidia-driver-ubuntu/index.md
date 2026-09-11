---
title: "Installing an NVIDIA GPU Driver on Ubuntu 22.04"
date: 2024-07-28T00:00:07+00:00
summary: "Notes on getting an NVIDIA driver and CUDA 12.4 installed cleanly, including disabling the nouveau driver first."
tags: ["Ubuntu", "NVIDIA", "CUDA"]
categories: ["Blog"]
---

### 1. Download the driver and disable the built-in one

- Check the GPU model: `lspci | grep -i nvidia`
- Download the driver from NVIDIA's official site.
- Remove Ubuntu's bundled driver: `sudo apt purge nvidia*`
- Blacklist the `nouveau` driver by editing `/etc/modprobe.d/blacklist.conf`.
- Update the system and reboot.

### 2. Install the graphics driver

- Stop the `lightdm` desktop service.
- Make sure GCC is up to date (12+ is required).
- Run the driver installer with the appropriate flags.
- Verify with `nvidia-smi`.

### 3. Install CUDA

- Followed a CSDN write-up for the exact steps.
- Installed CUDA 12.4 via `apt`.
- Added the CUDA environment variables to `.bashrc`.
- Verified the install with the usual CUDA version commands.
