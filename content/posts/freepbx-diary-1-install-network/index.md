---
title: "FreePBX Deployment Diary #1: Install and Network Setup"
date: 2026-01-18T00:01:12+00:00
summary: "First contact with FreePBX 17 on Debian — a Vultr trial instance, the official one-shot install script, and the firewall gotcha that can lock you out of your own server."
tags: ["Self-Hosting", "FreePBX", "Telecom"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/01/17/Pasted-image-20260117195938.jpg"
---

A couple of days ago, while helping the business side test an outbound-calling platform migration, I got my first hands-on exposure to FreePBX. Even though this is more of a data era than a voice one, as someone who works in telecom I found this kind of voice-trunking system genuinely interesting. Since it was the weekend, I figured I'd deploy a full instance myself and write down the process, as a reference for next time.

This post mainly covers **installing and doing the basic setup of FreePBX 17 on Debian** — it doesn't get into actual call routing, trunks, or dialing policy yet.

### 1. Picking the environment

**Cloud host**

A lot of older tutorials recommend Vultr's one-click FreePBX image, but that option has since been discontinued — now you install the OS yourself and deploy manually.

Vultr still offers a new-user trial credit (roughly $250), which is plenty for learning and testing. The credit is valid for 30 days, so there's no need to be stingy with resources — just use it up.

For this deployment I went with:

- **Provider**: Vultr
- **Instance**: 2 vCPU / 4 GB RAM
- **Disk**: 50 GB (more than enough for learning and testing)
- **Region**: Osaka, Japan
- **OS**: Debian (FreePBX 17's officially recommended choice)

![Choosing Debian when spinning up the instance](https://media.chengyu.eu/images/2026/01/17/Pasted-image-20260117195938.jpg)

**A note on the OS**

Starting with **FreePBX 17**, the project has made clear that it's no longer based on CentOS — it now runs exclusively on **Debian Linux**, distributed as a Debian install script package. So when creating the cloud instance, just pick Debian directly — no need to agonize over which OS track to take.

### 2. Basic system prep

After logging into the server, update the system first:

```
apt update && apt upgrade -y
```

If a **PAM configuration prompt** comes up (asking whether to overwrite `/etc/pam.d/common-*`), just choose **Yes** and go with the system defaults.

![The PAM configuration prompt during upgrade](https://media.chengyu.eu/images/2026/01/17/Pasted-image-20260117195841.jpg)

Then install a few common tools:

```
apt install -y curl wget sudo gnupg2
```

### 3. Running the FreePBX 17 install script

Move into a temp directory and download the install script:

```
cd /tmp
wget https://github.com/FreePBX/sng_freepbx_debian_install/raw/master/sng_freepbx_debian_install.sh
chmod +x sng_freepbx_debian_install.sh
./sng_freepbx_debian_install.sh
```

A couple of things to note: the install **takes a while** (anywhere from ten-odd minutes to half an hour), and it doesn't need any manual input along the way — just let it run.

![The install script finishing up](https://media.chengyu.eu/images/2026/01/17/2026-01-17-8.30.51.jpg)

Once it's done, Asterisk and the FreePBX web service start automatically.

### 4. Web interface setup

Visit the server's IP address in a browser to reach the FreePBX setup screen.

**System activation**

The first time in, you'll see a "Welcome to your new FreePBX Server" prompt. Choose **Activate**.

![The FreePBX initial setup screen](https://media.chengyu.eu/images/2026/01/17/2026-01-17-8.34.49.jpg)

Activation requires registering a Portal Account — my first attempt failed, and it turned out I needed to fill in the optional fields too before it would go through.

![The Portal Account registration form](https://media.chengyu.eu/images/2026/01/17/Pasted-image-20260117233023.jpg)

### 5. Firewall configuration (the important part)

FreePBX ships with its firewall and intrusion detection enabled by default, and if you're not careful with the configuration, **it's very easy to lock yourself out**.

**If you get locked out**

Restart the server twice in a row within 5 minutes, and the system will automatically disable the firewall for about 5 minutes — use that window to fix your configuration.

**Recommended initial setup**

Under **Firewall → Networks**, and under **Interfaces**, confirm the default zone is set to Internet (Default Firewall). Early on, I wouldn't recommend getting too granular with the rules — just make sure you can reliably access and manage the box first.

![Firewall network zone configuration](https://media.chengyu.eu/images/2026/01/17/2026-01-17-10.20.33.jpg)
![Firewall interface configuration](https://media.chengyu.eu/images/2026/01/17/2026-01-17-10.20.45.jpg)

### 6. Updating the system and modules

Once the web setup is done, it's worth updating everything right away.

**System update:**

```
apt update && apt upgrade
```

**FreePBX module update:**

```
fwconsole
fwconsole ma upgradeall
fwconsole restart
```

![Running the module updates](https://media.chengyu.eu/images/2026/01/17/2026-01-17-11.01.57.jpg)

After that, the system is in a relatively clean, usable baseline state.

### Wrap-up

This round of deployment covered: picking a VPS and installing Debian, installing and deploying FreePBX 17 on top of it, and turning on the firewall for basic network protection.

Going to sleep now — next up, building on this: setting up HTTPS, and getting a client connected to actually place a call.

This post is a record of the install and initial setup; I'll keep writing up the rest as the deployment progresses.
