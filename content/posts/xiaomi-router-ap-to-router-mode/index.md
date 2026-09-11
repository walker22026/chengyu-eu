---
title: "Swapping My Parents' ISP Router for a Spare Xiaomi Router (an AP-to-Router-Mode Story)"
date: 2025-06-11T11:00:20+00:00
summary: "A router that stubbornly stayed in AP mode after being moved, and the DHCP debugging it took to switch it back."
tags: ["Networking"]
categories: ["Blog"]
---

My parents had been using a router their ISP handed out for free, which — as it turns out — was really just an edge node for a PCDN storage scheme, and carriers are cracking down on that right now. Rather than risk causing them any trouble, I figured I'd just swap it out. I happened to have a spare Xiaomi router lying around, one I'd previously set up in AP (wireless access point) mode to extend Wi-Fi from the main router. It seemed like a natural fit to bring it to my parents' place and use it as the main router, plugged straight into the fiber modem. What I didn't expect was **forgetting to switch it back to router mode first, which cost me a bit of pointless troubleshooting** — so I figured I'd write the process down for future reference, in case it helps anyone in the same situation.

### The problem

- The Xiaomi router was still configured in AP mode (bridge mode).
- After connecting it to the fiber modem at my parents' place, my phone connected to the Wi-Fi but got an IP in the `192.168.1.x` range (assigned by the modem's own default DHCP).
- I couldn't reach `192.168.31.1`, meaning the Xiaomi router hadn't taken over DHCP and wasn't in "router mode."
- The router's admin panel showed a bare-bones set of options — no dial-up or port-forwarding settings, none of the core router functionality.

### Figuring out what happened

1. **The telltale signs of AP mode.** The Xiaomi router had its own DHCP turned off and was only acting as a wireless extender — which is why my phone picked up a `192.168.1.x` address from the modem instead.
2. **Why did it switch to AP mode in the first place?** The first time a Xiaomi router connects to an upstream network, it checks whether a DHCP service already exists on it. If it detects one (say, the modem's own router function hadn't been disabled), it automatically suggests bridge mode — and once you confirm that, it sticks even after a restart.
3. **How to switch it back to router mode.** Go into the Xiaomi Wi-Fi app or the admin web page and manually switch the "working mode" back to "router mode." That re-enables DHCP, handing control of the home network back to the Xiaomi router.

### The steps

**1. Physical reset (recommended)**

If you're worried about leftover configuration, it's simplest to just reset it: find the reset pinhole on the back of the router, hold it for 5–10 seconds while powered on, and release once the indicator light starts blinking rapidly. The router resets to factory defaults and reboots into the setup wizard.

**2. Setting it to "router mode"**

Connect a phone or computer to the router's default Wi-Fi network (something like `Xiaomi_xxx`), then visit `http://miwifi.com` or `http://192.168.31.1` in a browser and follow the setup wizard: choose **router mode**, choose how it connects to the internet (my parents' setup uses PPPoE dial-up through the fiber modem, so I entered the PPPoE username/password), set a new Wi-Fi name and password, and set an admin password. Once done, the router restarts and hands out `192.168.31.x` addresses — router mode is properly active.

**3. Avoiding an accidental switch back to AP mode**

Check whether the fiber modem's own settings need adjusting (whether its routing function needs to be disabled, or set to bridge mode). If you're not planning to touch the modem's settings, make sure to actively choose "router mode" the first time you set up the Xiaomi router, so it doesn't auto-suggest AP mode instead.

### The result

The Xiaomi router now successfully manages the home network, DHCP works properly handing out `192.168.31.x` addresses, the Wi-Fi signal is noticeably better than the old ISP-freebie router, and going forward it'll be easy to set up port forwarding and other local-network features.
