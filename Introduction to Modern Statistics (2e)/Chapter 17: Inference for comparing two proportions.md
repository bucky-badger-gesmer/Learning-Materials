# Chapter 17: Inference for Comparing Two Proportions

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/inference-two-props](https://openintro-ims.netlify.app/inference-two-props)

---

## Table of Contents

- [Overview](#overview)
- [17.1 Randomization Test for Difference in Proportions](#171-randomization-test-for-difference-in-proportions)
- [17.2 Bootstrap Confidence Intervals for Difference in Proportions](#172-bootstrap-confidence-intervals-for-difference-in-proportions)
- [17.3 Mathematical Model for Difference in Proportions](#173-mathematical-model-for-difference-in-proportions)
- [17.4 Chapter Review](#174-chapter-review)
  - [17.4.1 Summary](#1741-summary)
  - [17.4.2 Key Terms](#1742-key-terms)
- [17.5 Exercises Overview](#175-exercises-overview)
- [Key Takeaways: Inference for Comparing Two Proportions](#key-takeaways-inference-for-comparing-two-proportions)

---

## Overview

This chapter extends the inference methods developed for a single proportion to the comparison of two proportions. The quantity of interest is the difference $p_1 - p_2$, estimated by the point estimate $\hat{p}_1 - \hat{p}_2$. Three computational approaches are covered: randomization tests (to assess whether the observed difference could plausibly arise by chance), bootstrap confidence intervals (to quantify uncertainty around the estimated difference), and mathematical modeling using the normal distribution (for large samples meeting specific conditions).

---

## 17.1 Randomization Test for Difference in Proportions

### Case Study: CPR and Blood Thinners

An experiment examined whether blood thinners help or harm patients who underwent CPR for a heart attack. 90 patients were randomly assigned to treatment (blood thinner, $n = 40$) or control (no blood thinner, $n = 50$).

| Group | Died | Survived | Total |
|-------|:---:|:---:|:---:|
| **Control** | 39 | 11 | 50 |
| **Treatment** | 26 | 14 | 40 |
| **Total** | 65 | 25 | 90 |

**Observed proportions:**
- Control survival: $\hat{p}_C = 11/50 = 0.22$
- Treatment survival: $\hat{p}_T = 14/40 = 0.35$
- Point estimate: $\hat{p}_T - \hat{p}_C = 0.13$

**Hypotheses:**
- $H_0$: Blood thinners have no overall survival effect. $p_T - p_C = 0$
- $H_A$: Blood thinners have an impact on survival (positive or negative). $p_T - p_C \neq 0$

### Randomization Procedure

Under $H_0$, the treatment label is irrelevant — survival outcomes are independent of which group a patient was in. To build the null distribution:

1. Pool all 90 patient outcomes (25 survived, 65 died)
2. Randomly reassign outcomes to treatment ($n = 40$) and control ($n = 50$) groups
3. Compute the simulated difference $\hat{p}_T - \hat{p}_C$
4. Repeat 1,000 times to build the null distribution

**Result:** A difference of at least 0.13 occurred in approximately 12% of simulations.

**Two-sided p-value:** $\approx 0.262$

Since $0.262 > 0.05$, we fail to reject $H_0$. The data do not provide convincing evidence that blood thinners influence survival after CPR.

> **Key insight:** The randomization distribution is centered at 0 (the null hypothesis value) and reflects what differences we would expect purely by chance if there were no real effect.

---

## 17.2 Bootstrap Confidence Intervals for Difference in Proportions

Bootstrap methods estimate the variability of $\hat{p}_1 - \hat{p}_2$ without assumptions about the underlying distribution.

### Bootstrap Procedure

1. From group 1 (size $n_1$), draw a bootstrap sample of size $n_1$ with replacement; compute $\hat{p}_1^*$
2. From group 2 (size $n_2$), draw a bootstrap sample of size $n_2$ with replacement; compute $\hat{p}_2^*$
3. Record the bootstrap difference $\hat{p}_1^* - \hat{p}_2^*$
4. Repeat 1,000+ times to build the bootstrap distribution

> **Key difference from randomization:** Bootstrap samples are drawn from each group independently; randomization pools all observations and re-shuffles labels.

### Two Methods for Bootstrap CIs

| Method | Formula | Example (90% CI) |
|--------|---------|-----------------|
| **Percentile interval** | 5th and 95th percentiles of bootstrap distribution | $(-0.032,\ 0.284)$ |
| **SE interval** | $(\hat{p}_1 - \hat{p}_2) \pm z^* \times SE_{boot}$ | $\approx (-0.067,\ 0.327)$ for 95% |

**SE interval formula:**
$$(\hat{p}_1 - \hat{p}_2) \pm z^* \times SE_{boot}$$

where $SE_{boot}$ is the standard deviation of the bootstrap distribution of differences.

### Interpreting Confidence Level

> **Correct interpretation:** If we repeated the study many times and computed a 95% CI each time, approximately 95% of those intervals would capture the true parameter $p_1 - p_2$. We cannot say the single interval at hand has a 95% probability of containing the true value — the true value is fixed, not random.

---

## 17.3 Mathematical Model for Difference in Proportions

For sufficiently large samples, $\hat{p}_1 - \hat{p}_2$ follows an approximately normal distribution.

### Conditions

Two conditions must be satisfied:

1. **Independence:** Observations within each group and between groups must be independent. (Usually satisfied by random sampling or random assignment.)
2. **Success-failure condition:** Each group must have at least 10 successes and 10 failures:
   - $n_1 \hat{p}_1 \geq 10$ and $n_1(1 - \hat{p}_1) \geq 10$
   - $n_2 \hat{p}_2 \geq 10$ and $n_2(1 - \hat{p}_2) \geq 10$

### Standard Error

$$SE(\hat{p}_1 - \hat{p}_2) = \sqrt{\frac{\hat{p}_1(1-\hat{p}_1)}{n_1} + \frac{\hat{p}_2(1-\hat{p}_2)}{n_2}}$$

### Confidence Interval

$$(\hat{p}_1 - \hat{p}_2) \pm z^* \times SE(\hat{p}_1 - \hat{p}_2)$$

**Example — Fish Oil Study:** A large randomized trial found a 95% CI of $(-0.0071,\ -0.0015)$, suggesting fish oil reduces heart attack rates by 0.15 to 0.71 percentage points.

### Hypothesis Testing with Pooled Proportion

When testing $H_0: p_1 - p_2 = 0$, the null hypothesis asserts the two groups share a common proportion. We estimate this with the **pooled proportion**:

$$\hat{p}_{pool} = \frac{\text{total successes in both groups}}{n_1 + n_2}$$

The standard error under $H_0$ uses $\hat{p}_{pool}$:

$$SE_0(\hat{p}_1 - \hat{p}_2) = \sqrt{\hat{p}_{pool}(1-\hat{p}_{pool})\left(\frac{1}{n_1} + \frac{1}{n_2}\right)}$$

**Test statistic:**

$$Z = \frac{(\hat{p}_1 - \hat{p}_2) - 0}{SE_0(\hat{p}_1 - \hat{p}_2)}$$

The p-value is computed from the standard normal distribution (one-sided or two-sided depending on $H_A$).

### Case Study: Mammogram Screening

A large randomized trial of ~90,000 participants compared regular mammograms versus non-mammogram breast exams for breast cancer mortality.

- $H_0$: Mammograms make no difference in breast cancer deaths. $p_{mammogram} - p_{control} = 0$
- $H_A$: Mammograms change breast cancer death rates. $p_{mammogram} - p_{control} \neq 0$

**Result:** p-value $= 0.865$

We do not reject $H_0$. The data do not provide evidence that mammograms reduce breast cancer deaths compared to non-mammogram exams. This case also illustrates that statistical analyses must be weighed against real-world factors such as costs, false positives, and over-diagnosis.

> **Reminder:** Use the pooled proportion $\hat{p}_{pool}$ for hypothesis tests (assuming $H_0: p_1 - p_2 = 0$) and the individual sample proportions for confidence intervals.

---

## 17.4 Chapter Review

### 17.4.1 Summary

This chapter presented three complementary approaches for drawing inference about the difference between two proportions, $p_1 - p_2$:

- **Randomization tests** simulate what differences would look like if $H_0$ were true by randomly shuffling group labels; the p-value is the fraction of simulated differences as extreme as the observed one.
- **Bootstrap confidence intervals** resample from each group independently to build a distribution of plausible values for $\hat{p}_1 - \hat{p}_2$, yielding either percentile or SE-based intervals.
- **Mathematical modeling** uses the normal approximation (when conditions are met) to compute exact SE formulas, z-scores, and p-values. Pooled proportions are used for hypothesis tests; unpooled proportions are used for confidence intervals.

All three methods ask the same underlying question but through different computational lenses, and they generally agree when conditions are met.

### 17.4.2 Key Terms

| Term | Brief Definition |
|------|-----------------|
| **Percentile interval** | A bootstrap CI formed using the lower and upper percentiles (e.g., 2.5th and 97.5th) of the bootstrap distribution |
| **Pooled proportion ($\hat{p}_{pool}$)** | Combined success proportion from both groups, used in hypothesis tests where $H_0: p_1 = p_2$ |
| **SE interval** | A bootstrap CI using the point estimate $\pm z^* \times SE_{boot}$ |
| **Point estimate** | $\hat{p}_1 - \hat{p}_2$, the observed difference in sample proportions |
| **SE for difference in proportions** | $\sqrt{\hat{p}_1(1-\hat{p}_1)/n_1 + \hat{p}_2(1-\hat{p}_2)/n_2}$ — used for CIs |
| **Z-score for two proportions** | Test statistic $Z = (\hat{p}_1 - \hat{p}_2) / SE_0$, where $SE_0$ uses $\hat{p}_{pool}$ |
| **Success-failure condition** | Requirement that each group has $\geq 10$ successes and $\geq 10$ failures for the normal approximation |
| **Bootstrap distribution** | Distribution of a statistic built by resampling with replacement from the observed data |
| **Null distribution** | Sampling distribution of a statistic under the assumption that $H_0$ is true |

---

## 17.5 Exercises Overview

The chapter includes **22 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1 | **Disaggregating Asian American tobacco use (HT)** | Randomization test for two proportions; defining parameter vs. statistic; estimating SE from histogram; p-value interpretation |
| 2 | **Malaria vaccine effectiveness (HT)** | One-sided randomization hypothesis test; identifying parameter; estimating p-value from simulation histogram |
| 3 | **Asian American tobacco use (CI)** | Bootstrap SE and percentile confidence intervals for Filipino vs. Chinese smoking rate differences |
| 4 | **Malaria vaccine effectiveness (CI)** | Bootstrap SE and percentile CIs for infection rate difference (vaccine vs. control) |
| 5 | **COVID-19 and degree completion** | Distinguishing randomization vs. bootstrap distributions; comparing centers and standard errors; formulating research questions |
| 6 | **Renewable energy** | Comparing randomization and bootstrap distributions; interpreting differences in center; formulating research questions |
| 7 | **HIV in Sub-Saharan Africa** | Mathematical model hypothesis test; constructing two-way table; checking conditions; Nevirapine vs. Lopinavir failure rates |
| 8 | **Supercommuters** | Computing mean and SD of individual sample proportions; deriving mean and SD of the difference; understanding how SDs combine |
| 9 | **National Health Plan** | 95% CI for Democrat vs. Independent support difference; interpreting CI; true/false about direction of difference |
| 10 | **Sleep deprivation (CA vs. OR) — CI** | Mathematical model CI for state-level proportion difference; interpreting in context |
| 11 | **Gender pay gap in medicine** | Hypothesis test for binary outcomes (men earned more in 19/21 positions); writing and completing test |
| 12 | **Sleep deprivation (CA vs. OR) — HT** | Mathematical model hypothesis test; checking conditions; identifying Type I or Type II error |
| 13 | **Is yawning contagious?** | Identifying success-failure condition violations; consequences of using inappropriate normal approximation |
| 14 | **Heart transplant success** | Checking conditions for normal approximation CI; risks of proceeding when conditions fail |
| 15 | **Government shutdown** | True/false interpretation of a 95% CI $(-0.16, 0.02)$; 90% vs. 95% CI width; reversing the difference |
| 16 | **Online harassment by age** | True/false questions on CI interpretation, random sampling behavior, statistical discernibility, and calculation details |
| 17 | **Decision errors I** | Identifying Type I and Type II errors in three studies (malaria vaccine, Asian American smoking, government shutdown) |
| 18 | **Decision errors II** | Identifying Type I and Type II errors in two studies (offshore drilling opinions, CA/OR sleep deprivation) |
| 19 | **Active learning** | Evaluating applicability of two-proportion methods when the same students are measured twice (paired data) |
| 20 | **An apple a day** | Determining whether two-proportion methods apply to paired survey data from the same students |
| 21 | **Malaria vaccine (effect size and power)** | Power calculations under different true differences $\delta$ and sample sizes; drawing conclusions about detectability |
| 22 | **Diabetes and unemployment** | Two-way table construction; hypotheses for proportion difference; distinguishing statistical discernibility from practical importance |

> Answers to odd-numbered exercises can be found in Appendix A.17 of the textbook.

---

## Key Takeaways: Inference for Comparing Two Proportions

### Three Methods at a Glance

| Method | Use For | Requires |
|--------|---------|---------|
| **Randomization test** | Hypothesis testing ($H_0: p_1 - p_2 = 0$) | Random assignment or sampling |
| **Bootstrap CI** | Confidence intervals | No distributional assumptions |
| **Mathematical model** | Both HT and CI | Success-failure condition ($\geq 10$ each cell) |

### Formulas

| Quantity | Formula | When to Use |
|----------|---------|-------------|
| **Point estimate** | $\hat{p}_1 - \hat{p}_2$ | Always |
| **SE (for CI)** | $\sqrt{\dfrac{\hat{p}_1(1-\hat{p}_1)}{n_1} + \dfrac{\hat{p}_2(1-\hat{p}_2)}{n_2}}$ | Confidence intervals |
| **Pooled proportion** | $\hat{p}_{pool} = \dfrac{\text{total successes}}{n_1 + n_2}$ | Hypothesis tests only |
| **SE under $H_0$** | $\sqrt{\hat{p}_{pool}(1-\hat{p}_{pool})\left(\dfrac{1}{n_1} + \dfrac{1}{n_2}\right)}$ | Hypothesis tests only |
| **Z-statistic** | $Z = \dfrac{(\hat{p}_1 - \hat{p}_2) - 0}{SE_0}$ | Hypothesis tests |
| **Confidence interval** | $(\hat{p}_1 - \hat{p}_2) \pm z^* \times SE$ | Point estimate $\pm$ margin of error |

### Conditions for Normal Approximation

| Condition | Requirement |
|-----------|-------------|
| **Independence** | Random sample or random assignment; observations do not influence each other |
| **Success-failure (group 1)** | $n_1 \hat{p}_1 \geq 10$ and $n_1(1-\hat{p}_1) \geq 10$ |
| **Success-failure (group 2)** | $n_2 \hat{p}_2 \geq 10$ and $n_2(1-\hat{p}_2) \geq 10$ |

> When the success-failure condition is violated, use simulation-based methods (randomization or bootstrap) instead.

### Randomization vs. Bootstrap Distributions

| Feature | Randomization Distribution | Bootstrap Distribution |
|---------|---------------------------|----------------------|
| **Center** | 0 (null hypothesis value) | $\hat{p}_1 - \hat{p}_2$ (observed value) |
| **Purpose** | Hypothesis testing | Confidence intervals |
| **How built** | Shuffle pooled data and re-assign labels | Resample each group independently with replacement |
| **SE estimate** | SE of the null distribution | SE of the bootstrap distribution |

### Critical Warnings

1. ⚠️ **Use pooled proportion for hypothesis tests, not for CIs** — the pooled estimate assumes $H_0: p_1 = p_2$; this assumption should not be baked into a confidence interval
2. ⚠️ **Check the success-failure condition in each group separately** — a large total sample does not guarantee the condition is met in both groups
3. ⚠️ **Do not apply two-proportion methods to paired data** — if the same individuals are measured twice (e.g., before/after), the independence condition is violated
4. ⚠️ **Statistical discernibility ≠ practical importance** — a very large sample can yield a statistically discernible but practically negligible difference
5. ⚠️ **A non-significant result does not prove $H_0$** — failing to reject $H_0: p_1 - p_2 = 0$ only means the data are consistent with no effect, not that no effect exists
6. ⚠️ **Confidence level describes repeated sampling, not a single interval** — the true parameter is fixed; "95% confidence" refers to the procedure, not the specific interval computed
7. ⚠️ **Randomization and bootstrap distributions differ in center** — the randomization null distribution is centered at 0; the bootstrap distribution is centered at the observed difference
8. ⚠️ **One-sided tests require pre-specified direction** — choosing the direction after seeing the data inflates the Type I error rate (see Chapter 14)
9. ⚠️ **Consider real-world context alongside statistical results** — as in the mammogram study, p-values must be interpreted alongside costs, over-diagnosis risk, and practical tradeoffs
10. ⚠️ **Power depends on true effect size and sample size** — small differences require large samples to detect reliably (see Exercise 21)
