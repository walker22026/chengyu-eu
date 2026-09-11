---
title: "100 Projects Before I Retire, #6: A Real-Time Refreshing Asset Dashboard"
date: 2025-07-09T10:00:11+00:00
summary: "A small web app for tracking multi-currency assets, with live exchange rates, category and currency breakdown charts, and inline editing."
tags: ["Web Dev", "Finance Tools"]
categories: ["Blog"]
---

Getting a clear picture of how your assets are made up and distributed is the first step in managing your finances. This time I built a web app meant to give you a **real-time, auto-refreshing asset dashboard** — one that shows an overview at a glance while staying easy to maintain and update.

This page is the sixth project in my "100 practical websites and apps" series, built around one core goal: **asset visualization, live exchange rates, and flexible editing**.

### The main features

**1. An asset overview across multiple currencies.** The system supports entering assets in different currencies, automatically pulls the exchange rate in the background, and converts everything into both USD and RMB totals, so it's easy to see the overall picture however you want to look at it.

**2. Charts showing asset breakdown.** There are two ways to visualize the structure of your assets: by category (stocks, funds, cash, bonds, and so on) and by currency, which helps spot currency-exposure risk.

**3. Editable asset entries.** Asset tracking can't just be a static display — it needs to flex. This page lets you adjust existing amounts, add new asset categories, or remove categories you no longer hold. Clicking "Edit Assets" on the homepage takes you into an editing view where you can make changes in real time.

**4. Live exchange rates, cached for speed.** For calculating and displaying totals, the app pulls in three key exchange rates: USD/RMB, EUR/USD, and AUD/RMB. To avoid hammering the API and slowing the page down, the rates are cached — staying fresh enough while keeping the page snappy.
