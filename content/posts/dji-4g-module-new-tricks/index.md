---
title: "Tinkering Log: Getting New Tricks Out of a DJI 4G Module"
date: 2026-07-10T07:55:28+00:00
summary: "A DJI 4G module identifies itself with a private USB ID that Linux won't drive properly — spoofing it as a generic Quectel EC25 unlocked full modem support, and it ended up running SMS management on an OpenWrt router instead of the NAS."
tags: ["Self-Hosting", "Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/07/09/Pasted-image-20260710074856.jpg"
---

Jumped on the bandwagon recently and bought a DJI 4G module. At first I just wanted to see if I could hook it up to my NAS and use it as an SMS send/receive management tool. After a fair bit of tinkering, it turned out to be genuinely interesting — but definitely not a plug-and-play kind of thing.

### The original goal

My initial goal was simple: hook this DJI 4G module up to one of my own devices to centrally manage SMS sending and receiving — ideally on the NAS, since it's on long-term, easy to manage, and well-suited to running web services.

Later I found people online who had gotten this module to identify itself as a Quectel EC25 and used it with VoHive, managing SMS, eSIM, and device status, even sending notifications through Telegram — so I started looking into whether I should reflash it or modify its device identity.

### To reflash or not to reflash?

At first I was a bit wary of the word "reflash," since it usually involves firmware and NV parameters, which carries real risk. After digging in, though, I confirmed this wasn't reflashing firmware in the traditional sense — it was modifying the USB identification info via AT commands.

This DJI 4G module is essentially a variant of the Quectel EG25-G. Its default USB ID is DJI's private ID: `2ca3:4006`. After modifying it, the system can recognize it as a more generic Quectel EC25: `2c7c:0125`.

Once changed, the Linux kernel can load the `option`, `qmi_wwan`, `cdc_wdm`, and similar drivers fairly naturally, and the device shows up as `/dev/ttyUSB*`, `/dev/cdc-wdm0`, and `wwan0`. This step opened up a lot more possibilities.

### Why it didn't end up on the NAS

I really did want to plug it into the NAS at first — it's stable, quiet, always online, and seemed like the ideal host. But testing showed my NAS could see the USB device itself, but was missing the key kernel modules — `option`, `qmi_wwan`, `cdc_wdm`, `usb_wwan`. In other words, the NAS could tell "a USB device got plugged in," but couldn't recognize it as a usable cellular modem, serial device, or QMI network device.

That's awkward — even running VoHive on the NAS, it wouldn't actually be able to take control of the module.

So the final plan became: **run the module off an OpenWrt router, with VoHive deployed there too; the NAS, if needed at all, would just act as a reverse proxy or access point.**

That actually made more sense. A router is a networking device to begin with, and OpenWrt has more direct support for USB network adapters, QMI, and serial ports.

### Deploying to OpenWrt

Once recognized on OpenWrt, the module's status looked normal: `2c7c:0125`, `/dev/cdc-wdm0`, `/dev/ttyUSB0` through `/dev/ttyUSB3`, `wwan0`.

I then deployed VoHive on OpenWrt. It provides a web management interface showing module status, SIM info, an SMS center, an eSIM page, notification configuration, and more. The final access address was `http://192.168.5.1:7575`. Once deployed, VoHive started up normally and correctly recognized the module.

### What changed after modifying the device identity

After switching the device from DJI's private ID to a Quectel EC25-compatible identity, the biggest change was a clear jump in how well the system could recognize it. Before, it was basically "a module that only serves DJI's own ecosystem"; afterward, it became a cellular module that Linux can manage normally.

What became possible: reading SIM card info; checking signal strength, network status, and registration status; managing SMS send/receive; managing the cellular module via QMI; web-based management paired with VoHive; configuring notifications through Telegram, webhooks, or email; in theory, support for eSIM/eUICC management; and, with a SIM that supports cellular registration, further configuring data connectivity. This step was really the key turning point of the whole project.

### The use case I've got working now

What I currently have working: **a SIM card that can register normally on the cellular network, plugged into the DJI 4G module, with centralized SMS management handled through VoHive running on OpenWrt.**

I can now view SMS messages in the web interface, and I've also set up a Telegram bot so that incoming texts get pushed straight to Telegram. This means verification codes, notification texts, and messages to backup numbers no longer need some phone kept permanently powered on with a SIM in it. The module sits in the router, VoHive handles management, Telegram handles alerts — and overall it's turned out to be a genuinely convenient setup.
