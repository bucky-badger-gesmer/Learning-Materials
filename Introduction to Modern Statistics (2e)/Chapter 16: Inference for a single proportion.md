# Chapter 16: Inference for a Single Proportion

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/inference-one-prop](https://openintro-ims.netlify.app/inference-one-prop)

---

## Table of Contents

- [Overview](#overview)
- [16.1 Bootstrap Test for a Proportion](#161-bootstrap-test-for-a-proportion)
- [16.2 Mathematical Model for a Proportion](#162-mathematical-model-for-a-proportion)
  - [16.2.1 Conditions](#1621-conditions)
  - [16.2.2 Confidence Interval for a Proportion](#1622-confidence-interval-for-a-proportion)
  - [16.2.3 Variability of the Sample Proportion](#1623-variability-of-the-sample-proportion)
  - [16.2.4 Changing the Confidence Level](#1624-changing-the-confidence-level)
  - [16.2.5 Hypothesis Test for a Proportion](#1625-hypothesis-test-for-a-proportion)
  - [16.2.6 Violating Conditions](#1626-violating-conditions)
- [16.3 Chapter Review](#163-chapter-review)
  - [16.3.1 Summary](#1631-summary)
  - [16.3.2 Key Terms](#1632-key-terms)
- [16.4 Exercises Overview](#164-exercises-overview)
- [Key Takeaways: Inference for a Single Proportion](#key-takeaways-inference-for-a-single-proportion)

---

## Overview

This chapter focuses on making inferences about a single population proportion $p$ from sample data. Two approaches are covered:

1. **Bootstrap simulation** — a computational method that does not rely on normal approximation; useful when sample sizes are small or conditions for the mathematical model are not met
2. **Mathematical model** — uses the normal distribution to construct confidence intervals and conduct hypothesis tests; requires specific conditions to be satisfied

---

## 16.1 Bootstrap Test for a Proportion

When the conditions for the normal model are not met, we use **parametric bootstrap simulation** to build a null distribution and compute a p-value.

### Case Study: Medical Consultant

A medical consultant claims her clients have fewer complications than the national average. She facilitated 62 liver donor surgeries, and only 3 resulted in complications — compared to a national rate of 10%.

**Observed data:**
- $n = 62$, complications $= 3$
- $\hat{p} = 3/62 = 0.0484$

**Hypotheses:**
- $H_0$: $p = 0.10$ (no association between consultant's work and outcomes)
- $H_A$: $p < 0.10$ (consultant's clients have lower complication rates)

### Simulating the Null Distribution

Rather than relying on the normal approximation (which may be unreliable with small counts), we use **parametric bootstrap simulation**:

1. Assume $H_0$ is true: set the probability of complication to $p_0 = 0.10$
2. Simulate 62 cases by randomly drawing from a population where 10% experience complications
3. Record the resulting sample proportion $\hat{p}_{sim}$
4. Repeat 10,000 times to build the null distribution

**Example simulation result:** One trial yielded 5 complications → $\hat{p}_{sim} = 5/62 = 0.081$

### Computing the P-Value

From 10,000 simulations, 1,170 sample proportions were $\leq 0.0484$:

$$\text{p-value} = \frac{1170}{10{,}000} = 0.117$$

### Conclusion

Since $\text{p-value} = 0.117 > 0.05$, we **fail to reject** $H_0$. The data do not provide convincing evidence that the consultant's complication rate is lower than the national standard.

> **Important caveat:** Failing to reject $H_0$ does not prove the consultant is effective — only that the data do not provide sufficient statistical evidence of improvement.

---

## 16.2 Mathematical Model for a Proportion

When conditions are met, we can use the **normal distribution** as a mathematical model for the sampling distribution of $\hat{p}$.

### 16.2.1 Conditions

Two conditions must be satisfied before applying the mathematical model:

| Condition | Description |
|-----------|-------------|
| **Independence** | Observations are independent (e.g., from a simple random sample) |
| **Success-Failure** | At least 10 expected successes and 10 expected failures: $np \geq 10$ and $n(1-p) \geq 10$ |

When both conditions are met, the sampling distribution of $\hat{p}$ is approximately normal with:
- Mean: $p$
- Standard error: $SE = \sqrt{\dfrac{p(1-p)}{n}}$

---

### 16.2.2 Confidence Interval for a Proportion

**Formula:**

$$\hat{p} \pm z^* \times SE$$

**Standard error for confidence intervals** (use $\hat{p}$ as the best estimate of $p$):

$$SE(\hat{p}) = \sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$$

#### Worked Example: Payday Loan Borrowers

A poll of 826 payday loan borrowers found 70% support new regulations.

- **Check success-failure:** $826(0.70) = 578 \geq 10$ ✓ and $826(0.30) = 248 \geq 10$ ✓
- **SE:** $\sqrt{0.70(0.30)/826} = 0.016$
- **95% CI:** $0.70 \pm 1.96(0.016) = (0.669, 0.731)$

**Interpretation:** We are 95% confident that the true proportion of payday borrowers who supported regulation at the time of the poll was between 0.669 and 0.731.

---

### 16.2.3 Variability of the Sample Proportion

Standard error decreases as sample size increases:

$$SE = \sqrt{\frac{p(1-p)}{n}} \quad \Rightarrow \quad \text{larger } n \Rightarrow \text{ smaller } SE \Rightarrow \text{ narrower CI}$$

---

### 16.2.4 Changing the Confidence Level

Different confidence levels require different critical values $z^*$:

| Confidence Level | $z^*$ |
|:---:|:---:|
| 90% | 1.65 |
| 95% | 1.96 |
| 99% | 2.58 |

The confidence interval formula remains: $\hat{p} \pm z^* \times SE$

---

### 16.2.5 Hypothesis Test for a Proportion

**Test statistic (Z-score):**

$$Z = \frac{\hat{p} - p_0}{\sqrt{p_0(1-p_0)/n}}$$

> **Key difference from CI:** Use the **null value** $p_0$ (not $\hat{p}$) in the standard error calculation for hypothesis tests, because we assume $H_0$ is true.

**Conditions:** Independent observations and $np_0 \geq 10$, $n(1-p_0) \geq 10$

#### Worked Example: Credit Check Regulation

Do a majority of payday borrowers support credit check regulations?

- $H_0$: $p = 0.5$; $H_A$: $p > 0.5$
- Sample: $n = 826$, $\hat{p} = 0.51$
- $SE = \sqrt{0.5(0.5)/826} = 0.017$
- $Z = (0.51 - 0.50)/0.017 = 0.59$
- $\text{p-value} = 0.2776$

**Conclusion:** Fail to reject $H_0$. Insufficient evidence of majority support.

---

### 16.2.6 Violating Conditions

When the success-failure condition fails (too few expected successes or failures), the normal approximation becomes inaccurate and **underestimates variability**.

In these cases, use the bootstrap simulation method from Section 16.1 instead.

---

## 16.3 Chapter Review

### 16.3.1 Summary

With a single categorical variable, randomization testing is not applicable, so computational hypothesis testing uses a **bootstrapping framework** instead. Both bootstrap confidence intervals and mathematical models apply similarly to other parameters and data structures studied throughout the course. Critical considerations:

- Always verify the **success-failure condition** before applying the mathematical model
- Bootstrap accuracy improves with **larger samples**
- For hypothesis tests, use $p_0$ in the SE; for confidence intervals, use $\hat{p}$

### 16.3.2 Key Terms

| Term | Definition |
|------|-----------|
| **Parametric bootstrap** | Simulation method using a hypothesized null parameter value ($p_0$) to generate the null distribution |
| **Success-failure condition** | Requirement that $np \geq 10$ and $n(1-p) \geq 10$ for the normal approximation to be valid |
| **SE for a single proportion** | $\sqrt{\hat{p}(1-\hat{p})/n}$ (for CIs) or $\sqrt{p_0(1-p_0)/n}$ (for hypothesis tests) |
| **Z-score (test statistic)** | Measures how many standard errors the sample proportion deviates from the hypothesized value |

---

## 16.4 Exercises Overview

The chapter includes **30 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Aliens** | Identify errors in hypothesis notation using survey data about extraterrestrial beliefs |
| 2 | **Married at 25** | Critique hypothesis setup for testing a 25% claim (sample of 776, 24% married) |
| 3 | **Defund Police** | Seattle poll analysis (650 respondents, 159 support); write hypotheses, simulate, estimate p-value |
| 4 | **Assisted Reproduction** | Test clinic's 60% success rate ($n=30$) vs. CDC's 48.8% baseline using simulation |
| 5 | **Cat Preference** | Test whether 7 cats show preference between square and Kanizsa illusion (5 chose square) |
| 6 | **Marijuana Legalization** | Test whether 65.3% support ($n=1{,}207$) meets a 55% threshold using null distribution |
| 7 | **Cat Study – Standard Errors** | Compare mathematical model SE to simulated distribution; evaluate success-failure condition |
| 8 | **Marijuana – Standard Errors** | Calculate SE under mathematical model; compare with simulated values |
| 9 | **Student Employment** | Compare two sampling distributions: null (p=0.7) vs. bootstrap from actual data (60% employed) |
| 10 | **Health Plan** | Use simulated null distribution to test whether 55% of 617 Independents constitutes majority |
| 11 | **Employment – Bootstrap** | Identify when to use null distribution vs. bootstrap distribution for tests vs. intervals |
| 12 | **CLT for Proportions** | Describe shape, center, and spread of sampling distribution as sample size grows ($p=0.1$) |
| 13 | **Vegetarian Students** | Evaluate true/false statements about sampling distributions ($p=0.08$) at various $n$ |
| 14 | **American Dream** | Assess claims about sampling distributions (77% of young adults believe in the American dream) |
| 15 | **Orange Tabbies** | Evaluate statements about sample proportion distributions (90% of orange tabbies are male) |
| 16 | **Starting Family** | Evaluate claims about sampling distributions (25% delayed family-starting due to economy) |
| 17 | **Sex Equality** | Analyze GSS data ($n=1{,}390$, 82% support, ME=2%, 95% CI); assess true/false interpretations |
| 18 | **Elderly Drivers** | Verify margin of error in Marist Poll; test whether evidence supports majority (>67%) |
| 19 | **July 4 Fireworks** | Calculate margin of error for 56% estimate from 600 Kansas residents at 95% confidence |
| 20 | **COVID Vaccination** | Gallup survey ($n=3{,}731$, 57% favor vaccine requirement); construct 95% CI, discuss sample size |
| 21 | **Study Abroad** | Evaluate sample representativeness ($n=1{,}509$, 55%); construct 90% CI; assess majority claim |
| 22 | **Marijuana Legalization** | Determine if 60% is statistic or parameter ($n=1{,}563$); build 95% CI; assess news claim |
| 23 | **National Health Plan** | Test whether 55% of 617 surveyed Independents constitutes majority; predict CI for opposition |
| 24 | **College Worth** | Test whether 48% minority claim holds ($n=331$); predict CI inclusion of 0.5 |
| 25 | **Taste Test** | Test whether 53 of 80 participants correctly identified soda (better than chance) |
| 26 | **Coronavirus Impact** | Construct 90% CI for 12% believing coronavirus brings world closer ($n=4{,}265$); find required $n$ |
| 27 | **Quality Control** | Analyze chip defect rate (27 of 212); compute SE; compare to 10% historical rate |
| 28 | **Nearsighted Children** | Test 8% prevalence claim using mathematical model (21 of 194 nearsighted) |
| 29 | **Website Registration** | Check CI conditions; compute SE; construct 90% CI for first-time visitor registration (64 of 752) |
| 30 | **Coupon Effectiveness** | Build 95% CI for proportion of shoppers whose visit resulted from mail coupon (142 of 603) |

> Answers to odd-numbered exercises can be found in Appendix A.16 of the textbook.

---

## Key Takeaways: Inference for a Single Proportion

### Two Methods

| Method | When to Use | Key Tool |
|--------|-------------|----------|
| **Parametric Bootstrap** | Success-failure condition not met; small samples | Simulate null distribution using $p_0$ |
| **Mathematical Model** | Both conditions satisfied | Normal distribution, Z-score |

### Standard Error — Which $p$ to Use?

| Context | Formula | Use |
|---------|---------|-----|
| **Confidence interval** | $\sqrt{\hat{p}(1-\hat{p})/n}$ | $\hat{p}$ (sample estimate) |
| **Hypothesis test** | $\sqrt{p_0(1-p_0)/n}$ | $p_0$ (null value) |

### Conditions for Mathematical Model

| Condition | Requirement |
|-----------|-------------|
| **Independence** | Simple random sample or equivalent |
| **Success-Failure** | $np \geq 10$ AND $n(1-p) \geq 10$ |

### Common $z^*$ Critical Values

| Confidence Level | $z^*$ |
|:---:|:---:|
| 90% | 1.65 |
| 95% | 1.96 |
| 99% | 2.58 |

### Critical Warnings

1. ⚠️ **Use $p_0$ (not $\hat{p}$) in the SE for hypothesis tests** — we assume $H_0$ is true when computing the test statistic
2. ⚠️ **Check success-failure before applying the normal model** — use bootstrap simulation when the condition fails
3. ⚠️ **Failing to reject $H_0$ ≠ proving $H_0$** — the data may simply lack sufficient power to detect an effect
4. ⚠️ **Bootstrap accuracy depends on sample size** — results are less reliable with very small samples
5. ⚠️ **Hypotheses are about population parameters ($p$), not sample statistics ($\hat{p}$)** — never write $H_0: \hat{p} = 0.10$
6. ⚠️ **Larger samples produce narrower CIs** — more data ⟹ smaller SE ⟹ more precision
7. ⚠️ **A 95% CI will miss the true parameter 5% of the time** — this is expected behavior, not an error
