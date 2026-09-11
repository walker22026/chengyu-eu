---
title: "AI Study Notes: Probability Density Functions and Weighted MSE"
date: 2024-07-31T00:00:02+00:00
summary: "Working through two ML fundamentals in plain language: what a probability density function actually tells you, and how weighting by probability density changes mean squared error."
tags: ["Machine Learning", "Study Notes"]
categories: ["Blog"]
---

### 1. What is a probability density function?

Imagine playing darts against a long straight line instead of a circular board.

- A probability density function (PDF) is basically a "map" of how likely your dart is to land at each point along that line.
- Why "density"? Because it tells you how concentrated the landings are near each point — the higher the density, the more likely a dart lands there.
- If you plot this "map," you get a wavy curve: the higher the curve at a point, the more likely a dart lands near it.
- Two key properties: the total area under the curve always equals 1 (100% probability), and the curve never dips below zero (probability can't be negative).
- Real-world uses: modelling the distribution of exam scores, estimating a product's lifespan, analysing an athlete's performance.
- An everyday analogy: picking out a specific height in a crowd — the PDF tells you which height range you're most likely to find a match in.

The key thing to remember: a PDF doesn't hand you a probability directly — it describes the *shape* of the distribution. To get an actual probability, you need the area under the curve over some range.

### 2. Mean squared error, weighted by a probability density

A quick recap: a data distribution *D* describes the overall pattern in your data, while a probability density function *p* gives the precise mathematical description of it.

**What is MSE?** Mean Squared Error measures the gap between predicted and actual values.

**In plain terms:** back to darts — after each throw, measure the distance from the dart to the bullseye, then square that distance. MSE is just the average of all those squared distances.

**Formally, based on D and p:**

```
MSE = E[(Y - f(X))²]
```

where `E` is the expected value (the mean), `Y` is the actual value, `f(X)` is your prediction, and the expectation is taken with respect to the distribution `D` / density `p`.

**In plain terms again:** say you're predicting exam scores. `Y` is a student's real score, `f(X)` is your prediction, `(Y - f(X))²` is the squared error, and we average that squared error using `D` and `p`.

**Why square it?** Squaring stops positive and negative errors from cancelling out, and it penalises large errors more heavily.

**A tiny worked example:** predicting three students' scores — actual: 80, 85, 90; predicted: 82, 83, 91.

```
(82-80)² + (83-85)² + (91-90)² = 4 + 4 + 1 = 9
MSE = 9 ÷ 3 = 3
```

### Extending the example: bringing probability density into it

Now imagine a full class of 100 students instead of just three, and assume their scores follow something like a normal (bell-curve) distribution. The density function `p(x)` tells you how likely each score is to occur.

Instead of just averaging every squared error equally, we weight each one by how likely that score was to occur in the first place:

```
MSE = ∫ (y - f(x))² · p(x) dx
```

The intuition: scores near the peak of the distribution (say, most students clustering around 70–80) carry more weight, because they're common — errors there matter more to the overall picture. Scores out in the tails (very high or very low, and rare) contribute less, even if the error on any single one of them happens to be large.

A simple way to think about it: every score has an "importance weight" attached, and common scores pull more weight in the final average than rare ones — similar to how you'd weigh a restaurant's frequently-ordered dishes more heavily than a rarely-ordered special when judging its overall quality. In practice, you rarely know the exact density function up front, but you can estimate it from enough data — and doing so makes your error metric better reflect how the model will actually perform in the real world, rather than being skewed by rare edge cases.
