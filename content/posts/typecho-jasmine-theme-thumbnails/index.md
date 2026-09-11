---
title: "Patching the Jasmine Theme to Show a Post's First Image as Its Thumbnail"
date: 2024-04-28T17:05:00+00:00
summary: "Obsidian's sync to Typecho doesn't support custom fields over XML-RPC, so I patched the theme instead."
tags: ["Typecho", "Self-Hosting"]
categories: ["Blog"]
---

While auto-syncing from Obsidian to Typecho in the background, I ran into a snag: XML-RPC doesn't support uploading custom fields, so homepage thumbnail images weren't showing up — which, with my particular brand of OCD, was hard to leave alone. I considered patching Typecho's XML-RPC support properly, but in the end went with the lazier, simpler option: modify the theme directly so it automatically uses the first image in a post's body as its homepage thumbnail.

The trade-off is that with more homepage images, page load got a bit slower. It's a small site just for me, though, so I'm not chasing peak performance — as long as it looks right to me, that's good enough.

Here's how I patched the Jasmine theme to show that thumbnail next to the post title on the homepage:

**1. Add a helper function to `functions.php`** that grabs the first image out of the post content — this can go anywhere in the file:

```php
// Get the first image from the post content
function getFirstImageFromContent($content) {
    $output = preg_match_all('/<img.+src="([^"]+)"/i', $content, $matches);
    if ($output && !empty($matches[1])) {
        return $matches[1][0];
    }
    return false;
}
```

**2. In the theme's `component` directory, open `post-item-default.php`** and find:

```php
<?php if ($thumbnail = getThumbnail($this->cid, "")): ?>
```

Replace it with:

```php
<?php if ($thumbnail = getFirstImageFromContent($this->content, "")): ?>
```

Save and refresh.
