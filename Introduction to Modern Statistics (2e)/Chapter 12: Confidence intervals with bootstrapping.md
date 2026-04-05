# Chapter 12: Confidence Intervals with Bootstrapping

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/foundations-bootstrapping](https://openintro-ims.netlify.app/foundations-bootstrapping)

---

## Table of Contents

- [Overview](#overview)
- [12.1 Medical Consultant Case Study](#121-medical-consultant-case-study)
  - [12.1.1 Observed Data](#1211-observed-data)
  - [12.1.2 Variability of the Statistic](#1212-variability-of-the-statistic)
- [12.2 Tappers and Listeners Case Study](#122-tappers-and-listeners-case-study)
  - [12.2.1 Observed Data](#1221-observed-data)
  - [12.2.2 Variability of the Statistic](#1222-variability-of-the-statistic)
- [12.3 Confidence Intervals](#123-confidence-intervals)
  - [12.3.1 Plausible Range of Values for the Population Parameter](#1231-plausible-range-of-values-for-the-population-parameter)
  - [12.3.2 Bootstrap Confidence Interval](#1232-bootstrap-confidence-interval)
- [12.4 Chapter Review](#124-chapter-review)
  - [12.4.1 Summary](#1241-summary)
  - [12.4.2 Key Terms](#1242-key-terms)
- [12.5 Exercises Overview](#125-exercises-overview)
- [Key Takeaways: Bootstrapping and Confidence Intervals](#key-takeaways-bootstrapping-and-confidence-intervals)

---

## Overview

This chapter introduces **confidence intervals** — a range of plausible values where the true population parameter may lie. Instead of testing a claim (as in hypothesis testing), the goal is to estimate the unknown value of a population parameter. The technique used is **bootstrapping**, a simulation method that models how a statistic varies from one sample to another by resampling from the observed data with replacement. Bootstrapping is best suited for modeling studies where data have been generated through random sampling from a population.

---

## 12.1 Medical Consultant Case Study

**Research question:** What is the true complication rate for liver donors working with a specific medical consultant?

### 12.1.1 Observed Data

A medical consultant facilitated 62 liver donor surgeries, with only 3 complications. The national average complication rate is about 10%.

- **Sample proportion:** $\hat{p} = 3/62 = 0.048$ (4.8%)

| Concept | Value |
|---------|-------|
| **Parameter ($p$)** | The true complication rate for the consultant's clients (unknown) |
| **Statistic ($\hat{p}$)** | The observed complication rate: 3/62 = 0.048 |

> ⚠️ **Causal claim limitation:** The data are observational, so we cannot assess whether the consultant's work *causes* the lower complication rate. Confounding variables (e.g., patients who can afford a consultant may also afford better medical care) could explain the difference. However, we can still estimate the consultant's true complication rate.

### 12.1.2 Variability of the Statistic

There is no reason to believe $p$ is exactly $\hat{p} = 0.048$, but also no reason to believe it is particularly far from it. To understand the variability of $\hat{p}$, we use **bootstrapping** — sampling with replacement from the observed data.

**The bootstrap process:**
1. Treat the observed sample as a proxy for the unknown population
2. Draw a new sample of the same size (62) **with replacement** from the observed data
3. Compute the proportion of complications ($\hat{p}_{boot}$) for this resample
4. Repeat thousands of times

**Key insight:** The variability of $\hat{p}_{boot}$ around $\hat{p}$ approximates the variability of different sample proportions ($\hat{p}$) around the true parameter ($p$).

**Results — 10,000 bootstrap simulations:**

| Statistic | Value |
|-----------|-------|
| 2.5th percentile | 0.000 |
| 97.5th percentile | 0.113 |
| **95% bootstrap percentile CI** | **(0%, 11.3%)** |

**Interpretation:** We are confident that the true probability of a complication for this consultant's clients falls somewhere between 0% and 11.3%.

**Does this support the consultant's claim of being below the 10% national average?** No. Because the interval overlaps 10%, the consultant's true rate could be lower than, equal to, or higher than the national average. The data do not provide convincing evidence of a lower rate.

---

## 12.2 Tappers and Listeners Case Study

**Research question:** What is the true proportion of people who can guess the tapper's tune? (Newton 1990)

### 12.2.1 Observed Data

Elizabeth Newton recruited 120 tappers and 120 listeners. About 50% of tappers expected listeners to guess the song. In reality, only 3 out of 120 listeners guessed correctly.

- **Sample proportion:** $\hat{p} = 3/120 = 0.025$ (2.5%)

### 12.2.2 Variability of the Statistic

Using the same bootstrap procedure — a bag of 120 marbles (3 red for correct, 117 white for incorrect), sampling 120 times with replacement — repeated 10,000 times:

| Statistic | Value |
|-----------|-------|
| 2.5th percentile | 0.000 |
| 97.5th percentile | 0.0583 |
| **95% bootstrap percentile CI** | **(0%, 5.83%)** |

**Interpretation:** We are confident that between 0% and 5.83% of people are truly able to guess the tapper's tune.

**Does this provide evidence against the claim that 50% of listeners can guess the tune?** Yes. Because 50% is far outside the interval (0%, 5.83%), there is very convincing evidence against this hypothesis.

---

## 12.3 Confidence Intervals

### 12.3.1 Plausible Range of Values for the Population Parameter

A **point estimate** provides a single plausible value for a parameter, but it is rarely perfect — there is always some error. A **confidence interval** provides a plausible *range of values* for the parameter.

| Analogy | Point Estimate | Confidence Interval |
|---------|---------------|-------------------|
| **Fishing** | Throwing a spear at a fish | Casting a net in the area |
| **Certainty** | Likely to miss | Good chance of catching the fish |

**Trade-off:** Wider intervals (e.g., 99% confidence) are more certain to capture the parameter but are less precise. Narrower intervals (e.g., 80% confidence) are more precise but less certain.

### 12.3.2 Bootstrap Confidence Interval

**Bootstrap sample:** A sample of the original sample, drawn **with replacement**. Each observation has a $1/n$ chance of being selected at each draw, meaning some observations may appear multiple times in a bootstrap sample while others may not appear at all.

**95% bootstrap percentile confidence interval:**

1. Generate many (e.g., 10,000) bootstrap samples
2. Compute $\hat{p}_{boot}$ for each
3. Sort the $\hat{p}_{boot}$ values
4. The 2.5th percentile is the **lower bound**
5. The 97.5th percentile is the **upper bound**
6. The 95% confidence interval is (lower, upper)

> **What "95% confidence" means:** Over a researcher's lifetime, 95% of the confidence intervals they construct using this method will capture the true population parameter. Any single interval may or may not capture the parameter, but the method is reliable in the long run.

---

## 12.4 Chapter Review

### 12.4.1 Summary

The **bootstrap process** follows five steps:

| Step | Description |
|------|-------------|
| **1. Frame the research question** | Define the parameter to estimate; confidence intervals are appropriate for questions about estimating a population number |
| **2. Collect data** | Use an observational study or experiment to calculate a statistic as the best guess for the parameter |
| **3. Model randomness** | Take repeated resamples from the dataset (with replacement) to measure variability in bootstrapped statistics |
| **4. Create the interval** | Choose a confidence level and use the variability of bootstrapped statistics to create an interval estimate |
| **5. Form a conclusion** | Report the interval estimate in plain language |

**Bootstrapping summary:**

| Question | Answer |
|----------|--------|
| What does it do? | Resamples (with replacement) from the observed data to mimic sampling variability |
| What random process is described? | Random sampling from a population |
| What other processes can be approximated? | Random allocation in an experiment |
| What is it best for? | Confidence intervals (can also be used for hypothesis testing for one proportion) |
| What physical object represents the simulation? | Pulling marbles from a bag **with replacement** |

### 12.4.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Bootstrap percentile confidence interval** | An interval estimate constructed using the percentiles of the bootstrap distribution |
| **Bootstrap sample** | A resample of the same size as the original data, drawn with replacement |
| **Bootstrapping** | A simulation technique that resamples from the observed data to estimate the variability of a statistic |
| **Confidence interval** | A plausible range of values for the population parameter |
| **Parameter** | The "true" value of interest in the population (e.g., $p$, $\mu$) |
| **Point estimate** | A single value computed from sample data used to estimate a parameter (also called a statistic) |
| **Sampling with replacement** | Each draw is returned to the pool before the next draw, keeping the population constant |
| **Statistic** | A numerical summary calculated from sample data; used to estimate a parameter |

---

## 12.5 Exercises Overview

The chapter includes **8 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Outside YouTube videos** | Identifying statistic vs. parameter; notation; bootstrap center; estimating and interpreting a 90% CI from a histogram |
| 2 | **Chronic illness** | Approximating a 92% CI from a bootstrap distribution; interpreting the interval in context |
| 3 | **Social media users and news** | Approximating a 98% CI from a bootstrap distribution; interpreting in context |
| 4 | **Bootstrap distributions of $\hat{p}$, I** | Matching bootstrap histograms to original sample proportions ($\hat{p} = 0.13, 0.22, 0.30, 0.43$) |
| 5 | **Bootstrap distributions of $\hat{p}$, II** | Determining which datasets could plausibly come from given population $p$ values |
| 6 | **Bootstrap distributions of $\hat{p}$, III** | Understanding how sample size ($n = 10, 100, 500, 1000$) affects bootstrap distribution spread |
| 7 | **Cyberbullying rates** | Evaluating claims against a given 95% CI (54%–64%); reasoning about how CI width changes with confidence level |
| 8 | **Waiting at an ER** | Evaluating claims against a given 95% CI for a mean (128–147 minutes); reasoning about 99% CI width |

> Answers to odd-numbered exercises can be found in Appendix A.12 of the textbook.

---

## Key Takeaways: Bootstrapping and Confidence Intervals

### Core Concepts

| Concept | Key Point |
|---------|-----------|
| **Point estimate** | A single best guess for the parameter (e.g., $\hat{p} = 3/62$) |
| **Confidence interval** | A range of plausible values for the parameter |
| **Bootstrap distribution** | The distribution of statistics from many resamples; centered at the observed statistic |
| **95% CI** | The middle 95% of the bootstrap distribution (2.5th to 97.5th percentile) |

### Bootstrap vs. Randomization

| Feature | Bootstrapping | Randomization |
|---------|--------------|---------------|
| **Purpose** | Estimating a parameter (confidence intervals) | Testing a claim (hypothesis testing) |
| **Process** | Resample **with replacement** from the observed data | Shuffle/reassign the explanatory variable |
| **Center of distribution** | Centered at the observed statistic ($\hat{p}$) | Centered at the null value (e.g., 0 difference) |
| **Physical analogy** | Pulling marbles from a bag **with replacement** | Shuffling cards and re-dealing into groups |
| **Best for** | Random sampling from a population | Randomized experiments |

### Critical Warnings

1. ⚠️ **Bootstrapping estimates variability, not the parameter itself** — the bootstrap distribution tells us how much $\hat{p}$ varies, not what $p$ is
2. ⚠️ **The bootstrap distribution is centered at $\hat{p}$, not $p$** — but the *spread* of the bootstrap distribution approximates the spread of $\hat{p}$ around $p$
3. ⚠️ **Sampling with replacement is essential** — without replacement, you would just recreate the original sample every time
4. ⚠️ **Observational data cannot support causal claims** — even if a confidence interval excludes a national average, it does not prove causation
5. ⚠️ **A CI that overlaps a reference value provides no convincing evidence of a difference** — if the interval includes the national average, the true rate could be the same
6. ⚠️ **A value far outside the CI provides strong evidence against it** — if 50% is far outside (0%, 5.83%), the claim is very unlikely
7. ⚠️ **Larger sample sizes produce narrower CIs** — more data means less variability in the statistic, leading to a more precise interval
8. ⚠️ **Higher confidence levels produce wider CIs** — being more certain requires a wider net
9. ⚠️ **"95% confidence" is about the method, not the specific interval** — any single interval may or may not capture the parameter; 95% refers to the long-run success rate of the procedure
10. ⚠️ **Bootstrap CIs work for almost any statistic** — not just proportions; bootstrapping can estimate variability for medians, regression coefficients, and other statistics without simple mathematical theory
