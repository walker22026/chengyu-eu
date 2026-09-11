---
title: "Two Quick Wins for This Blog: Caching and Visitor Analytics"
date: 2024-04-28T13:57:08+00:00
summary: "Adding a caching plugin for faster page loads, plus wiring up Google Analytics."
tags: ["Typecho", "Self-Hosting"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/1ce8c47716f26d21d4b40.png"
---

Two small optimisations to the blog today: adding a caching plugin to speed up page loads, and wiring up visitor analytics.

### Enabling the TpCache plugin

1. Go to the [TpCache](https://github.com/phpgao/TpCache) GitHub repo.
2. Download the plugin: `https://github.com/phpgao/TpCache/archive/master.zip`
3. Drop it into the site's `/usr/plugins/` directory.
4. Unzip it and rename the folder to `TpCache`.
5. In the site admin panel, go to **Plugins**, find TpCache, and enable it.
   - If `Memcache`/`Redis` PHP extensions aren't installed, it'll fall back to a MySQL-backed driver.
6. Once it's configured, force-refresh the homepage to see the effect.

![Enabling the TpCache plugin](https://images.chengyu.eu/file/1ce8c47716f26d21d4b40.png)

If you don't want to bother setting up Redis, that's as far as you need to go.

### Adding Google Analytics

- Open [Google Analytics](https://analytics.google.com/) and create a new account in settings.
- Once created, go into the tracking-code configuration and add it manually.
- Copy the tracking snippet, then find the `header.php` file in the current Typecho theme.
- Paste the snippet in anywhere sensible — you can verify it's working from the Google Analytics dashboard.

![Google Analytics account setup](https://images.chengyu.eu/file/2c1e00757f34655bd2190.png)

![Adding the tracking snippet](https://images.chengyu.eu/file/0b5778797a68b1da23d21.png)

![Locating header.php in the theme](https://images.chengyu.eu/file/fc3cb9ec6bb6db74d5f5a.png)

![Pasting the snippet into place](https://images.chengyu.eu/file/cc47b4cf12ddb1ffb7de1.png)
