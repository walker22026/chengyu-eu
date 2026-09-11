---
title: "RemNote's iPhone Flashcard Text Too Small? Fixed It With CSS"
date: 2026-04-14T10:28:54+00:00
summary: "RemNote's iOS app has no font-size setting for flashcards — after confirming custom CSS actually gets through the WebView, a couple of targeted selectors made the card text readable without touching the rest of the UI."
tags: ["Tools"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/13/Pasted-image-202604131613343d6a1361dab4da8b.jpg"
---

After switching to a new iPhone, I opened RemNote to review my Portuguese vocabulary cards and found the text on the flashcards uncomfortably small. Even with glasses on it was a strain, and getting through 280 cards left my eyes tired.

My first instinct was to look for a font setting — I went through every option under Appearance and found nothing. A search of the community turned up complaints about this exact issue going back over two years. RemNote's own official reply was:
> "Currently the only workaround is custom CSS; we hope to add this setting in a future update."

Two years later, the feature still hasn't shipped. So I fixed it myself.

### Step 1: finding the Custom CSS entry point

RemNote → Settings → search "CSS" → Custom CSS → add a blank CSS block.

### Step 2: nuke everything first, to confirm CSS actually works

Most tutorials online were written back in 2020, and the class names have long since changed across versions. Rather than trying outdated selectors one by one, it's better to first confirm CSS injection even works at all, with a wildcard:

```css
* {
  font-size: 24px !important;
}
```

Save it and open the flashcard review screen — if all the text got bigger, CSS injection is working and it's just the selector that's wrong. If nothing changed at all, the iOS app's WebView is blocking CSS entirely, and this path is a dead end.

In my case, **it worked** — the whole interface got bigger, including the top nav bar and the bottom rating buttons.

### Step 3: targeting precisely — only enlarge the card body

Enlarging everything globally breaks the layout. The goal is to only make the card content area bigger, leaving everything else untouched.

Eventually landed on a working combination of selectors:

```css
.spacedRepetition .spacedRepetitionContent,
.spacedRepetition .spaced-repetition__prompt,
.spacedRepetition .rem-text {
  font-size: 20px;
  line-height: 2.0 !important;
}
```

Result: the card body text — like "cardinal number ↔ cardinais, /kɐrdi'najʃ/" — came out large and clear, while the ×😬😄👑 rating buttons at the bottom and the path breadcrumb at the top were completely unaffected.

`line-height: 2.0` is double the font size — Portuguese vocabulary comes with phonetics and example sentences, so a wider line height keeps it from feeling cramped. Adjust anywhere from 1.6 to 2.5 depending on preference.

### Summary

| Method | Effective? |
| --- | --- |
| Adjusting font size directly in Settings | ❌ No such option |
| Following the iOS system font size | ❌ App doesn't respond |
| Switching themes | ❌ Themes only control color |
| Custom CSS wildcard `*` | ✅ Works, but affects everything |
| Custom CSS targeted selectors | ✅ Only affects card body |

If you're also using RemNote to review language flashcards and find the text too small and cramped on your phone, this CSS is ready to use as-is — just adjust the font size to match your own eyesight and screen size.
