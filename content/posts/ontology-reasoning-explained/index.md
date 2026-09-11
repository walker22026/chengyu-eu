---
title: "How Does a Machine \"Figure Out\" Something You Never Told It? A Look at Ontological Reasoning"
date: 2026-08-14T16:00:24+00:00
summary: "Databases only know what you explicitly store; ontological reasoning lets a machine derive thousands of unstated facts from a handful of concept definitions — powerful, expensive to build, and increasingly paired with LLMs to keep each other's weaknesses in check."
tags: ["AI"]
categories: ["Blog"]
featureImage: "https://media.chengyu.eu/images/2026/08/14/Pasted-image-20260814141416.jpg"
---

### A dumb question

Imagine you're teaching an assistant who's never seen the world how to manage your household affairs. You tell it two sentences:

> Old Wang is my dad. I'm Xiao Ming.

Then you ask it: what's the relationship between Old Wang and Xiao Ming?

A normal person answers in a fraction of a second. But for a machine that only stores data, this is a trap — its database has exactly two records, neither of which says "Old Wang is Xiao Ming's father." So the standard answer is: **no such relationship found.**

The traditional way to get it right is to hand-write another rule: "If A is B's father, and B is the one asking, then A is the asker's father." That works — until you quickly discover this path doesn't scale. Grandfather, nephew, cousins, brothers-in-law, a daughter-in-law's sister's husband — family relationships alone can tangle you up completely, and you'd need hundreds of rules. And switch domains (a company's equity structure, a disease classification system, a telecom pricing structure) and you're writing hundreds more rules from scratch.

**Ontological reasoning is trying to solve exactly this: letting a machine, with just a small set of concept definitions, derive on its own the mountain of conclusions you never explicitly stated.**

### What an ontology is: sketching a skeleton of the world

The word "ontology" sounds abstract, but it's borrowed from philosophy. In philosophy, ontology studies "what actually exists in the world"; once computer science borrowed the term, the meaning got a lot more practical: **write down, in a way a machine can read, what concepts exist in a given domain, what relationships hold between them, and what rules can never be violated.**

Take family relationships as an example — roughly what you'd need to write is: what classes exist (person, man, woman); who's a subset of whom (man is a kind of person, woman is a kind of person); what relationships exist (someone is someone's "parent," "spouse," "sibling"); and the rules (a person has exactly one biological father; man and woman don't overlap; "father" equals "is male + is someone's parent").

That last point is the essence. You didn't directly define what a "father" is — you **assembled** it out of other concepts. Give a machine this assembly instruction, and it can go search the database on its own: who is both male and someone's parent? Whoever it finds, that person is a father — even if the database never had a "father" label to begin with.

Add another assembly rule: "grandfather = the father of a father," and the machine can automatically derive a whole batch of grandfathers too. You wrote five definitions; it derived thousands of conclusions. That's the basic idea behind ontological reasoning.

### What a reasoner actually does

The program that does this work is called a **reasoner** — common ones in the industry include HermiT and Pellet. It mainly does three things.

**First, it states what's implied.** The grandfather example above — nothing was stored directly, but it logically has to be true, and the reasoner fills it in for you.

**Second, it auto-classifies things.** You add a new person to the database and only tell the machine "he's male, and he's someone's parent." The reasoner automatically places him into the "father" category, and along the way places that someone into "someone's child." You never manually applied a single label.

**Third — the part most easily overlooked, and actually the most valuable — it catches contradictions.** If you accidentally entered both "Xiao Hong is a man" and "Xiao Hong is a woman," and the ontology explicitly states those two classes are mutually exclusive, the reasoner will flag it on the spot and tell you the knowledge base contradicts itself. It's not just deriving new things — it can prove that certain things **cannot possibly both be true.** In compliance, auditing, and risk-control scenarios, this capability matters even more than the reasoning itself.

### The fundamental difference from "guessing"

At this point you might be wondering: can't a large language model do all of this too? If I ask ChatGPT how Old Wang and Xiao Ming are related, it can answer that as well.

It can — but the mechanism is completely different, and the difference comes down to one word: **guarantee.**

An LLM's answer comes from statistical patterns learned across a massive amount of text. It gets it right because "my dad" and "father-son relationship" have appeared together in countless sentences. That's an extremely powerful capability — it can handle expressions you never defined, that are vague, or roundabout. But it can't give you a guarantee: phrase it differently and it might get it wrong, ask the exact same question twice and get inconsistent answers, and when it doesn't actually know, it'll still confidently make something up.

Ontological reasoning is **deduction.** Its conclusion isn't "this looks about right" — it's "starting from these premises, this must be true." And it can lay out the derivation step by step for you: because Old Wang is Xiao Ming's parent, because Old Wang is male, because the definition of father is a male parent, therefore Old Wang is Xiao Ming's father. **Every link in that chain can be checked by a human.**

Here's one way to put it: an LLM is like a well-read expert with great intuition, who occasionally talks out of turn; ontological reasoning is like an accountant who only trusts hard logic, but never makes a mistake. Ask the former the wide-open, imaginative questions; go to the latter for the conclusion you need to sign your name to.

### Where the cost lies

This all sounds great so far, but ontological reasoning's actual adoption in industry isn't very high, and the reasons are pretty concrete.

**Writing an ontology is absurdly expensive.** That whole set of concepts, relationships, and rules has to be defined one by one by experts who genuinely understand the business, and the logic has to stay self-consistent throughout. A medium-sized domain ontology taking a few people several months is normal. And the world keeps changing — when business rules change, the ontology has to change with them, and changing one rule can ripple across a large chunk of the structure. A lot of enterprise ontologies, once built, end up untouched because nobody dares modify them, gradually drift away from the real business, and eventually get abandoned.

**It can't tolerate vagueness or exceptions.** Real-world rules are rarely clean. "A customer with an overdue bill should be suspended" — sounds like an ironclad rule, but in actual business there's a pile of exceptions hanging off it: VIPs aren't suspended, government/enterprise accounts aren't suspended, disputed bills aren't suspended, emergency communications aren't suspended. Ontological logic handles this "true in principle, but with exceptions" situation very clumsily — you have to enumerate every exception as a formal definition, and it quickly bloats into something unmaintainable.

**It's rigidly single-minded.** If a premise is wrong, it will meticulously propagate that error across the entire knowledge base — the more thoroughly it reasons, the more broadly it's wrong. And if a premise is simply missing, it stays silent — it'll quietly tell you "unknown," but it won't flag "hey, a rule seems to be missing here."

**It also has a scale ceiling.** Logical reasoning has high computational complexity, and once the number of entities grows large, it stops being tractable — in practice you often have to fall back to a weakened, simplified version.

### So who actually benefits

Putting all of this together, the scenarios where ontological reasoning genuinely pays off are actually pretty narrow, and need to satisfy several conditions at once: the concept hierarchy is fairly complex, there are too many implicit relationships to hand-write as rules, the knowledge is relatively stable and doesn't change daily, and — most critically — **the conclusions must be able to produce a derivation that can withstand scrutiny.**

Healthcare is a classic example. The SNOMED CT medical terminology ontology contains hundreds of thousands of concepts; a doctor enters a specific diagnosis, and the system can automatically classify it into the correct disease category, used for statistics, insurance settlement, and clinical decision support. This needs both a complex concept hierarchy and every classification step to survive being questioned.

Auditing, compliance, and financial risk control are the same. You need to be able to answer a regulator asking "on what basis did you determine this transaction violates the rules" — and the answer can't be "the model thought it looked like it."

Conversely, if your domain's rules are simple, enumerable, and don't change often, a plain rule engine or database constraints will do the job just fine, and run faster too. **Bringing in an ontology for that is overkill.**

### A more interesting ending

Interestingly, over the past few years the relationship between ontological reasoning and large language models has shifted from "which one replaces the other" to "each patching the other's weaknesses."

An LLM's classic flaw is hallucination — it'll confidently say things without them being reliably true. An ontology's classic flaw is cost — it's reliably true, but too expensive to build. So a natural combination emerged: let the LLM read documents, extract concepts, and draft the ontology (turning months of work into days, with experts just reviewing), and then, in reverse, use the completed ontology to validate the LLM's output — if a generated conclusion references a concept combination that doesn't exist in the ontology, or that's mutually exclusive within it, it gets blocked outright.

**One handles "being able to figure it out." The other handles "not getting it wrong."** No single technology today can do both well at once, and there's no sign that's changing anytime soon.
