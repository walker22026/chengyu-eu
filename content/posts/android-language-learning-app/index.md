---
title: "100 Projects Before I Retire, #5: A Language-Learning App Built on Nursery Rhymes and Fairy Tales"
date: 2025-06-17T19:00:08+00:00
summary: "Frustrated by bloated, ad-filled kids' language apps, I built a clean native Android app that loads curated foreign-language rhymes and stories through a WebView."
tags: ["Android", "Kotlin"]
categories: ["Blog"]
---

When I went looking for a decent language-learning tool for my kids, most of what I found was either a bare video player or a bloated "learning app" packed with ads and too complicated for a young child to use. So I decided to build my own — a clean, focused native Android app that loads curated foreign-language nursery rhymes and fairy tales through a WebView, giving kids (and adults learning a language too) an immersive, controlled, ad-free environment to learn in.

### The technical background

The app is built as a **native Android app**, with a stack that includes:

- **Kotlin** — the modern, safe, concise language Android officially recommends.
- **Android Jetpack components**:
  - `androidx.appcompat.app.AppCompatActivity` for compatibility with older system versions
  - `androidx.swiperefreshlayout.widget.SwipeRefreshLayout` for pull-to-refresh on the embedded pages
  - `android.webkit.WebView` as the embedded browser that displays the nursery-rhyme and fairy-tale content

### Why go native instead of cross-platform?

Cross-platform frameworks like Flutter and React Native have been getting a lot of attention lately, but I still went native, mainly because:

- **Better performance** — the WebView starts up fast, and combined with native UI components, the interface responds quickly.
- **Deeper system integration** — down the line I can add background playback, speech recognition, keep-screen-on, eye-comfort mode, and more.
- **More UI customization** — I can heavily tailor content display, safety prompts, and parental controls.

### Content: WebView plus curated foreign-language resources

At its core, the app loads external resources through a WebView — things like kid-friendly foreign-language nursery-rhyme sites, multilingual versions of classic fairy tales, bilingual picture-book pages I've put together or collaborated on, and it can be extended to load local HTML pages or offline bundles.

This approach keeps the flexibility of web-based content management while avoiding the usual "nested webpage plus ads plus no feedback" experience. It makes it easy to pair English nursery rhymes with Chinese subtitles, Portuguese fairy tales with narrated audio, tap-a-word popups for translations and example sentences (doable later via a JSBridge), and offline caching so stories are available on the go.

### Upsides and challenges

**Upsides:** strong performance and smooth animation; compatible across Android versions; a manageable install size; a flexible, easily customizable UI; and it can plug straight into native features like WeChat sharing, parental controls, and speech recognition.

**Challenges:** a WebView still doesn't quite match native components, especially for gestures and load speed; the web content itself needs to be kept under control to prevent unwanted redirects or inappropriate content from slipping in; and the app is still fairly bare — no favorites, no queue management yet — things I'll fill in as I keep using it.
