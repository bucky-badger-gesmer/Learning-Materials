# Chapter 1: Hello Data

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/data-hello](https://openintro-ims.netlify.app/data-hello)

---

## Table of Contents

- [Overview](#overview)
- [1.1 Case Study: Using Stents to Prevent Strokes](#11-case-study-using-stents-to-prevent-strokes)
- [1.2 Data Basics](#12-data-basics)
  - [1.2.1 Observations, Variables, and Data Matrices](#121-observations-variables-and-data-matrices)
  - [1.2.2 Types of Variables](#122-types-of-variables)
  - [1.2.3 Relationships Between Variables](#123-relationships-between-variables)
  - [1.2.4 Explanatory and Response Variables](#124-explanatory-and-response-variables)
  - [1.2.5 Observational Studies and Experiments](#125-observational-studies-and-experiments)
- [1.3 Chapter Review](#13-chapter-review)
  - [Key Terms](#key-terms)
- [1.4 Exercises Overview](#14-exercises-overview)
- [Key Principles & Takeaways](#key-principles--takeaways)

---

## Overview

**Statistics** is the study of how best to collect, analyze, and draw conclusions from data. Scientists answer questions using rigorous methods and careful observations — collected from field notes, surveys, and experiments — which form the backbone of statistical investigations and are called **data**.

This chapter introduces the foundational concepts of data: its properties, how it is organized, and how it is collected.

---

## 1.1 Case Study: Using Stents to Prevent Strokes

### Research Question

> *"Does the use of stents reduce the risk of stroke?"*

### Study Design (Chimowitz et al. 2011)

- **451 at-risk patients** randomly assigned to two groups
- **Stents** are small mesh tubes placed inside narrow or weak arteries to assist recovery after cardiac events

| Group | Description |
|-------|-------------|
| **Treatment** (224 patients) | Received a stent + medical management (medications, risk factor management, lifestyle modification help) |
| **Control** (227 patients) | Received the same medical management but **no stents** |

### Results

| Group | 30 days: Stroke | 30 days: No event | 365 days: Stroke | 365 days: No event |
|-------|----------------|-------------------|------------------|--------------------|
| Control | 13 | 214 | 28 | 199 |
| Treatment | 33 | 191 | 45 | 179 |
| **Total** | **46** | **405** | **73** | **378** |

### Summary Statistics (365 days)

- **Treatment group stroke rate:** 45/224 = **0.20 (20%)**
- **Control group stroke rate:** 28/227 = **0.12 (12%)**

### Key Finding

An additional **8% of patients in the treatment group had a stroke** — contrary to what doctors expected. The published analysis found compelling evidence of **harm** caused by stents in this study.

### Important Caveat

> Do not generalize results to all patients and all stents. This study used the self-expanding **Wingspan stent** (Boston Scientific) with very specific patient characteristics.

---

## 1.2 Data Basics

### 1.2.1 Observations, Variables, and Data Matrices

| Term | Definition | Example |
|------|-----------|---------|
| **Case** / **Observation** / **Unit of observation** | A single row in a dataset | A single loan |
| **Variable** | A column representing a characteristic of each case | `loan_amount`, `interest_rate` |
| **Data frame** | A common way to organize data in spreadsheet format | Rows = cases, columns = variables |

#### Tidy Data

A **tidy data** structure satisfies three conditions:

1. Each **row** is a unique case
2. Each **column** is a variable
3. Each **cell** is a single value

#### Example Datasets

**`loan50`** — 50 randomly sampled loans from Lending Club

Variables: `loan_amount`, `interest_rate`, `term`, `grade`, `state`, `total_income`, `homeownership`

**`county`** — 3,142 US counties

Variables: `name`, `state`, `pop2000`, `pop2010`, `pop2017`, `pop_change`, `poverty`, `homeownership`, `multi_unit`, `unemployment_rate`, `metro`, `median_edu`, `per_capita_income`, `median_hh_income`, `smoking_ban`

---

### 1.2.2 Types of Variables

All variables fall into one of two main categories:

```
Variables
├── Numerical
│   ├── Discrete    → specific values with jumps (e.g., population count, number of siblings)
│   └── Continuous  → any value within a range (e.g., unemployment rate, height)
│
└── Categorical
    ├── Nominal     → categories without natural ordering (e.g., state, homeownership status)
    └── Ordinal     → categories with natural ordering (e.g., median_edu: below_hs < hs_diploma < some_college < bachelors)
```

#### Numerical Variables

Can take a wide range of numerical values; it is sensible to add, subtract, or average them.

- **Discrete:** Only specific values with jumps between them
- **Continuous:** Any value within a range

#### Categorical Variables

Values are categories rather than numbers.

- **Nominal:** No natural ordering among categories
- **Ordinal:** Natural ordering exists among categories

---

### 1.2.3 Relationships Between Variables

| Term | Definition |
|------|-----------|
| **Associated variables** | Two variables show some connection with one another |
| **Independent variables** | No evident relationship between the two variables |
| **Positive association** | As one variable increases, the other tends to increase |
| **Negative association** | As one variable increases, the other tends to decrease |

- **Scatterplots** are used to visualize relationships between two numerical variables.

> **Key Principle:** Variables are either *associated* or *independent* — **not both**.

---

### 1.2.4 Explanatory and Response Variables

```
Explanatory variable  →  might affect  →  Response variable
     (predictor)                           (outcome)
```

| Term | Definition |
|------|-----------|
| **Explanatory variable** | The variable that might causally affect another (the *predictor*) |
| **Response variable** | The variable that might be affected (the *outcome*) |

> **Note:** Labeling variables as explanatory and response does **not** guarantee a causal relationship exists.

---

### 1.2.5 Observational Studies and Experiments

#### Two Primary Types of Data Collection

| Type | Description | Causal Inference |
|------|------------|-----------------|
| **Experiment** | Researchers assign treatments to evaluate causal effects | ✅ Can establish causation |
| **Observational Study** | Researchers collect data without interfering with how data arise | ❌ Cannot establish causation |

#### Experiments

- **Randomized experiment:** Participants are randomly assigned to groups
- Uses a **placebo** (fake treatment) to control for psychological effects
- Controls for **confounding variables**

#### Observational Studies

- Includes surveys, medical records, and cohort studies
- Can show **association** but **cannot establish causation**
- May require advanced statistical methods to control for confounding variables

> **Key Principle: Association ≠ Causation**
>
> Randomized experiments make it easier to establish causal relationships. Observational studies can only demonstrate associations.

---

## 1.3 Chapter Review

### 1.3.1 Summary

- Data can be organized in many ways, but **tidy data** (each row = observation, each column = variable) is best for statistical analysis
- The fundamental concepts introduced here will be revisited in end-to-end data analyses throughout the book

### Key Terms

| Term | Term | Term |
|------|------|------|
| associated | case | categorical |
| cohort | continuous | data |
| data frame | dependent | discrete |
| experiment | explanatory variable | independent |
| level | nominal | negative association |
| numerical | observation | observational study |
| ordinal | placebo | positive association |
| randomized experiment | response variable | summary statistic |
| tidy data | unit of observation | variable |

---

## 1.4 Exercises Overview

The chapter includes **16 exercises** covering the following topics:

| Topic | Example Datasets / Scenarios |
|-------|------------------------------|
| **Identifying observations and variables** | Marvel movies, Cherry Blossom Run |
| **Study component identification** | Air pollution & birth outcomes, cheaters study, gamification, stealers study |
| **Analyzing experimental results** | Migraine & acupuncture, sinusitis & antibiotics |
| **Observational studies vs. experiments** | Daycare fines, COVID-19 vaccine trial |
| **Classifying variables** | Palmer penguins, UK smoking habits, US airports, UN votes, UK baby names, Netflix shows |

---

## Key Principles & Takeaways

1. **Tidy data structure** — Each row is an observation, each column is a variable, each cell is a single value. This is the gold standard for statistical analysis.

2. **Variable classification** — Every variable is either *numerical* (discrete or continuous) or *categorical* (nominal or ordinal). Knowing the type determines how you analyze and visualize it.

3. **Association ≠ Causation** — Observational studies can reveal relationships but cannot prove cause and effect. Only well-designed randomized experiments can establish causation.

4. **Variables are either associated or independent** — There is no middle ground. Two variables either show a connection or they do not.

5. **Explanatory vs. response labeling** — Identifying which variable might affect the other is a useful analytical framework, but it does not by itself prove causation.

6. **Generalizability matters** — Results from a study with specific patients, treatments, or conditions should not be blindly generalized to all populations or all variants of a treatment.

7. **Randomization is powerful** — Random assignment to treatment and control groups helps control for confounding variables, making causal inference more reliable.
