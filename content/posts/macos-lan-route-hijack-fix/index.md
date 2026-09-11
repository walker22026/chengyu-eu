---
title: "Tech Log: Chasing Down a Hijacked LAN Route on macOS"
date: 2026-02-05T11:00:06+00:00
summary: "A NAS on 192.168.5.x went unreachable with 'No route to host' — the culprit was a leftover feth interface from an ad-blocker's content-filter extension that never fully uninstalled."
tags: ["macOS", "Networking", "Troubleshooting"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/02/05/Pasted-image-20260205104625.jpg"
---

**Date:** February 5, 2026 · **Environment:** macOS Sequoia / Sonoma

**The problem:** couldn't reach a NAS on the local network (`192.168.5.154`). Both `ping` and `traceroute` to that address kept failing with `sendto: No route to host`, even though it was on the same physical network.

### Digging in

**Basic connectivity test.** Running `ping 192.168.5.154` returned `No route to host` — which meant the device wasn't simply off; macOS genuinely didn't know which network interface to send the packet out through.

**Route tracing.** `traceroute -n 192.168.5.154` failed at the very first hop, confirming the traffic was being intercepted or dropped before it even left the local machine's network stack.

**Checking the routing table (the key discovery).** Running `netstat -nr | grep 192.168.5` turned up something odd: that subnet's route pointed to a virtual interface called `feth3656`, instead of the physical network card (`en0`) or an expected network tunnel.

Tracking that down: a `feth` (fake Ethernet) interface is typically created by the "transparent proxy" driver behind an ad-blocking app. Even after that app is turned off in its own UI, its system extension can stay resident at a lower level and keep hijacking traffic for that private subnet.

![The routing table showing the hijacked interface](https://media.chengyu.eu/images/2026/02/05/Pasted-image-20260205104625.jpg)

### Fixing it

**A quick manual fix.** First, delete the hijacked route:

```
sudo route delete -net 192.168.5.0/24
```

Then point that subnet back at the actual active network interface:

```
sudo route add -net 192.168.5.0/24 -interface utunX
```

(the interface name here needs to match your actual setup). This fixed things immediately, but the problem came back after a restart.

**Actually removing the leftover driver.** Go to **System Settings → Network → VPN & Filters**. The ad-blocking app showed as disabled, but its plugin entry was still registered — clicking the "–" button removed it from the system configuration entirely. After removing the leftover plugin, running `sudo route -n flush` cleared out all the dynamically generated bad routes.

**Tuning the network service order.** In network settings, I adjusted the service order so that commonly used virtual network interfaces rank above the physical adapter, to prevent the routing table from conflicting again in the future.

### Root cause

An ad-blocking app I'd installed before hadn't fully uninstalled — its leftover virtual network driver (the `feth` interface) had illegitimately claimed routing for the `192.168.5.0/24` subnet at the system level. Since the driver was in a half-broken state, every packet headed for that subnet ran into a dead end.

**Fix:** used `netstat` to pin down the exact rogue interface, removed the corresponding content-filter extension from System Settings, and after a restart the system went back to assigning correct routes automatically.
