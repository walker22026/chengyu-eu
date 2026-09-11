---
title: "FreePBX Deployment Diary #2: HTTPS and Automatic Backups"
date: 2026-01-18T20:00:47+00:00
summary: "Getting Let's Encrypt working on the admin panel and setting up a weekly automatic backup — the two unglamorous steps that make a FreePBX box actually production-ready."
tags: ["Self-Hosting", "FreePBX", "Telecom"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.02.50.jpg"
---

With the basic install and initial setup of FreePBX 17 out of the way, today was about filling in two things any production setup needs: **HTTPS access for the web admin panel**, and an **automatic backup mechanism** for the system and its configuration. Once these two are in place, this FreePBX instance is actually ready to run long-term.

### 1. Setting up HTTPS

**Port management**

Requesting a certificate needs port 80 open, so that has to be sorted first:

```
Settings → System Admin → Port Management
```

I moved the admin port to 8080 and left port 80 free for certificate issuance.

A few things worth checking here: enable **"Responsive Let's Encrypt Rules"**, which allows the ACME validation traffic through during certificate renewal; keep **Intrusion Detection** on; and keep **Safe Mode** on (a self-rescue mechanism for accidental misconfiguration). The overall approach is still the same as before: minimize exposure, and whitelist management IPs.

**Requesting and configuring the certificate**

I used a **Let's Encrypt** certificate, which requires a domain that already resolves to the FreePBX server's public IP, and port 80 reachable from the outside (for ACME validation).

![The Port Management screen](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.02.50.jpg)

In Certificate Management: add a new Let's Encrypt certificate, confirm it shows as available once issued, and set it as the default certificate.

![Certificate Management showing the new Let's Encrypt certificate](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.04.18.jpg)

**HTTPS Setup (the key step)**

On the HTTPS Setup page: pick the certificate just created under Certificate Manager, and click **Install** — the system confirms Apache has been configured and shows the certificate's validity period.

![Installing the certificate through HTTPS Setup](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.07.14.jpg)

On the same page, I left TLSv1.2 and TLSv1.3 enabled without trimming further, prioritizing compatibility, then saved and restarted Apache.

![Confirming TLS versions before saving](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.26.18.jpg)

At that point the web service is ready for HTTPS — once applied and restarted, the admin panel is reachable over HTTPS.

### 2. Setting up automatic backups

With HTTPS done, next came the backup configuration.

**Backup storage location**

```
Admin → Backup & Restore → File Store
```

For now I'm using local storage: path name "Local Backup", actual path `/var/spool/asterisk/backup`. This can be extended to SFTP or object storage later if needed.

![Configuring the local file store](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.28.43.jpg)

**Creating the backup job**

```
Admin → Backup & Restore → Backup
```

I added a new backup job (called "WeekBackup") covering module configuration and the custom config directories (`__ASTETCDIR__` and similar). For notifications, I set an email address to receive backup status updates, for both success and failure. For scheduling: enabled, running weekly, keeping the 3 most recent backups.

![Setting up the scheduled backup job](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.41.18.jpg)

**Verifying it actually worked**

Rather than just trusting that the web panel "looked fine," I checked directly on the server:

```
root@freepbx:/var/spool/asterisk/backup/WeekBackup#
```

That confirmed three things: the scheduled job actually ran, the backup content was genuinely written to disk, and the filenames include version and timestamp info, making it easy to roll back to a specific point later.

![Confirming the backup files on disk](https://media.chengyu.eu/images/2026/01/18/2026-01-18-5.44.26.jpg)

### Wrap-up

The two things done today are both the kind of unglamorous configuration that quietly decides whether a system is actually trustworthy to run: HTTPS secures management access and future scalability, and automatic backups give me a way back from any misconfiguration or failed upgrade.

At this point, this FreePBX setup has a stable baseline environment, reasonable security boundaries, and the ability to recover.
