---
title: "Testing Models Against a Real Use Case, Not Just Benchmarks"
date: 2026-04-18T17:00:16+00:00
summary: "Blind-testing two Ollama models against real CIPLE A2 Portuguese-exam questions, the well-documented, benchmark-topping model lost badly to an obscure community fine-tune — because the benchmarks were measuring Brazilian Portuguese, not European."
tags: ["AI", "Language Learning"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/04/18/Gemini_Generated_Image_nahkl7nahkl7nahk56194cc7161df992.jpg"
---

A note up front: I recently needed a model that's strong specifically in Portuguese. When picking a model, the first thing we usually look at is parameter count — but does parameter count actually determine the outcome? It's better to narrow down to a parameter range and then actually test candidate models against a real use case, letting real-world results speak for themselves. This post records a blind test I ran using real exam-style questions to compare two candidate models, along with the process and the conclusion. I initially favored one of them going in — the actual results proved me wrong.

### Background: why these two models?

Within a 12GB VRAM budget, there really aren't that many local models that can handle pt-PT (European Portuguese) well. After an initial round of filtering, two candidates stood out, both installable with a single `ollama pull`:

**Candidate A: `aya-expanse:8b`**

A multilingual flagship model from Cohere Labs, supporting 23 languages. Backed by a full paper, public benchmarks, DPO preference training, model merging — a complete methodology. On the multilingual Arena-Hard-Auto benchmark, it beat Gemma 2, Qwen 2.5, and Llama 3.1 in its class. High transparency, strong community reputation, industrial-grade production.

**Candidate B: `jobautomation/OpenEuroLLM-Portuguese`**

A personal project from an Ollama community user (jobautomation), fine-tuned from Gemma 3 on Portuguese-language corpora. No benchmarks, no technical report, just a README. The name borrows from the EU's OpenEuroLLM project but has no relation to that official project.

Honestly, before testing, I was leaning toward aya-expanse — it's well-documented, has a name behind it, and has benchmark data. OpenEuroLLM-Portuguese was, to me, a black box — more of a gamble.

### Why not just trust the benchmark scores?

Multilingual LLM benchmark scores share a common problem: nearly all of them are evaluated on data dominated by pt-BR (Brazilian Portuguese). Most "Portuguese" content on the internet is Brazilian Portuguese, so a model scoring 90 on a PORTUGUESE benchmark might be 90% pt-BR ability and only 10% pt-PT (European Portuguese) ability.

And that 10% is exactly what I care about — writing "ônibus" instead of "autocarro" would cost you points, and so would "estou estudando" instead of "estou a estudar." The differences between pt-PT and pt-BR in vocabulary, grammatical structure, verb conjugation, pronoun placement, and preposition usage are things no general-purpose benchmark can measure.

So I decided to test with what CIPLE actually asks.

### Test design: 10 A2-level practical questions

I designed 10 questions covering the main dimensions tested by CIPLE A2. Both models used the identical system prompt (both required to use pt-PT), the same temperature (0.5), the same max tokens (500) — only the model name changed.

| # | Dimension | What it tests |
| --- | --- | --- |
| 1 | Core vocabulary translation | pt-PT vs. pt-BR high-frequency word pairs (autocarro/ônibus, etc.) |
| 2 | *Tu* verb conjugation | Standard in everyday pt-PT, almost never used in pt-BR |
| 3 | Second-person address forms | When tu/você/o senhor are used in Portugal |
| 4 | Bakery-conversation scenario | A common A2 scenario, testing natural phrasing |
| 5 | Self-introduction writing | Directly matches CIPLE's writing section |
| 6 | Future-tense tendency | Whether `ir+inf` or `futuro simples` is more everyday |
| 7 | Preposition usage | European preposition usage (ir a casa vs. ir para casa) |
| 8 | Authentic colloquialisms | Meaning of fixe/giro/está-se bem |
| 9 | Grammar correction | Rewriting a pt-BR passage into pt-PT |
| 10 | Listening comprehension | Simulated CIPLE train-station announcement question |

Every question had a clear correct answer — this wasn't a subjective "does it write well" evaluation, but a check of how solid the model's grasp of pt-PT actually is.

### The results were a shock

The bottom line first: OpenEuroLLM-Portuguese won decisively, 48/50 against aya-expanse's 35/50. What was even more surprising was that aya-expanse's mistakes weren't minor details — they were systematic pt-BR contamination.

Here are a few of the most telling examples.

**Q5 self-introduction: a single pronoun position gave it away.** I had both models write an A2-level student self-introduction.

aya-expanse's output (excerpt): "...estou a estudar para me tornar um tradutor e interprete, e actualmente me preparo para o exame CIPLE..."

OpenEuroLLM's output (excerpt): "...Estou a preparar-me para o CIPLE para melhorar o meu português!"

There's a key pt-PT grammar point here: clitic pronoun placement. In pt-PT, in a context that doesn't trigger proclisis (a pronoun before the verb), the pronoun must come after the verb (enclisis) — i.e., "preparo-me." Writing "me preparo," with the pronoun placed first, is typical pt-BR grammar. Aya wrote "me preparo," which would cost points on a CIPLE writing question. OpenEuroLLM not only got the pronoun position right, it also naturally used the pt-PT-specific progressive construction "estou a + infinitive" (pt-BR would say "estou me preparando"). This is one of the hardest pt-PT signals to get right, and OpenEuroLLM passed cleanly.

**Q6 future tense: aya got the facts backwards.** I asked both models how to say "I am going to travel to Porto next week" in pt-PT, and which is more commonly used day-to-day, `ir + infinitivo` or `futuro simples`?

aya-expanse's answer: "In everyday Portuguese, futuro simples ('Viajarei...') is more frequent and natural; ir + infinitivo is used less."

OpenEuroLLM's answer: "In everyday conversation in Portugal, 'Vou viajar...' (ir + infinitivo) is more natural and common. Futuro simples is more formal and rarely used in speech."

OpenEuroLLM is actually correct here. Whether in Portugal or Brazil, spoken Portuguese favors `ir + infinitivo`; the simple future (Viajarei) carries a formal, written-register feel. Aya got this completely backwards — this isn't a matter of stylistic preference, it's a plain linguistic fact error. Worse, aya's example sentence used "semana que vem," a Brazilian-Portuguese collocation; people in Portugal are more likely to say "na próxima semana" or "para a semana."

**Q3 the subtle status of "você": a cultural trap.** I asked both models when tu/você/o senhor are each used in pt-PT.

aya-expanse said "você" is a formal, neutral form, suitable for professional exchanges, strangers, or situations calling for politeness. OpenEuroLLM said "você" isn't commonly used in Portugal — it can sound somewhat formal or distant, used when wanting to keep some distance without being overly formal.

This was the question that hit me hardest. What aya described is entirely the Brazilian usage pattern. In Portugal, saying "você" to a stranger isn't just impolite — it can come across as condescending or distancing. In formal settings, people in Portugal either use "o senhor/a senhora" or drop the subject entirely (verb conjugation already carries the person). This is a genuinely sensitive point in Portuguese culture that foreign learners commonly trip over. OpenEuroLLM caught this nuance — "um pouco formal ou distante" (a bit formal or distant) — which is the real sense Portuguese speakers have of it. If a student followed aya's advice and used "você" with a stranger on the street in Portugal, they'd likely get an awkward look in return.

**Q7 preposition: "vou para casa" vs. "vou a casa".** This is a classic pt-PT sticking point: both `a` and `para` translate to "to/going," but they carry different meaning. `Vou a casa` implies a brief stop ("going home for a bit, then leaving again"); `Vou para casa` implies returning and staying a while ("heading home [for the day]"). I gave the prompt "Vou \_\_\_ casa" with no additional context, defaulting to the scenario of going home after work. aya filled in `a`, OpenEuroLLM filled in `para`. If you ask a Portuguese person where they're heading after work, nine times out of ten they'll say "vou para casa," not "vou a casa."

### Why did aya-expanse trip up on these points?

Thinking it over afterward, this is actually explainable. Even though aya-expanse's training data is large in volume, it's shared across 23 languages, and the Portuguese portion is likely dominated by pt-BR (reflecting the real-world distribution of Portuguese content online). Its statistical understanding of "Portuguese" is dominated by pt-BR. When you explicitly ask for pt-PT, it can switch vocabulary (high-frequency words like autocarro, ginásio, telemóvel come out fine), but the deeper grammatical structure and feel for the language can't be temporarily injected through a system prompt — pronoun placement, tense preference, culturally sensitive terms are baked into the model, and no prompt can undo that.

That's exactly where OpenEuroLLM-Portuguese's edge lies. It's a niche model fine-tuned specifically on pt-PT corpora — no paper, no benchmark, but the author is very likely a Portuguese person who genuinely understands pt-PT — you can tell from its grasp of the subtle sense of "você," its use of "Queria" rather than "Quero" as the polite way to order at a shop, and its natural use of the distinctly Portuguese collocation "para a semana." A focused small model beating a general-purpose large one on a narrow domain — this isn't the first time that's happened.

### A reflection on "transparency"

Before testing, my main concern with OpenEuroLLM-Portuguese was that it's a black box — no paper, no benchmarks, an anonymous author, no disclosed training details. By comparison, aya-expanse is practically a model of academic transparency.

But the actual test results made me realize: transparency and fitness-for-purpose are two different things. For industrial applications, for research that needs reproducibility, for commercial deployment, transparency obviously matters. But for an end user like me, treating the model as a tool, what actually matters is: does it work well for my specific use case?

All of aya-expanse's benchmark scores are meaningless for this pt-PT-specific scenario — because none of those benchmarks actually measure pt-PT's distinctive features. OpenEuroLLM-Portuguese, despite having no public scores at all, performed consistently, precisely, and in line with the CIPLE standard across my 10 real-scenario questions.

A test of 10 real-scenario questions is worth more than 10 benchmark reports — provided those 10 questions actually cover the dimensions you care about.

### Final workflow adjustment

After testing, I changed my configuration:

```bash
cat > Modelfile.ciple <<EOF
FROM jobautomation/OpenEuroLLM-Portuguese:latest
SYSTEM """És um tutor de português europeu especializado em preparar estudantes para o exame CIPLE A2. Responde sempre em pt-PT rigoroso. Corrige erros típicos de quem aprendeu pt-BR. Sê conciso."""
PARAMETER temperature 0.6
EOF

ollama create ciple-tutor -f Modelfile.ciple
```

I didn't delete aya-expanse, just changed what it's used for:

| Scenario | Which model |
| --- | --- |
| Precise pt-PT proofreading, Portuguese writing | **OpenEuroLLM-Portuguese** |
| Quick multilingual switching (English/Chinese/Portuguese translation) | aya-expanse |
| Verifying Portuguese cultural expressions, authentic usage | **OpenEuroLLM-Portuguese** |
| Long-form comprehensive text needs | aya-expanse |

OpenEuroLLM runs about twice as slow as aya (172 seconds total for the 10 questions vs. 87 seconds), but for exam prep, accuracy matters far more than speed.

### A few open questions

Who's actually behind OpenEuroLLM-Portuguese? The README doesn't say. Judging from output quality, it's very likely a Portuguese person who understands pt-PT, but the specific background is unknown. How clean is its training data? No public information — it may have some pt-BR contamination mixed in (I noticed one or two questionable phrasings during testing), but overall it's far cleaner than aya's. Is it worth re-testing once official AMALIA and Gervásio GGUFs are out? These two are official pt-PT-specific models from Portuguese academic institutions, and in theory should be more specialized than a community fine-tune — but as of now there's no ready-made GGUF or Ollama version, so I'll test again once someone in the community packages it.
