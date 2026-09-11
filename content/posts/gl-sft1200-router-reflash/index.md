---
title: "Reviving a Shelved Router: Reflashing a GL-SFT1200"
date: 2025-06-11T11:00:16+00:00
summary: "A painfully slow GL.iNet Opal, rescued from a drawer with a two-step official firmware upgrade instead of a U-Boot flash."
tags: ["Networking", "Self-Hosting"]
categories: ["Blog"]
---

While cleaning my room recently, I stumbled across a **GL.iNet GL-SFT1200 (Opal)** router I'd shelved a while back — the web interface was painfully slow, and it got even worse after I flashed OpenWrt onto it. On a whim, I decided to try rescuing it through the official web-based upgrade path, and figured I'd write down the process and how it went, in case it's useful to anyone with a similar device.

### Why it got shelved

I originally bought the GL-SFT1200 for portable VPN use and setting up small temporary networks, but in practice:

- The web interface was extremely slow, and switching between admin pages would nearly freeze it.
- Flashing OpenWrt 18.06 onto it made things worse — LuCI pages lagged badly.
- Anything with a slightly more complex plugin (SSR Plus+, Passwall) basically couldn't run smoothly.
- Eventually I just shelved it in a drawer.

### Why give it another shot

- GL.iNet's official and community firmware support has gotten noticeably better since then — the newer OpenWrt 4.x official firmware is clearly more optimized.
- The web interface supports upgrading directly, with a low barrier to entry, so it seemed worth testing whether it would actually fix the performance.
- Why not take the chance to upgrade and see if it comes back to life?

### Getting ready to upgrade

**1. Picking the official web-upgrade path**

I went with GL.iNet's official web-interface upgrade — a friendly process that doesn't require messing with U-Boot mode, so it's fine for everyday users:

- Open the admin panel at `http://192.168.8.1` → System → Firmware Upgrade.
- Upload the official `.tar` package directly through the manual-upload flow.

**2. Things to watch out for during the upgrade**

After trying it out, I found:

- **You can't jump straight to the newest version (4.3.25)** — uploading the new firmware directly just fails.
- You need to **upgrade to an intermediate version first** (I used 4.3.19 as a stepping stone). Once that flashes successfully, run the web upgrade a second time to land on the latest 4.3.25.

This "upgrade twice" approach worked around the firmware compatibility issue and avoided having to fuss with U-Boot flashing — the whole thing went very smoothly.

**3. Results after upgrading**

- It restarted automatically once the upgrade finished.
- The system reset to defaults, and the admin panel's responsiveness noticeably improved.
- The GL.iNet UI and LuCI pages that used to lag are now snappy to switch between and load.

### Performance before and after

| What | Before (OpenWrt 18.06) | After (4.3.25) |
| --- | --- | --- |
| Web interface load time | 10+ seconds, sometimes freezing | 1–2 seconds, responsive |
| Plugin management | Plugins often hung or failed | Plugins load normally |
| Basic Wi-Fi performance | Unstable | More stable, slightly better wall penetration |
| System load | Frequently maxed out | Stays low |

The hardware is still limited compared to a proper software router (x86 or an ARM SBC), but as a portable backup router or a household IoT gateway, it's now completely serviceable.
