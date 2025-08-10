---
title: "Why Variance Has a Secret: n − 1 (Bessel's Correction)"
date: 2025-08-10
categories: [Statistics, Intuition]
tags: [variance, sampling, simulation]
---

> **Hook:** Imagine measuring how wild Mumbai traffic is, but only listening during Ganesh Chaturthi — your estimate will almost certainly be wrong. That’s what happens when we estimate population variance using `n` instead of `n−1`. This post shows why — visually, intuitively, and with a simulation.

---

## 1 — The innocent-looking formula

For a population we (theoretically) use:
\[
\sigma^2 = \frac{1}{N}\sum_{i=1}^N (x_i - \mu)^2
\]

But in practice we usually only have a *sample*. The common *sample* variance formula you see in textbooks is:

\[
s^2 = \frac{1}{n-1}\sum_{i=1}^n (x_i - \bar x)^2
\]

Why `n − 1`? Why not `n`?

---

## 2 — A tiny, human intuition

Think of variance as “how far people wander from the group leader” during a trek.

- If you know the **true leader** (population mean), you measure actual wandering.  
- If you only have a handful of hikers, you naturally pick **the friend in the middle** as the leader (the sample mean). That person is already close to everyone — so measured wandering looks smaller.  

Using `n` **underestimates** the spread because the sample mean is biased toward the sample points. `n − 1` corrects that tiny, but consistent, bias.

---

## 3 — Picture time (quick illustrations)

**Sample points vs true mean vs sample mean**

![Sample points vs means](/assets/numberline_sample_mean.png)

The figure above shows a real sample (blue dots), the true mean (square) and the sample mean (diamond). Notice how the deviations from the **sample mean** are slightly smaller than deviations from the **true mean** on average.

---

## 4 — Simulation: what happens if we divide by different numbers?

I ran a simple simulation where, for each sample size `n`, I drew many independent samples from a Normal(0,1) population (true variance = 1). For each sample I computed:
- `ss / (n+1)` — divide by n+1  
- `ss / n` — divide by n  
- `ss / (n-1)` — divide by n−1  
- `ss / (n-2)` — divide by n−2  
- `ss / (n-3)` — divide by n−3

(where `ss = sum((x_i - xbar)^2)`).

**Average estimate vs sample size**:

![Average estimated variance vs sample size](/assets/bias_vs_n.png)

**What this shows (read the plot left→right):**
- For small `n`, dividing by `n` gives an estimate **below** the true variance (underestimates).  
- Dividing by `n−1` sits very close to the true variance across all `n` (it's *unbiased*).  
- Dividing by fewer (e.g., `n−2`, `n−3`) wildly overestimates for small `n`.  
- Dividing by `n+1` underestimates even more than `n`.  
- As `n` grows large, all estimators converge toward the true value — the difference matters most for small samples.

---

## 5 — Distribution view (n = 5)

Here’s how the **distributions** of these estimators look when `n = 5`:

![Histogram of estimators for n=5](/assets/hist_estimators_n5.png)

You can see:
- The `n+1` and `n` curves sit left of the truth (they’re biased low).
- The `n−1` curve centers near the true value.
- The `n−2` and `n−3` curves shift right (overestimation) and have long tails.

---

## 6 — Short mathematical check (sketch)

Use identity:
\[
\sum_{i=1}^n (X_i - \bar X)^2 = \sum_{i=1}^n (X_i - \mu)^2 - n(\bar X - \mu)^2.
\]
Take expectations:
\[
E\Big[\sum (X_i - \bar X)^2\Big] = n\sigma^2 - n\cdot Var(\bar X) = n\sigma^2 - n\cdot \frac{\sigma^2}{n} = (n-1)\sigma^2.
\]
Divide both sides by `n-1` ⇒ the expected value of the statistic equals \(\sigma^2\). That is **unbiased**.

---

## 7 — Practical takeaway

- **Use `n−1`** when estimating variance from a sample: most statistical software uses this by default for sample variance (ddof=1 in NumPy/Pandas).  
- The correction matters most for small samples. For very large samples, difference between `n` and `n−1` is tiny.  
- This is a neat example of a broader principle: *estimating something from the same data uses up degrees of freedom* — account for that when you correct your formulas.

---
