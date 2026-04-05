# Chapter 13: Inference with Mathematical Models

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/foundations-mathematical](https://openintro-ims.netlify.app/foundations-mathematical)

---

## Table of Contents

- [Overview](#overview)
- [13.1 Central Limit Theorem](#131-central-limit-theorem)
- [13.2 Normal Distribution](#132-normal-distribution)
  - [13.2.1 Normal Distribution Model](#1321-normal-distribution-model)
  - [13.2.2 Standardizing with Z Scores](#1322-standardizing-with-z-scores)
  - [13.2.3 Normal Probability Calculations](#1323-normal-probability-calculations)
  - [13.2.4 Normal Probability Examples](#1324-normal-probability-examples)
- [13.3 Quantifying the Variability of a Statistic](#133-quantifying-the-variability-of-a-statistic)
  - [13.3.1 68-95-99.7 Rule](#1331-68-95-997-rule)
  - [13.3.2 Standard Error](#1332-standard-error)
  - [13.3.3 Margin of Error](#1333-margin-of-error)
- [13.4 Case Study (test): Opportunity Cost](#134-case-study-test-opportunity-cost)
  - [13.4.1 Observed Data](#1341-observed-data)
  - [13.4.2 Variability of the Statistic](#1342-variability-of-the-statistic)
  - [13.4.3 Observed Statistic vs. Null Statistics](#1343-observed-statistic-vs-null-statistics)
- [13.5 Case Study (test): Medical Consultant](#135-case-study-test-medical-consultant)
  - [13.5.1 Observed Data](#1351-observed-data)
  - [13.5.2 Variability of the Statistic](#1352-variability-of-the-statistic)
  - [13.5.3 Observed Statistic vs. Null Statistics](#1353-observed-statistic-vs-null-statistics)
  - [13.5.4 Conditions for Applying the Normal Model](#1354-conditions-for-applying-the-normal-model)
- [13.6 Case Study (interval): Stents](#136-case-study-interval-stents)
  - [13.6.1 Observed Data](#1361-observed-data)
  - [13.6.2 Variability of the Statistic](#1362-variability-of-the-statistic)
  - [13.6.3 Interpreting Confidence Intervals](#1363-interpreting-confidence-intervals)
- [13.7 Chapter Review](#137-chapter-review)
  - [13.7.1 Summary](#1371-summary)
  - [13.7.2 Key Terms](#1372-key-terms)
- [13.8 Exercises Overview](#138-exercises-overview)
- [Key Takeaways: Mathematical Models for Inference](#key-takeaways-mathematical-models-for-inference)

---

## Overview

Previous chapters addressed questions about population parameters using computational techniques — randomization tests (permuting data assuming the null hypothesis) and bootstrapping (resampling to measure variability). In many cases, the variability of a statistic can also be described by a mathematical formula. This chapter introduces the **normal distribution** to describe the variability associated with sample proportions from repeated samples or experiments. The normal distribution is powerful because it describes the variability of many different statistics and will be encountered throughout the remainder of the book.

---

## 13.1 Central Limit Theorem

Across the four case studies encountered so far (opportunity cost, sex discrimination, medical consultant, tappers and listeners), the null distributions all share a common shape: symmetric and bell-shaped. This similarity is not a coincidence — it is guaranteed by mathematical theory.

**Sampling distribution:** The distribution of all possible values of a *sample statistic* from samples of a given size from a given population. It describes how sample statistics (e.g., $\hat{p}$, $\bar{x}$) vary from one study to another.

**Null distribution:** The sampling distribution of the statistic created under the setting where the null hypothesis is true. It is always centered at the null value of the parameter.

**Central Limit Theorem for proportions:** If we look at a proportion (or difference in proportions) and the scenario satisfies certain conditions, then the sample proportion will follow a bell-shaped curve called the **normal distribution**.

**Technical conditions for the normal approximation:**

| Condition | Requirement |
|-----------|------------|
| **Independent observations** | Guaranteed by random sampling from a population or random assignment to treatment/control groups |
| **Large enough sample** | For proportions, at least 10 expected successes and 10 expected failures in the sample |

If these conditions do not hold, it is unwise to use the normal distribution (and related concepts like Z scores, normal probabilities, etc.) for inferential analyses.

---

## 13.2 Normal Distribution

The **normal distribution** (also called the normal curve, normal model, or Gaussian distribution) is the most common distribution in statistics. It is symmetric, unimodal, and bell-shaped. Under certain conditions, sample proportions, sample means, and sample differences can be modeled using the normal distribution.

> **Important:** Distributions of many variables are nearly normal, but none are *exactly* normal. The normal distribution, while not perfect for any single problem, is very useful for a variety of problems.

### 13.2.1 Normal Distribution Model

The normal distribution is described by two parameters:

| Parameter | Symbol | Effect |
|-----------|--------|--------|
| **Mean** | $\mu$ | Shifts the bell curve left or right (center) |
| **Standard deviation** | $\sigma$ | Stretches or constricts the curve (spread) |

Notation: $N(\mu, \sigma)$. Examples:
- Standard normal distribution: $N(\mu = 0, \sigma = 1)$
- Normal with mean 19 and SD 4: $N(\mu = 19, \sigma = 4)$

### 13.2.2 Standardizing with Z Scores

The **Z score** of an observation is the number of standard deviations it falls above or below the mean:

$$Z = \frac{x - \mu}{\sigma}$$

| Z Score | Interpretation |
|---------|---------------|
| Positive | Observation is above the mean |
| Negative | Observation is below the mean |
| Zero | Observation equals the mean |

If $x$ comes from a normal distribution $N(\mu, \sigma)$, then the Z score follows a standard normal distribution $N(0, 1)$.

**Comparing unusualness:** An observation $x_1$ is more unusual than $x_2$ if $|Z_1| > |Z_2|$.

**Example — SAT vs. ACT:**
- SAT: $\mu = 1500$, $\sigma = 300$; Nel scored 1800 → $Z = \frac{1800 - 1500}{300} = 1.0$
- ACT: $\mu = 21$, $\sigma = 5$; Sian scored 24 → $Z = \frac{24 - 21}{5} = 0.6$
- Nel performed better relative to their test's distribution.

### 13.2.3 Normal Probability Calculations

Normal probabilities are found using statistical software:

| R Function | Purpose | Mnemonic |
|------------|---------|----------|
| `pnorm(z)` | Find the percentile (area to the left) for a given Z score | "p" for probability |
| `qnorm(p)` | Find the Z score (cutoff) for a given percentile | "q" for quantile/cutoff |

**Example:** The percentile for $Z = 0.43$ is `pnorm(0.43)` = 0.6664 → 66.64th percentile. The Z score for the 80th percentile is `qnorm(0.80)` = 0.84.

**Areas to the right:** Most software and tables give the area to the *left*. To find the area to the right, compute $1 - \text{area to the left}$.

### 13.2.4 Normal Probability Examples

**Key workflow for any normal probability problem:**
1. **Always draw a picture first** — sketch the normal curve, mark the mean, shade the area of interest
2. **Find the Z score** for the cutoff value
3. **Use software or a table** to find the corresponding area

**Finding an observation from a percentile:**
1. Draw the picture and shade the known probability
2. Find the Z score for the percentile using `qnorm()`
3. Solve for $x$ using the Z score formula: $x = \mu + Z \times \sigma$

**Example — US male heights** ($\mu = 70.0"$, $\sigma = 3.3"$):
- Kamron at 67": $Z = \frac{67 - 70}{3.3} = -0.91$ → 18.17th percentile
- Adrian at 76": $Z = \frac{76 - 70}{3.3} = 1.82$ → 96.55th percentile
- Yousef at 40th percentile: $Z = -0.25$ → $x = 70 + (-0.25) \times 3.3 = 69.2"$ (about 5'9")

---

## 13.3 Quantifying the Variability of a Statistic

### 13.3.1 68-95-99.7 Rule

For any normal distribution:

| Range | Probability |
|-------|------------|
| Within 1 SD of the mean ($\mu \pm \sigma$) | ≈ 68% |
| Within 2 SDs of the mean ($\mu \pm 2\sigma$) | ≈ 95% |
| Within 3 SDs of the mean ($\mu \pm 3\sigma$) | ≈ 99.7% |

Values beyond 4 SDs are extremely rare (≈ 1-in-30,000). Beyond 5 SDs: ≈ 1-in-3.5 million. Beyond 6 SDs: ≈ 1-in-1 billion.

### 13.3.2 Standard Error

The **standard error (SE)** is the standard deviation associated with a statistic. It quantifies how much a point estimate varies from sample to sample. Almost always, the SE is itself an estimate, calculated from the sample data.

### 13.3.3 Margin of Error

The **margin of error** describes how far away observations are from their mean. For 95% of observations:

$$\text{Margin of error} = z^* \times SE$$

where $z^*$ is the cutoff value from the normal distribution. The most common value is $z^* = 1.96$ (often approximated as 2), meaning 95% of sampled statistics fall within 1.96 standard errors of the mean.

**Quick approximation:** If you know the range of sample proportions (from lower bound to upper bound), a rough estimate of the SE is $\text{range} / 4$.

---

## 13.4 Case Study (test): Opportunity Cost

### 13.4.1 Observed Data

Recall from [Chapter 11](/foundations-randomization): students became thriftier when reminded that money not spent now could be spent later. The observed difference in purchase rates was 20 percentage points (treatment minus control).

### 13.4.2 Variability of the Statistic

The null distribution from 10,000 randomization simulations is well-approximated by a normal curve with:
- Mean = 0 (under $H_0$)
- Standard error: $SE = 0.078$

### 13.4.3 Observed Statistic vs. Null Statistics

**Z score for the hypothesis test:**

$$Z = \frac{\text{point estimate} - \text{null value}}{SE} = \frac{0.20 - 0}{0.078} = 2.56$$

The right tail area for $Z = 2.56$ is 0.0052, nearly identical to the randomization approach result (0.006). Since p < 0.05, we reject $H_0$ — the treatment impacted students' spending.

**95% confidence interval:**

$$\text{point estimate} \pm 1.96 \times SE = 0.20 \pm 1.96 \times 0.078 = (0.047, 0.353)$$

We are 95% confident that the video purchase rate in the treatment group is between 4.7% and 35.3% lower than in the control group. Since the interval does not contain 0, it is consistent with the hypothesis test conclusion.

---

## 13.5 Case Study (test): Medical Consultant

### 13.5.1 Observed Data

Recall from [Chapter 12](/foundations-bootstrapping): a medical consultant had 3 complications in 62 liver transplant surgeries ($\hat{p} = 0.048$), compared to the national rate of 10%.

### 13.5.2 Variability of the Statistic

The null distribution from simulations has:
- Mean = 0.10 (the null value)
- Standard error: $SE = 0.038$

However, the null distribution is slightly skewed and not particularly smooth — the normal model only *sort of* fits.

### 13.5.3 Observed Statistic vs. Null Statistics

$$Z = \frac{\hat{p} - p_0}{SE} = \frac{0.048 - 0.10}{0.038} = -1.37$$

The left tail area for $Z = -1.37$ is 0.0853. This is the estimated p-value.

**Problem:** The normal model p-value (0.0853) is almost 30% smaller than the simulation p-value (0.1222). The discrepancy is caused by the normal model's poor fit to the skewed, non-smooth null distribution.

### 13.5.4 Conditions for Applying the Normal Model

The technical conditions from [Section 13.1](#131-central-limit-theorem) would have alerted us that the normal model was a poor approximation for this case study. Statistical techniques are like a carpenter's tools — when used responsibly, they produce precise results. When applied under inappropriate conditions, they produce unreliable results. **Conditions should always be checked before applying a technique.**

---

## 13.6 Case Study (interval): Stents

### 13.6.1 Observed Data

An experiment examined whether implanting a stent in the brain of a stroke-risk patient reduces stroke risk. Results from 451 patients over 30 days:

| Group | No Event | Stroke | Total |
|-------|:---:|:---:|:---:|
| **Control** | 214 | 13 | 227 |
| **Treatment** | 191 | 33 | 224 |
| **Total** | 405 | 46 | 451 |

- **Point estimate:** $p_{trmt} - p_{ctrl} = 0.090$
- Surprisingly, patients who received stents had a *higher* risk of stroke.

### 13.6.2 Variability of the Statistic

Conditions for the normal model have been verified. $SE = 0.028$.

**95% confidence interval:**

$$\text{point estimate} \pm 1.96 \times SE = 0.090 \pm 1.96 \times 0.028 = (0.035, 0.145)$$

We are 95% confident that implanting a stent increased the risk of stroke within 30 days by a rate of 0.035 to 0.145. Since the interval does not contain 0, the data provide convincing evidence that the stent changed the risk of stroke.

### 13.6.3 Interpreting Confidence Intervals

**What "95% confident" means:** If we took many samples and constructed a 95% confidence interval from each, about 95% of those intervals would capture the true parameter.

**Important:** A confidence interval only provides a plausible range. Values outside the interval may be implausible based on the data, but this does not mean they are impossible. Just as some observations fall more than 1.96 SDs from the mean, some point estimates fall more than 1.96 SEs from the parameter.

---

## 13.7 Chapter Review

### 13.7.1 Summary

**Mathematical models summary:**

| Question | Answer |
|----------|--------|
| What does it do? | Uses theory (primarily the Central Limit Theorem) to describe the hypothetical variability from repeated randomized experiments or random samples |
| What random process is described? | Randomized experiment or random sampling |
| What other processes can be approximated? | Random sampling in an observational model or random allocation in an experiment |
| What is it best for? | Quick analyses (e.g., calculating a Z score) |
| What physical object represents the simulation? | Not applicable — this is a mathematical approach |

### 13.7.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **95% confidence interval** | $\text{point estimate} \pm 1.96 \times SE$; a range that captures the true parameter about 95% of the time |
| **95% confident** | The long-run success rate of the confidence interval method — about 95% of such intervals capture the true parameter |
| **Central Limit Theorem** | Theory guaranteeing that sample proportions follow a normal distribution under certain conditions |
| **Margin of error** | $z^* \times SE$; the distance from the point estimate to the interval bounds |
| **Normal curve / distribution / model** | A symmetric, unimodal, bell-shaped distribution described by mean ($\mu$) and standard deviation ($\sigma$) |
| **Normal probability table** | A table listing Z scores and corresponding percentiles |
| **Null distribution** | The sampling distribution of a statistic under the assumption that $H_0$ is true |
| **Parameter** | The "true" value of interest in the population |
| **Percentile** | The percentage of observations falling below a given value |
| **Sampling distribution** | The distribution of all possible values of a sample statistic from repeated samples |
| **Standard error (SE)** | The standard deviation of a statistic; quantifies sample-to-sample variability |
| **Standard normal distribution** | The normal distribution with $\mu = 0$ and $\sigma = 1$ |
| **Z score** | The number of standard deviations an observation falls above or below the mean |

---

## 13.8 Exercises Overview

The chapter includes **16 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1–2 | **Area under the curve** | Computing areas/probabilities for regions of the standard normal distribution using Z inequalities |
| 3 | **GRE scores, Z scores** | Computing Z scores, percentiles, and comparing performance across different test distributions |
| 4 | **Triathlon times, Z scores** | Computing Z scores for finishing times; comparing within-group performance; effect of non-normality |
| 5–6 | **GRE/triathlon cutoffs** | Finding cutoff scores/times for given percentiles using `qnorm()` |
| 7 | **LA weather, Fahrenheit** | Computing probabilities and percentiles for temperature data following a normal distribution |
| 8 | **CAPM** | Computing probabilities for portfolio returns modeled as a normal distribution |
| 9 | **LA weather, Celsius** | Converting normal distribution parameters between units; computing IQR from a normal model |
| 10 | **Find the SD** | Back-solving for the standard deviation given a percentile and mean |
| 11 | **Chronic illness** | Constructing and interpreting a 95% CI from a point estimate and SE; true/false statements about CI interpretation |
| 12 | **Social media users, mathematical model** | Constructing a 99% CI; true/false statements about SE, confidence levels, and evidence |
| 13 | **Interpreting a Z score** | Identifying the correct interpretation of a Z score from a hypothesis test |
| 14 | **Mental health** | Interpreting a given 95% CI; understanding "95% confident"; effect of changing confidence level and sample size |
| 15–16 | **Repeated samples** | Identifying sampling distributions; predicting shape; naming variability (SE); comparing variability at different sample sizes |

> Answers to odd-numbered exercises can be found in Appendix A.13 of the textbook.

---

## Key Takeaways: Mathematical Models for Inference

### Core Concepts

| Concept | Key Point |
|---------|-----------|
| **Normal distribution** | $N(\mu, \sigma)$; symmetric, bell-shaped; described by mean and SD |
| **Z score** | $Z = \frac{x - \mu}{\sigma}$; standardizes any normal distribution to $N(0, 1)$ |
| **Standard error** | The SD of a statistic; measures how much the statistic varies from sample to sample |
| **95% CI formula** | $\text{point estimate} \pm 1.96 \times SE$ |
| **Z score in hypothesis testing** | $Z = \frac{\text{point estimate} - \text{null value}}{SE}$ |

### Three Approaches to Inference

| Approach | Method | Best For |
|----------|--------|----------|
| **Randomization** | Shuffle/reassign explanatory variable | Hypothesis testing for experiments |
| **Bootstrapping** | Resample with replacement from observed data | Confidence intervals |
| **Mathematical models** | Use the normal distribution and formulas | Quick analyses when conditions are met |

### Conditions and Warnings

| Rule | Requirement |
|------|------------|
| **68-95-99.7** | ≈68% within 1 SD, ≈95% within 2 SDs, ≈99.7% within 3 SDs |
| **Independence** | Random sampling or random assignment required |
| **Large sample** | For proportions: at least 10 expected successes and 10 expected failures |

### Critical Warnings

1. ⚠️ **Always check conditions before using the normal model** — if observations are not independent or the sample is too small, the normal approximation will be unreliable
2. ⚠️ **Always draw a picture first** — sketching the normal curve and shading the area of interest prevents direction errors (left tail vs. right tail)
3. ⚠️ **The normal model is never exact** — no real-world distribution is perfectly normal, but it is a useful approximation
4. ⚠️ **A poor-fitting normal model produces misleading p-values** — the medical consultant case showed a 30% discrepancy between the normal model p-value and the simulation p-value
5. ⚠️ **Z scores allow comparison across different scales** — Nel's SAT score ($Z = 1.0$) was better relative to peers than Sian's ACT score ($Z = 0.6$), even though the raw scores are on different scales
6. ⚠️ **"95% confident" is about the method, not the specific interval** — any single interval may or may not capture the parameter; 95% refers to the long-run success rate
7. ⚠️ **A CI that excludes 0 is consistent with rejecting $H_0$** — if the 95% CI for a difference does not contain 0, the p-value will be less than 0.05
8. ⚠️ **Values outside a CI are not impossible** — just as some observations fall beyond 2 SDs, some parameters fall outside the CI; the interval provides a *plausible* range, not a definitive boundary
9. ⚠️ **Larger samples reduce the SE** — the SE decreases as sample size increases, leading to narrower confidence intervals and more precise estimates
10. ⚠️ **Statistical techniques are tools that require responsible use** — applying a method under inappropriate conditions produces unreliable results; always verify the conditions outlined for each technique
