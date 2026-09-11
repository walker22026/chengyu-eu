---
title: "Trying Out Claude's Finance Analysis Feature on NVDA"
date: 2026-02-26T18:00:24+00:00
summary: "Setting up Claude's Finance Analysis tool through Cowork, then having it produce a full comps-based valuation report on NVIDIA against AMD, Broadcom, Qualcomm, Marvell, and Intel."
tags: ["AI", "Finance"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/02/26/2026-02-26-17.33.42.jpg"
---

Claude's Finance Analysis feature has been getting a lot of buzz lately — the pitch is that a single model can replace the analytical work of multiple roles at a financial firm, from macro analysis to deep-dive stock research, from reading financial statements to building an investment strategy, start to finish.

Today I couldn't resist trying it myself.

Honestly, I was completely lost at first — not clear on how to use it or where to even start. Claude's own install instructions for the feature weren't very clear either; what finally got me unstuck was handing it the documentation straight from GitHub and letting it work through the setup itself.

This post has two parts: the first covers installing and using it; the second — riding the current hype — has it actually analyze NVDA (NVIDIA) to see whether it looks over- or under-valued. The full report is below.

### Part 1: installing and using the plugin

I used the **Cowork** path to run Claude Finance Analysis. Setup was simple: open the Claude interface, click **Customize** in the left navigation, find the Finance Analysis plugin/tool in the Customize panel, follow the prompts to install and configure it, and it's ready to call directly in a conversation once installed.

One tip: if you get stuck during setup, go straight to the GitHub repo for the official docs — they're much clearer than Claude's own description. Handing the doc link to Claude and letting it read through the doc itself works better than trying to muddle through on your own.

![Finding Finance Analysis under Customize](https://media.chengyu.eu/images/2026/02/26/2026-02-26-17.33.42.jpg)

### Part 2: demo report — NVIDIA (NVDA), a deep dive and investment framework

> **Disclaimer:** this piece is based on comparable-company financial data and is for research reference only — it is not investment advice. Investing carries risk; consult a professional financial advisor before making decisions. Data as of February 26, 2026, sourced from each company's most recent annual filings and public disclosures.

**Executive summary.** NVIDIA (NASDAQ: NVDA), on the strength of its dominant position in AI-accelerated computing, has transformed from a traditional GPU chip company into the world's most important compute-infrastructure provider. Full-year FY2026 revenue reached $215.9 billion, up 65% year over year, with an EBITDA margin of 70.2% — a combination essentially unmatched among comparable global semiconductor companies, and the core support behind its roughly $4.69 trillion market cap.

Looking at comparable companies, NVIDIA's valuation premium is significant but not unfounded: its EV/revenue multiple of roughly 21.7x is well above the industry median of about 10x, but its 65% growth rate is likewise far above the peer median of roughly 19%. The real question isn't whether NVIDIA is expensive — it's how long that growth rate can hold.

![Comparing NVIDIA against peers](https://media.chengyu.eu/images/2026/02/26/2026-02-26-17.33.56.jpg)

**Business overview.** NVIDIA's focus has shifted entirely from consumer GPUs to enterprise AI infrastructure centered on the data center. Its revenue mix breaks down roughly as: data center (85%+ of revenue) — H100, H200, and Blackwell-series GPUs covering both training and inference; gaming (~10%) — the GeForce line, with a stable market share but no longer a growth driver; and professional visualization plus automotive (~5%) — the DRIVE platform gradually ramping up. NVIDIA's core logic is no longer "selling chips" — it's building and controlling an AI computing platform ecosystem (the CUDA ecosystem, NVLink interconnects, InfiniBand networking), forming a highly sticky technical moat.

**Revenue scale and growth, by company (fiscal-year revenue in millions):**

| Company | FY revenue | YoY growth | Notes |
| --- | --- | --- | --- |
| NVIDIA (NVDA) | $215,900 | +65% | FY2026, through Jan 2026 |
| AMD | $34,600 | +34% | FY2025, through Dec 2025 |
| Broadcom (AVGO) | $64,000 | +24% | FY2025, through Oct 2025 |
| Qualcomm (QCOM) | $44,284 | +14% | FY2025, through Sep 2025 |
| Marvell (MRVL) | $5,767 | +25% | FY2025, through Feb 2025 |
| Intel (INTC) | $52,900 | 0% | FY2025, through Dec 2025 |

NVIDIA leads at 65% growth, almost double second-place AMD's 34% — and maintaining that rate on top of an already massive $215.9 billion revenue base is genuinely rare. Intel's flat revenue, by contrast, is a clear picture of the structural challenge traditional CPU architectures face in the AI era.

**Profitability comparison:**

| Company | Gross margin | EBITDA margin | Free cash flow |
| --- | --- | --- | --- |
| NVIDIA (NVDA) | 71.1% | 70.2% | ~$45,000M |
| Broadcom (AVGO) | 78.3% | 67.2% | ~$22,000M |
| Qualcomm (QCOM) | 55.4% | 31.2% | ~$12,000M |
| AMD | 50.0% | 19.4% | ~$2,800M |
| Marvell (MRVL) | 41.3% | 8.8% | ~$400M |
| Intel (INTC) | 36.7% | 5.5% | -$1,500M |

NVIDIA and Broadcom have similar gross margins (Broadcom's is slightly higher), but NVIDIA's revenue is 3.4x Broadcom's and its growth rate is nearly 3x. Broadcom's high margin comes largely from post-acquisition cost optimization, while NVIDIA's comes from pricing power — H100/H200 GPUs were in sustained short supply for a long stretch, letting NVIDIA essentially set its own prices. AMD's profitability still trails NVIDIA by a wide margin, with an EBITDA margin (19.4%) less than a third of NVIDIA's, reflecting the cost pressure AMD faces competing for AI GPU market share. Intel's 5.5% EBITDA margin reveals a company deep in a difficult transition, with losses in manufacturing eating into the profit of its downstream chip design business.

**Industry percentile summary (comparable metrics only):**

| Statistic | Revenue growth | Gross margin | EBITDA margin |
| --- | --- | --- | --- |
| Max | 65.0% | 78.3% | 70.2% |
| 75th percentile | ~30.5% | ~67.8% | ~55.0% |
| Median | ~24.5% | ~52.7% | ~25.3% |
| 25th percentile | ~12.5% | ~42.7% | ~12.1% |
| Min | 0.0% | 36.7% | 5.5% |

NVIDIA leads on both growth and EBITDA margin; Broadcom edges it slightly on gross margin (78.3% vs. 71.1%), but its overall earnings quality falls short of NVIDIA's.

**Valuation multiples:**

| Company | Market cap | EV | EV/Revenue | EV/EBITDA | P/E |
| --- | --- | --- | --- | --- | --- |
| NVIDIA (NVDA) | $4,690B | $4,689B | 21.7x | 30.9x | 47.4x |
| Broadcom (AVGO) | $1,583B | $1,632B | 25.5x | 38.0x | 69.4x |
| AMD | $349B | $347B | 10.0x | 51.8x | 79.7x |
| Qualcomm (QCOM) | $150B | $153B | 3.4x | 11.1x | 28.7x |
| Marvell (MRVL) | $68B | $70B | 12.1x | n/m | 28.4x |
| Intel (INTC) | $229B | $261B | 4.9x | n/m | n/m |
| **Industry median** | — | — | **~10.1x** | **~34.5x** | **~47.4x** |

**Reading the valuation.** Is NVIDIA's valuation reasonable? Three angles: on EV/revenue (21.7x), it's about 2.1x the industry median (10.1x) — but on a growth-adjusted basis (EV/revenue ÷ growth rate), NVIDIA comes out to roughly 0.33x, versus Broadcom's 1.06x, AMD's 0.29x, and Qualcomm's 0.25x. That suggests NVIDIA isn't actually the most expensive name once growth is accounted for — its premium reflects scale leadership and platform-monopoly value more than pure hype. On EV/EBITDA (30.9x), it's actually below the effective peer median (~34.5x) — counterintuitively, given it's the fastest-growing, highest-margin company in the group, but that's because NVIDIA has entered a stage of scaled profitability, with absolute EBITDA ($151.5B) far exceeding peers, shrinking the multiple as the denominator grows. On P/E (47.4x), it's essentially in line with the industry median (~47.4x); given NVIDIA's growth rate, that P/E implies a PEG ratio of about 0.73 (47.4 ÷ 65) — below 1 generally suggests the valuation is reasonable, or even cheap, relative to growth.

Taken together: NVIDIA's valuation premium has fundamental support, but it already reflects fairly high growth expectations — where the stock goes from here depends heavily on whether growth can hold in the 40–50%+ range.

**Core competitive advantages.** The CUDA ecosystem's moat runs deep — nearly 20 years of accumulated tooling, with more than 4 million developers worldwide building AI/HPC work on CUDA, and switching costs are steep: neither AMD's ROCm nor Intel's oneAPI comes close to CUDA's maturity, and switching platforms means not just recompiling code but abandoning years of accumulated optimization experience and engineering know-how — stickiness that's especially pronounced in AI training. NVIDIA also sells whole systems, not just chips — DGX systems and the HGX platform integrate GPUs, ultra-fast NVLink interconnects, and InfiniBand networking into a system-level moat, so customers are buying a full AI infrastructure stack rather than a single component, which lifts the competition from chip-vs-chip to ecosystem-vs-ecosystem. Its relationship with TSMC is another core edge — by locking down TSMC's advanced-node capacity (CoWoS packaging, 3nm/4nm), NVIDIA has effectively engineered a supply constraint during the AI compute boom, sustaining an unusually high gross margin (71%), not unlike how Apple's control over iPhone components converts a supply-chain advantage directly into margin. And NVIDIA is gradually shifting toward software revenue through products like NVIDIA AI Enterprise (subscription software) and DGX Cloud — software revenue tends to carry higher renewal rates and margins, and if that shift succeeds, NVIDIA's valuation story could evolve from "semiconductor cyclical" to "AI platform company," commanding an even higher premium.

**Key risks.** Competition is intensifying — AMD's MI300 series has broken through in some inference workloads, and hyperscalers like Google (TPU v5), Meta (MTIA), and Amazon (Trainium) are accelerating their own in-house AI chip efforts to reduce dependence on NVIDIA; as those chips mature, NVIDIA's data-center growth could slow. Demand sustainability is a real question, too — current AI compute demand is significantly driven by an "AI arms race" mentality, with major tech companies buying aggressively to avoid falling behind; if large-model ROI comes under scrutiny, or training efficiency improves sharply (algorithmic breakthroughs, like those from DeepSeek-style models, lowering the compute needed), capex could see a cyclical pullback that hits NVIDIA's orders in the short term. Geopolitics and export controls are a high risk — continued tightening of US semiconductor export controls to China has already barred high-end GPUs like the A100 and H100 from export there; NVIDIA developed reduced-spec versions (like the H20) in response, but China revenue is now structurally capped, and further tightening could affect a China business that represents roughly 15–20% of NVIDIA's global share. Valuation compression is a risk in its own right — the current 47x P/E already bakes in strong market expectations for the next 3–5 years, and if macro rates rise or risk appetite falls, high-multiple growth names get hit first, even with fundamentals intact. And customer concentration is a factor — NVIDIA's top five customers (Microsoft, Google, Meta, Amazon, Oracle, and other hyperscalers) drive most of its data-center revenue, and that concentration risk could grow more visible as those customers' in-house chip strategies mature.

**An investment framework** (restating: this is an analytical framework based on public data, not investment advice or a price prediction — factor in your own risk tolerance and consult a licensed professional before deciding).

The core bull case: AI infrastructure buildout is the most certain direction of this tech cycle, and NVIDIA is the least replaceable supplier on that path's supply side — whichever AI company ultimately "wins," training and inference will remain heavily dependent on NVIDIA GPUs. It's a similar logic to Cisco supplying internet-era network infrastructure, except NVIDIA's technical moat runs deeper (the CUDA ecosystem) and its margins are considerably higher (70% EBITDA versus Cisco's historical peak of around 40%).

Strategy options for different risk appetites: a **long-term core position** (for investors with a 3+ year horizon and higher risk tolerance) bets on the long-run growth of AI compute demand and tolerates short-term volatility, watching data-center revenue growth, gross-margin trends, H-series/Blackwell shipment volumes, and CUDA developer counts, with a re-evaluation trigger if data-center revenue growth falls below 25% for two consecutive quarters; **dollar-cost averaging** (for investors managing timing risk) takes advantage of volatility to build a position gradually rather than all at once — NVIDIA has seen single-quarter drawdowns of 30–40% before (as in 2022), and any setback to the AI narrative could offer a better entry point; and a **relative-value approach** (for more sophisticated investors) who like the AI compute thesis but are wary of NVIDIA's specific valuation might hedge with peers offering more moderate valuations and similar upside — Broadcom's EV/EBITDA (38x) is higher than NVIDIA's (31x) but its growth (24%) is far lower, and Qualcomm's P/E (28.7x) is only 61% of NVIDIA's, with edge-AI compute still to be unlocked — or go long the AI supply chain itself (TSMC, SK Hynix's HBM business) to benefit from compute demand growth while avoiding single-company concentration risk.

**Key catalysts to watch:** Blackwell Ultra's shipment ramp (positive, validates the next product cycle) in H1 2026; hyperscaler capex guidance (positive or negative, sets near-term order visibility) each earnings season; US-China export-control developments (negative risk, could weigh on valuation) on an ongoing basis; AMD's MI400 series launch (negative, a real competitive stress test) in 2026–2027; NVIDIA's software/subscription revenue mix (positive, could elevate the valuation narrative) each quarter; and macro rate policy (negative risk, higher rates pressure high-multiple growth names) around Fed meetings.

**A valuation sensitivity sketch:** target price ≈ next-12-months EPS × a reasonable P/E multiple. In an optimistic scenario (growth holding above 50%), EPS keeps growing fast and P/E could hold in the 50–60x range, implying meaningful further upside. In a base case (growth slowing to 30–40%), EPS growth moderates and P/E compresses to 35–45x, implying modest upside. In a pessimistic scenario (growth falling to 15–25%), EPS growth converges toward the industry average and P/E compresses to 20–30x, implying meaningful downside.

**Bottom line:** whether NVIDIA's investment case holds up ultimately comes down to one judgment call — whether AI compute demand is durable, and whether NVIDIA can hold onto its platform-dominant position over the next 3–5 years. If the answer is yes, the current valuation is reasonable; if AI investment enters a return-on-investment reckoning and growth slows meaningfully, valuation-compression risk shouldn't be ignored.

**Peer comparison summary table:**

| Metric | NVDA | AMD | AVGO | INTC | QCOM | MRVL | Median |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Revenue ($100M) | 2,159 | 346 | 640 | 529 | 443 | 58 | — |
| YoY growth | 65% | 34% | 24% | 0% | 14% | 25% | 24.5% |
| Gross margin | 71.1% | 50.0% | 78.3% | 36.7% | 55.4% | 41.3% | 52.7% |
| EBITDA margin | 70.2% | 19.4% | 67.2% | 5.5% | 31.2% | 8.8% | 25.3% |
| EV/Revenue | 21.7x | 10.0x | 25.5x | 4.9x | 3.4x | 12.1x | 10.1x |
| EV/EBITDA | 30.9x | 51.8x | 38.0x | n/m | 11.1x | n/m | 34.5x |
| P/E | 47.4x | 79.7x | 69.4x | n/m | 28.7x | 28.4x | 47.4x |

*n/m = not meaningful; data as of February 26, 2026. Based on a comparable-company (comps) framework, citing NVIDIA's FY2026 annual results (disclosed February 25, 2026) and peer public filings. For reference only — not a buy or sell recommendation.*

![The report's data breakdown](https://media.chengyu.eu/images/2026/02/26/2026-02-26-17.34.24.jpg)

### My own quick take

I don't really read financial statements myself, so I can't personally judge how reliable this generated report actually is — but I did make sure it was working from the most recent filings available, and the conclusions it reached are ones I could actually follow and understand.
