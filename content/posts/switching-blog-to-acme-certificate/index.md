---
title: "Switching My Blog to a Free ACME Certificate"
date: 2026-01-21T17:00:07+00:00
summary: "Tired of Tencent Cloud's free certificate silently expiring and needing manual re-binding, I moved my Typecho blog over to acme.sh via a Gitee mirror instead."
tags: ["Self-Hosting", "Nginx", "Typecho"]
categories: ["Blog"]
---

I run a **Typecho** blog on a Tencent Cloud Lighthouse instance, and it's generally been stable day to day. But one problem kept coming back: the **HTTPS certificate kept expiring**. I was using Tencent Cloud's free certificate, which is supposed to auto-renew, but in practice, every time it expired, I had to manually re-issue and re-bind it, or HTTPS would just break. Not only was that a hassle, it never really felt "automatic," and I never quite figured out what was going wrong with the renewal mechanism behind it.

Worse, there was never a clear warning ahead of time — usually I'd only find out once the browser started throwing certificate errors.

### ACME's smooth experience

While recently working on my FreePBX deployment, I ended up using **ACME's free certificate option** (based on Let's Encrypt) for real, and the whole experience can be summed up in one line: configure it once, and you basically never have to think about it again.

So I decided to switch my blog's certificate over too, moving entirely off Tencent Cloud's certificate and onto the ACME approach. Because of some network restrictions in my environment, the usual ACME install methods didn't work directly — certain online install scripts or the default CA connection would fail — so in practice I had to use an alternative approach to request and renew the certificate.

**1. Installing via a Gitee mirror**

Clone the repo from the Gitee mirror:

```
git clone https://gitee.com/neilpang/acme.sh.git
```

Enter the directory and install:

```
cd acme.sh
./acme.sh --install -m info@mcetf.cn
```

Then reload the shell so the command takes effect:

```
source ~/.bashrc
```

**2. Requesting and deploying the certificate**

Once you see "Install success," request the certificate right away:

```
acme.sh --issue -d mcetf.cn -d www.mcetf.cn -w /usr/local/lighthouse/softwares/typecho
```

Then install it into the Nginx config directory:

```
# make sure the directory exists
mkdir -p /usr/local/lighthouse/softwares/nginx/conf/ssl

# install the certificate
acme.sh --install-cert -d mcetf.cn \
--key-file       /usr/local/lighthouse/softwares/nginx/conf/ssl/mcetf.cn.key  \
--fullchain-file /usr/local/lighthouse/softwares/nginx/conf/ssl/mcetf.cn.cer \
--reloadcmd     "/usr/local/lighthouse/softwares/nginx/sbin/nginx -s reload"
```

**3. Finding and updating the Nginx config**

This is the last step, and the most important one — finding where Nginx points to the old certificate and updating it to the new path.

First, find where the config lives:

```
ls /usr/local/lighthouse/softwares/nginx/conf/include/
```

Or search directly for whichever file references the SSL settings:

```
grep -r "ssl_certificate" /usr/local/lighthouse/softwares/nginx/conf/
```

Then edit the config file:

```
vi /usr/local/lighthouse/softwares/nginx/conf/nginx.conf
```

Find the `ssl_certificate` and `ssl_certificate_key` lines and point them at the paths just generated:

```
ssl_certificate /usr/local/lighthouse/softwares/nginx/conf/ssl/mcetf.cn.cer;
ssl_certificate_key /usr/local/lighthouse/softwares/nginx/conf/ssl/mcetf.cn.key;
```

Save and exit (`Esc`, then `:wq`), then reload Nginx:

```
/usr/local/lighthouse/softwares/nginx/sbin/nginx -s reload
```
