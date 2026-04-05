# Chapter 14: Decision Errors

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/foundations-errors](https://openintro-ims.netlify.app/foundations-errors)

---

## Table of Contents

- [Overview](#overview)
- [14.1 Discernibility Level](#141-discernibility-level)
- [14.2 Two-Sided Hypotheses](#142-two-sided-hypotheses)
- [14.3 Controlling the Type I Error Rate](#143-controlling-the-type-i-error-rate)
- [14.4 Power](#144-power)
- [14.5 Chapter Review](#145-chapter-review)
  - [14.5.1 Summary](#1451-summary)
  - [14.5.2 Key Terms](#1452-key-terms)
- [14.6 Exercises Overview](#146-exercises-overview)
- [Key Takeaways: Decision Errors in Hypothesis Testing](#key-takeaways-decision-errors-in-hypothesis-testing)

---

## Overview

Hypothesis tests are not flawless — data can point to the wrong conclusion. Innocent people are sometimes wrongly convicted in court, and similarly, statistical tests can lead to incorrect decisions. What distinguishes statistical hypothesis testing from a court system is that the framework allows us to **quantify and control** how often the data lead us to incorrect conclusions. This chapter describes the types of errors that can arise in hypothesis testing, how to define and quantify them, and strategies for mitigating errors.

---

## 14.1 Decision Errors

In a hypothesis test, there are two competing hypotheses: the null ($H_0$) and the alternative ($H_A$). We make a statement about which one might be true, but we might choose incorrectly.

**Four possible scenarios:**

| | **Reject $H_0$** | **Fail to Reject $H_0$** |
|---|---|---|
| **$H_0$ is true** | Type I Error | Good Decision |
| **$H_A$ is true** | Good Decision | Type II Error |

| Error Type | Definition | Court Analogy |
|------------|-----------|---------------|
| **Type I Error** | Rejecting $H_0$ when $H_0$ is actually true | Innocent person is wrongly convicted |
| **Type II Error** | Failing to reject $H_0$ when $H_A$ is actually true | Guilty person walks free |

**Key trade-off:** If we reduce how often we make one type of error, we generally make more of the other type. Lowering the Type I error rate (e.g., raising the standard for conviction) increases the Type II error rate, and vice versa.

---

## 14.1 Discernibility Level

The **discernibility level** ($\alpha$) provides the cutoff for the p-value that leads to a decision of "reject the null hypothesis." The traditional level is 0.05, but it should be adjusted based on the application and the consequences of errors.

| Situation | Recommended $\alpha$ | Reasoning |
|-----------|:---:|---|
| **Type I error is dangerous or costly** | 0.01 or 0.001 | Demand very strong evidence before rejecting $H_0$ |
| **Type II error is more dangerous or costly** | 0.10 | Be cautious about failing to reject $H_0$ when it is actually false |

> **Principle:** The discernibility level selected for a test should reflect the real-world consequences associated with making a Type I or Type II error.

---

## 14.2 Two-Sided Hypotheses

Previous case studies used **one-sided hypothesis tests** — they only explored one direction of possibilities (e.g., "are women discriminated against?" but not "are men discriminated against?"). There are two dangers in ignoring alternative directions:

1. **Inflated Type I error rate** — framing $H_A$ to match the direction the data point undermines rigorous error control
2. **Confirmation bias** — looking only for data that supports our worldview is not scientific

**One-sided vs. Two-sided tests:**

| Type | $H_A$ Form | When to Use |
|------|-----------|-------------|
| **One-sided** | $p_T - p_C > 0$ or $p_T - p_C < 0$ | Only interested in a single direction |
| **Two-sided** | $p_T - p_C \neq 0$ | Want to consider all possibilities (default) |

> **Default to a two-sided test.** Use a one-sided hypothesis test only if you truly have interest in only one direction.

### Case Study: CPR and Blood Thinners

An experiment examined whether blood thinners help or harm patients who underwent CPR for a heart attack. 90 patients were randomly assigned to treatment (blood thinner, $n = 40$) or control (no blood thinner, $n = 50$).

| Group | Died | Survived | Total |
|-------|:---:|:---:|:---:|
| **Control** | 39 | 11 | 50 |
| **Treatment** | 26 | 14 | 40 |
| **Total** | 65 | 25 | 90 |

**Hypotheses (two-sided):**
- $H_0$: Blood thinners have no overall survival effect. $p_T - p_C = 0$
- $H_A$: Blood thinners have an impact on survival (positive or negative). $p_T - p_C \neq 0$

**Observed rates:**
- Control survival: $\hat{p}_C = 11/50 = 0.22$
- Treatment survival: $\hat{p}_T = 14/40 = 0.35$
- Point estimate: $\hat{p}_T - \hat{p}_C = 0.13$ (13% additional survival)

**Null distribution from 1,000 simulations:**
- Right tail area (differences ≥ 0.13): 0.131
- **Two-sided p-value:** $0.131 \times 2 = 0.262$

Since 0.262 > 0.05, we do not reject $H_0$. The data do not provide convincing evidence that blood thinners influence survival after CPR.

**Computing a two-sided p-value:** First compute the p-value for one tail of the distribution, then double that value.

---

## 14.3 Controlling the Type I Error Rate

It is **never okay** to change a two-sided test to a one-sided test after observing the data. Doing so doubles the Type I error rate.

**Why?** If $\alpha = 0.05$ and we freely choose the one-sided direction based on which way the data point:
- When the sample difference is positive, we test $H_A: \text{difference} > 0$ and reject if it falls in the upper 5%
- When the sample difference is negative, we test $H_A: \text{difference} < 0$ and reject if it falls in the lower 5%
- Combined: we reject $H_0$ about $5\% + 5\% = 10\%$ of the time when $H_0$ is true — **twice the intended error rate**

> **Critical rule:** Hypotheses should be set up **before** observing the data.

---

## 14.4 Power

**Power** is the probability of rejecting the null hypothesis when the alternative hypothesis is true. In other words, if there is a real effect large enough to matter, power tells us how likely we are to detect it.

| Factor | Effect on Power |
|--------|----------------|
| **Larger effect size** | Increases power — bigger effects are easier to detect |
| **Larger sample size** | Increases power — more data provides more convincing evidence |
| **Reduced variability** | Increases power — pairing, better measurement, etc. |
| **Higher $\alpha$** | Increases power — but also increases Type I error rate |

**Competing considerations in study design:**
- We want enough data to detect important effects
- Collecting data can be expensive, and in human experiments, there may be risk to patients

A good power analysis is a vital preliminary step to any study — it informs whether the data you plan to collect will be sufficient to detect the effects you care about.

**Examples of increasing power:**
- Paired tests are more powerful than independent tests because pairing reduces inherent variability
- Tests based on the mean are more powerful than tests based on the median because the median is almost always more variable

---

## 14.5 Chapter Review

### 14.5.1 Summary

Hypothesis testing provides a strong framework for making decisions based on data, but analysts must understand how and when the process can go wrong. Sometimes when $H_0$ is true, we accidentally reject it (Type I error); sometimes when $H_A$ is true, we fail to reject $H_0$ (Type II error). The power of a test quantifies how likely it is to obtain data that rejects $H_0$ when $H_A$ is true; power increases with larger sample sizes. Two-sided tests should be the default, and hypotheses must be set before seeing the data.

### 14.5.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Confirmation bias** | Looking for data that supports our pre-existing ideas rather than testing objectively |
| **Discernibility level ($\alpha$)** | The cutoff for the p-value that determines whether to reject $H_0$ |
| **Null distribution** | The sampling distribution of a statistic under the assumption that $H_0$ is true |
| **One-sided hypothesis test** | A test that only considers one direction of the alternative (e.g., $>$ or $<$) |
| **Power** | The probability of rejecting $H_0$ when $H_A$ is true |
| **Significance level** | Synonym for discernibility level (used in many texts) |
| **Two-sided hypothesis test** | A test that considers both directions of the alternative (e.g., $\neq$) |
| **Type I error** | Rejecting $H_0$ when $H_0$ is actually true (false positive) |
| **Type II error** | Failing to reject $H_0$ when $H_A$ is actually true (false negative) |

---

## 14.6 Exercises Overview

The chapter includes **10 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Testing for Fibromyalgia** | Writing hypotheses in words; identifying Type I and Type II errors in a medical context |
| 2 | **Testing for food safety** | Writing hypotheses; identifying Type I and Type II errors; evaluating which error is more problematic for different stakeholders; choosing an appropriate $\alpha$ |
| 3 | **Which is higher?** | Comparing standard error, margin of error, p-value, and Type II error probability across different scenarios (sample size, confidence level, $\alpha$) |
| 4 | **True/False** | Relationships between confidence levels and intervals; effect of $\alpha$ on Type I error; interpreting failure to reject; effect of large sample sizes |
| 5 | **Online communication** | Identifying errors in hypothesis notation (using $\hat{p}$ instead of $p$; mismatched values between $H_0$ and $H_A$) |
| 6 | **Same observation, different sample size** | Understanding how sample size affects the p-value (larger $n$ → smaller p-value for the same observed proportion) |
| 7 | **Estimating $\pi$** | Understanding that some CIs will miss the true parameter by chance; evaluating whether a professor was correct to penalize students |
| 8 | **Fermenting yeast** | Identifying Type I errors in the context of multiple independent studies; suggesting changes to lower the error rate |
| 9 | **Practical importance vs. statistical discernibility** | Understanding that large samples can detect trivial effects as statistically discernible |
| 10 | **Hypothesis statements** | Filling in null and alternative hypotheses for real-world scenarios; describing the parameter $p$ in words |

> Answers to odd-numbered exercises can be found in Appendix A.14 of the textbook.

---

## Key Takeaways: Decision Errors in Hypothesis Testing

### Error Types and Trade-offs

| Concept | Key Point |
|---------|-----------|
| **Type I error** | False positive — rejecting a true $H_0$; probability = $\alpha$ |
| **Type II error** | False negative — failing to reject a false $H_0$; probability = $\beta$ |
| **Trade-off** | Reducing one type of error generally increases the other |
| **Power** | $1 - \beta$; the probability of correctly rejecting a false $H_0$ |

### Choosing $\alpha$

| Context | Recommended $\alpha$ |
|---------|:---:|
| Type I error is more dangerous | 0.01 or lower |
| Type II error is more dangerous | 0.10 or higher |
| No strong preference | 0.05 (default) |

### One-Sided vs. Two-Sided Tests

| Feature | One-Sided | Two-Sided |
|---------|-----------|-----------|
| **$H_A$ direction** | $>$ or $<$ | $\neq$ |
| **p-value** | Single tail area | Double the single tail area |
| **When to use** | Only one direction matters | Default — consider all possibilities |

### Critical Warnings

1. ⚠️ **Never switch from two-sided to one-sided after seeing the data** — this doubles the Type I error rate from $\alpha$ to $2\alpha$
2. ⚠️ **Hypotheses must be set before observing data** — post-hoc hypothesis construction invalidates error rate control
3. ⚠️ **Failing to reject $H_0$ ≠ accepting $H_0$** — absence of evidence is not evidence of absence
4. ⚠️ **Statistical discernibility ≠ practical importance** — with large samples, trivial effects can be statistically discernible
5. ⚠️ **Some CIs will miss the true parameter by design** — a 95% CI misses 5% of the time; this is expected, not a mistake
6. ⚠️ **Multiple independent studies will produce false positives** — with $\alpha = 0.05$, about 1 in 20 studies will reject a true $H_0$ by chance
7. ⚠️ **Power depends on effect size and sample size** — small effects require large samples to detect
8. ⚠️ **Reducing variability increases power** — paired designs, better measurements, and blocking all help
9. ⚠️ **Use the correct notation** — hypotheses are about population parameters ($p$, $\mu$), not sample statistics ($\hat{p}$, $\bar{x}$)
10. ⚠️ **Default to two-sided tests** — one-sided tests should only be used when you genuinely have no interest in the opposite direction
