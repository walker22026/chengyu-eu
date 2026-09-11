---
title: "Forcing HTTPS on My Typecho Blog on Tencent Cloud Lighthouse"
date: 2026-01-28T22:20:13+00:00
summary: "The blog answered on both HTTP and HTTPS at once — tracking down the default_server catching every HTTP request and rewriting it into a clean 301 chain to a single canonical domain."
tags: ["Nginx", "Typecho", "Self-Hosting"]
categories: ["Blog"]
---

### Background

The blog runs on a Tencent Cloud Lighthouse instance, using Typecho. The current state: both the plain HTTP and the HTTPS version of the site were reachable. That's not great for security or SEO, so the goal was to force every HTTP request to redirect to HTTPS, with a single canonical domain.

### Confirming the environment

`ps -ef | grep nginx` confirmed the web server was Nginx, using Lighthouse's bundled install at `/usr/local/lighthouse/softwares/nginx/`.

### Finding the HTTP (port 80) config

Searching with `grep -R "listen 80" -n /usr/local/lighthouse/softwares/nginx/conf` turned up the key file: the HTTP entry point lived in `typecho.conf`, set as the `default_server`, catching every HTTP request as a fallback. Looking at the existing config, it was still running PHP and rewrite rules under plain HTTP — which is exactly why the HTTP version of the site was still reachable at all.

### Confirming HTTPS was already fine (no changes needed there)

HTTPS for the domain was already configured separately, in `mcetf.cn.conf` and `www.mcetf.cn.conf` under the Nginx include directory. Both only listen on 443 SSL, and HTTPS itself was working correctly. The plan: leave the HTTPS config alone entirely, and only touch the HTTP redirect behavior.

### Designing the redirect logic

The target behavior:

- `http://mcetf.cn` → 301 → `https://mcetf.cn`
- `http://www.mcetf.cn` → 301 → `https://mcetf.cn`
- `https://www.mcetf.cn` → 301 → `https://mcetf.cn`
- with `https://mcetf.cn` as the one canonical entry point

### Making the change

Back up the existing config first:

```
cp /usr/local/lighthouse/softwares/nginx/conf/include/typecho.conf \
   /usr/local/lighthouse/softwares/nginx/conf/include/typecho.conf.bak.$(date +%F_%H%M%S)
```

Then edit `typecho.conf` and replace it with:

```
server {
    listen 80;
    server_name www.mcetf.cn;
    return 301 https://mcetf.cn$request_uri;
}

server {
    listen 80 default_server;
    server_name mcetf.cn;
    return 301 https://mcetf.cn$request_uri;
}
```

The `www` host jumps straight to the bare domain in one hop (avoiding a double redirect), the `default_server` catches every other HTTP request as a fallback, and PHP/rewrite no longer runs under plain HTTP at all, which is safer.

Check and reload Nginx:

```
/usr/local/lighthouse/softwares/nginx/sbin/nginx -t
/usr/local/lighthouse/softwares/nginx/sbin/nginx -s reload
```

### Verifying it worked

```
curl -I http://mcetf.cn
```
```
HTTP/1.1 301 Moved Permanently
Location: https://mcetf.cn/
```

```
curl -I http://www.mcetf.cn
```
```
HTTP/1.1 301 Moved Permanently
Location: https://mcetf.cn/
```

```
curl -I https://www.mcetf.cn
```
```
HTTP/1.1 301 Moved Permanently
Location: https://mcetf.cn/
```

Goal achieved: HTTPS everywhere, with a single canonical domain.

### One more thing on the Typecho side

To stop the admin panel or posts from generating `http://` links on their own, it's worth updating the site URL under Typecho's **Settings → General** to `https://mcetf.cn/`.

### Final state

| Request | Result |
| --- | --- |
| `http://mcetf.cn` | 301 → `https://mcetf.cn` |
| `http://www.mcetf.cn` | 301 → `https://mcetf.cn` |
| `https://www.mcetf.cn` | 301 → `https://mcetf.cn` |
| `https://mcetf.cn` | Loads normally |
