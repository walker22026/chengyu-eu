---
title: "FreePBX Deployment Diary #3: The First Successful Test Call"
date: 2026-01-19T00:01:07+00:00
summary: "Creating extensions, getting UDP registration working first, then chasing down a misleading 'wrong password' error that was actually a TLS transport and certificate mismatch."
tags: ["Self-Hosting", "FreePBX", "Telecom"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/18/2026-01-18-8.22.41.jpg"
---

With the basic deployment, security hardening, HTTPS, and backups covered in the first two posts, today's step is where things get real: does this thing actually work for making calls?

> Create a user (extension) → register a client → get an actual call working

Sounds simple, but on this particular combination of Debian 12, FreePBX 17, and OpenSSL 3, the TLS registration process had a few genuinely classic gotchas worth writing down in full.

### 1. Creating extensions

**Quick Create**

```
Applications → Extensions → Quick Create Extension
```

![The Quick Create Extension screen](https://media.chengyu.eu/images/2026/01/18/2026-01-18-8.22.41.jpg)

**Advanced extension settings — stay hands-off for now**

The extension's Advanced page has a long list of NAT, RTP, Contact, Force rport, and similar options. My rule for this stage was simple: don't start tuning parameters right away — get the basic flow working first with the defaults.

**Bulk import, for later**

Bulk Handler exposes Extensions, Users, Groups, and UCP Templates. Using the export template that already had my first extension (200) in it, I had an AI model generate 19 more, imported them, and ended up with a small 20-user call platform. FreePBX is fully capable of managing users at that kind of scale.

![The bulk import template screen](https://media.chengyu.eu/images/2026/01/18/2026-01-18-10.36.06.jpg)

### 2. Testing from a phone: get UDP working first

On the phone, using Linphone with the server address over UDP: registration succeeded, and internal extensions could call each other.

That step matters more than it sounds — it confirms the extension configuration is correct, that the network/NAT/firewall path works, and that Asterisk's core functionality is fine. Whatever goes wrong after this point, the basic environment is no longer the suspect.

### 3. Fixing the TLS registration failure

**Symptom:** switching Linphone to use the domain name with TLS transport produced a "wrong password" error — a genuinely misleading message.

**Fixing the certificate/PJSIP TLS binding.** Two things to check:

**Certificate Manager** — this must be set to the Let's Encrypt certificate, never the default self-signed one. A self-signed certificate will fail 100% of the time on a phone client.

![Certificate Manager set to the Let's Encrypt certificate](https://media.chengyu.eu/images/2026/01/18/2026-01-18-10.29.41.jpg)

**SSL Method** (critically important) — on Debian 12 with OpenSSL 3, the recommended order is Default (automatic negotiation) first, then tlsv1_2. Don't use plain tlsv1 — it's rejected at the system security-policy level.

![The SSL Method setting](https://media.chengyu.eu/images/2026/01/18/2026-01-18-10.30.30.jpg)

**The extension's transport was locked to UDP.** The root cause: the CSV/template extension (200) used UDP, and the new extension (201) inherited that template with its Transport fixed to `0.0.0.0-udp` — so even with TLS enabled server-side, the extension itself simply refused to use it.

**The fix (at the extension level):**

```
Applications → Extensions → edit the extension → Advanced
```

Find **Transport**: if it's set to `0.0.0.0-udp`, change it to **All - PJSIP Transports** (or explicitly `0.0.0.0-tls`). "All" is the one I'd recommend, since it stays compatible with UDP, TCP, and TLS at once.

Then, if needed, apply the change and restart:

1. Submit
2. Apply Config
3. SSH into the server and run:

```
fwconsole restart
```

### Wrap-up: the first successful test call

After waiting about 30 seconds for the service to restart, I opened Linphone, switched transport to TLS on port 5061, and logged in — while watching the log on the server at the same time:

```
tail -f /var/log/asterisk/full
```

TLS registration succeeded, and the first test call genuinely went through.

By the end of this post, FreePBX has gone from "the system runs," to "users can be provisioned," to "a real test call actually works."
