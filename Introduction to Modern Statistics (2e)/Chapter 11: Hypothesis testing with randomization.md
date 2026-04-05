# Chapter 11: Hypothesis Testing with Randomization

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/foundations-randomization](https://openintro-ims.netlify.app/foundations-randomization)

---

## Table of Contents

- [Overview](#overview)
- [11.1 Sex Discrimination Case Study](#111-sex-discrimination-case-study)
  - [11.1.1 Observed Data](#1111-observed-data)
  - [11.1.2 Variability of the Statistic](#1112-variability-of-the-statistic)
  - [11.1.3 Observed Statistic vs. Null Statistics](#1113-observed-statistic-vs-null-statistics)
- [11.2 Opportunity Cost Case Study](#112-opportunity-cost-case-study)
  - [11.2.1 Observed Data](#1121-observed-data)
  - [11.2.2 Variability of the Statistic](#1122-variability-of-the-statistic)
  - [11.2.3 Observed Statistic vs. Null Statistics](#1123-observed-statistic-vs-null-statistics)
- [11.3 Hypothesis Testing](#113-hypothesis-testing)
  - [11.3.1 The US Court System](#1131-the-us-court-system)
  - [11.3.2 p-value and Statistical Discernibility](#1132-p-value-and-statistical-discernibility)
- [11.4 Chapter Review](#114-chapter-review)
  - [11.4.1 Summary](#1141-summary)
  - [11.4.2 Key Terms](#1142-key-terms)
- [11.5 Exercises Overview](#115-exercises-overview)
- [Key Takeaways: Hypothesis Testing with Randomization](#key-takeaways-hypothesis-testing-with-randomization)

---

## Overview

Statistical inference is concerned with understanding and quantifying the uncertainty of parameter estimates. While equations and details change depending on the setting, the foundations for inference are the same throughout all of statistics. This chapter introduces the **hypothesis testing framework** through two case studies, formalizing the process of evaluating competing claims about a population using **randomization tests**. The key idea: if the observed data would be very unlikely to occur by chance alone under a null hypothesis, we reject the null in favor of the alternative.

---

## 11.1 Sex Discrimination Case Study

**Research question:** Are individuals who identify as female discriminated against in promotion decisions made by managers who identify as male? (Rosen and Jerdee 1974)

### 11.1.1 Observed Data

48 male bank supervisors at a management institute were asked to judge identical personnel files for a branch manager promotion, except half indicated the candidate identified as male and half as female. Files were randomly assigned.

| | Promoted | Not Promoted | Total |
|---|:---:|:---:|:---:|
| **Male** | 21 | 3 | 24 |
| **Female** | 14 | 10 | 24 |
| **Total** | 35 | 13 | 48 |

- **Promotion rate (male):** 21/24 = 87.5%
- **Promotion rate (female):** 14/24 = 58.3%
- **Observed difference:** 87.5% − 58.3% = **29.2 percentage points**

This is an **experiment** (random assignment of files), so results can be used to evaluate a causal relationship between candidate sex and promotion decisions.

**Competing claims:**

| Hypothesis | Statement |
|------------|-----------|
| **$H_0$ (Null)** | `sex` and `decision` are independent. The 29.2% difference was due to natural variability (chance). |
| **$H_A$ (Alternative)** | `sex` and `decision` are *not* independent. Equally qualified female personnel are less likely to be promoted than male personnel. |

### 11.1.2 Variability of the Statistic

To simulate what would happen if the null hypothesis were true (no discrimination), we shuffle the 48 personnel files (35 promoted, 13 not promoted) and randomly reassign them into two groups of 24. Any difference in promotion rates between these simulated groups is due to chance alone.

**One simulated result:**

| | Promoted | Not Promoted | Total |
|---|:---:|:---:|:---:|
| **Male** | 18 | 6 | 24 |
| **Female** | 17 | 7 | 24 |
| **Total** | 35 | 13 | 48 |

- Simulated difference: 18/24 − 17/24 = **4.2%** — much smaller than the observed 29.2%

### 11.1.3 Observed Statistic vs. Null Statistics

Repeating the simulation 100 times produces a distribution of differences under the null hypothesis. The distribution is centered around 0 (since under $H_0$, sex has no effect).

| Result | Count |
|--------|:---:|
| Simulated differences ≥ 29.2% | 2 out of 100 |
| Probability (p-value) | ≈ 0.02 |

A difference of at least 29.2% would only happen about **2% of the time** by chance alone under the null hypothesis. This is rare enough to reject $H_0$ and conclude the data provide strong evidence of sex discrimination against female candidates.

---

## 11.2 Opportunity Cost Case Study

**Research question:** Does reminding students that money not spent now can be spent later reduce the chance they will continue with a purchase? (Frederick et al. 2009)

### 11.2.1 Observed Data

150 students were given a scenario about buying a $14.99 video. Half were in a control group; the other half (treatment) saw an additional reminder: "Keep the $14.99 for other purchases."

| Group | Buy Video | Not Buy Video | Total |
|-------|:---:|:---:|:---:|
| **Control** | 56 | 19 | 75 |
| **Treatment** | 41 | 34 | 75 |
| **Total** | 97 | 53 | 150 |

| Group | Buy Video | Not Buy Video |
|-------|:---:|:---:|
| **Control** | 74.7% | 25.3% |
| **Treatment** | 54.7% | 45.3% |

- **Point estimate of difference:** 45.3% − 25.3% = **20.0 percentage points**

**Hypotheses:**

| Hypothesis | Statement |
|------------|-----------|
| **$H_0$** | The reminder has no impact on students' spending decisions. |
| **$H_A$** | The reminder reduces the chance students will continue with a purchase. |

### 11.2.2 Variability of the Statistic

Under $H_0$, the treatment had no effect, so the 20% difference is attributed to random chance. We simulate by shuffling 150 cards (97 "buy", 53 "not buy") and splitting into two groups of 75.

### 11.2.3 Observed Statistic vs. Null Statistics

Repeating the simulation 1,000 times:

- The null distribution is centered around 0
- Differences of at least +20% occurred in **6 out of 1,000** simulations
- **p-value ≈ 0.006**

This is extremely rare under the null hypothesis. We reject $H_0$ and conclude the data provide strong evidence that the reminder lowers purchase rates. Because this is an experiment, we can make a **causal statement** — the reminder causes the change in behavior.

---

## 11.3 Hypothesis Testing

### 11.3.1 The US Court System

The hypothesis testing framework mirrors the US court system:

| Court System | Hypothesis Testing |
|-------------|-------------------|
| Defendant is innocent until proven guilty | $H_0$ is assumed true until evidence contradicts it |
| Jury evaluates evidence for guilt | Data are evaluated for evidence against $H_0$ |
| "Not guilty" ≠ proven innocent | Failing to reject $H_0$ ≠ accepting $H_0$ as true |

> **Key principle:** Failing to find evidence in favor of the alternative hypothesis is **not** equivalent to finding evidence that the null hypothesis is true.

### 11.3.2 p-value and Statistical Discernibility

**p-value:** The probability of observing data at least as favorable to the alternative hypothesis as the current dataset, if the null hypothesis were true.

| Concept | Definition |
|---------|-----------|
| **Test statistic** | A summary value (e.g., difference in proportions) used to compute the p-value |
| **Discernibility level ($\alpha$)** | A predetermined threshold for rejecting $H_0$; historically 0.05 in many fields |
| **Statistically discernible** | Results where p-value < $\alpha$; data provide strong enough evidence to reject $H_0$ |

**Case study p-values:**

| Study | Observed Difference | p-value | Discernible at $\alpha = 0.05$? |
|-------|--------------------|:---:|:---:|
| Sex discrimination | 29.2% | ≈ 0.02 | ✅ Yes |
| Opportunity cost | 20.0% | ≈ 0.006 | ✅ Yes |

> ⚠️ **"Statistically discernible" ≠ "large or meaningful."** The term only indicates that the p-value fell below the chosen threshold — it says nothing about the practical size of the effect.

---

## 11.4 Chapter Review

### 11.4.1 Summary

The **randomization test procedure** follows five steps:

| Step | Description |
|------|-------------|
| **1. Frame hypotheses** | Define $H_0$ (no effect / no relationship) and $H_A$ (effect / relationship exists) |
| **2. Collect data** | Use an observational study (for associations) or experiment (for causal claims) |
| **3. Model randomness under $H_0$** | Simulate what the data would look like if the null hypothesis were true by shuffling/reassigning |
| **4. Analyze the data** | Compute the p-value from the null distribution |
| **5. Form a conclusion** | If p-value < $\alpha$, reject $H_0$; write the conclusion in plain language |

**Randomization test summary:**

| Question | Answer |
|----------|--------|
| What does it do? | Shuffles the explanatory variable to mimic natural variability in a randomized experiment |
| What random process is described? | Randomized experiment |
| What other processes can be approximated? | Random sampling in an observational model |
| What is it best for? | Hypothesis testing |
| What physical object represents the simulation? | Shuffling cards |

### 11.4.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Alternative hypothesis ($H_A$)** | The claim representing a new view, effect, or relationship between variables |
| **Discernibility level ($\alpha$)** | The threshold below which the p-value leads to rejection of $H_0$ |
| **Hypothesis test** | A statistical technique used to evaluate two competing claims using data |
| **Independent** | Two variables with no relationship; changes in one do not affect the other |
| **Null hypothesis ($H_0$)** | The skeptical perspective or claim of "no difference" / "no effect" |
| **Permutation test** | A randomization test applied to observational studies (where the explanatory variable was not randomly assigned) |
| **p-value** | Probability of observing data at least as extreme as the observed data, assuming $H_0$ is true |
| **Point estimate** | A single value computed from sample data used to estimate a population parameter |
| **Randomization test** | A simulation technique that shuffles the explanatory variable to model variability under $H_0$ |
| **Simulation** | A computer-generated approximation of a random process |
| **Statistical inference** | Making decisions and conclusions from data in the context of uncertainty |
| **Statistically discernible** | Results where the p-value is less than the chosen $\alpha$ threshold |
| **Statistically significant** | Synonym for "statistically discernible" (used in many texts) |
| **Statistic** | A numerical summary calculated from sample data |
| **Success** | The outcome of interest in a study (may or may not be a positive outcome) |
| **Test statistic** | The summary value (e.g., difference in proportions) used to compute the p-value |

---

## 11.5 Exercises Overview

The chapter includes **8 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1–2 | **Identifying parameters** | Distinguishing whether the parameter of interest is a mean or a proportion |
| 3 | **Classifying hypotheses** | Identifying whether research statements represent null or alternative hypothesis claims |
| 4 | **True null hypothesis** | Understanding the relationship between $\alpha$ and the probability of mistakenly rejecting a true $H_0$ |
| 5–6 | **Writing hypotheses** | Formulating null and alternative hypotheses in words and symbols for real-world scenarios |
| 7 | **Side effects of Avandia** | Evaluating true/false statements about contingency tables; computing expected counts; interpreting randomization histograms |
| 8 | **Heart transplants** | Interpreting stacked bar plots; computing proportions; setting up a randomization test (fill-in-the-blank); interpreting simulation results |

> Answers to odd-numbered exercises can be found in Appendix A.11 of the textbook.

---

## Key Takeaways: Hypothesis Testing with Randomization

### The Hypothesis Testing Framework

| Concept | Key Point |
|---------|-----------|
| **Null hypothesis ($H_0$)** | Represents "no effect" or "no difference"; the skeptical perspective |
| **Alternative hypothesis ($H_A$)** | Represents the effect or relationship the researchers are trying to demonstrate |
| **p-value** | Probability of the observed (or more extreme) result occurring by chance alone if $H_0$ is true |
| **Statistical discernibility** | p-value < $\alpha$ means we reject $H_0$ in favor of $H_A$ |

### The Randomization Test

| Step | What Happens |
|------|-------------|
| **Shuffle** | Randomly reassign the outcome variable across groups, assuming $H_0$ is true |
| **Compute** | Calculate the test statistic (e.g., difference in proportions) for each shuffle |
| **Repeat** | Do this hundreds or thousands of times to build a null distribution |
| **Compare** | See how often the simulated statistic is as extreme as the observed statistic |
| **Conclude** | If the observed result is rare under $H_0$, reject $H_0$ |

### Critical Warnings

1. ⚠️ **Failing to reject $H_0$ ≠ accepting $H_0$** — absence of evidence is not evidence of absence
2. ⚠️ **"Statistically discernible" ≠ "large or important"** — a tiny effect can be statistically discernible with a large enough sample
3. ⚠️ **$\alpha = 0.05$ is a convention, not a law** — the threshold can and should vary depending on context and consequences of errors
4. ⚠️ **Randomization tests require random assignment** — for observational studies, the analogous technique is called a permutation test
5. ⚠️ **Causal claims require experiments** — only randomized experiments support causal conclusions; observational studies can only show associations
6. ⚠️ **The p-value assumes $H_0$ is true** — it does not tell you the probability that $H_0$ is true or false
7. ⚠️ **Rare events happen** — even under $H_0$, about 1 in 20 tests will produce p < 0.05 by chance alone (this is what $\alpha = 0.05$ means)
8. ⚠️ **Define "success" carefully** — in statistics, "success" simply means the outcome of interest, which may be a negative event (e.g., having a disease)
