# Chapter 2: Study Design

> **Source:** *Introduction to Modern Statistics (2e)* — [openintro-ims.netlify.app/data-design](https://openintro-ims.netlify.app/data-design)

---

## Table of Contents

- [Overview](#overview)
- [2.1 Sampling Principles and Strategies](#21-sampling-principles-and-strategies)
  - [2.1.1 Populations and Samples](#211-populations-and-samples)
  - [2.1.2 Parameters and Statistics](#212-parameters-and-statistics)
  - [2.1.3 Anecdotal Evidence](#213-anecdotal-evidence)
  - [2.1.4 Sampling from a Population](#214-sampling-from-a-population)
  - [2.1.5 Four Sampling Methods](#215-four-sampling-methods)
- [2.2 Experiments](#22-experiments)
  - [2.2.1 Principles of Experimental Design](#221-principles-of-experimental-design)
  - [2.2.2 Reducing Bias in Human Experiments](#222-reducing-bias-in-human-experiments)
- [2.3 Observational Studies](#23-observational-studies)
- [2.4 Chapter Review](#24-chapter-review)
  - [2.4.1 Scope of Inference](#241-scope-of-inference)
  - [2.4.2 Key Terms](#242-key-terms)
- [2.5 Exercises Overview](#25-exercises-overview)
- [Key Takeaways](#key-takeaways)

---

## Overview

This chapter examines how data are collected and how study design affects the validity of conclusions. Understanding data provenance — who or what the data represent and how observations were collected — is essential for determining whether results can be generalized to a population and whether causal relationships can be established. A good question to ask before working with data at all is: *"How were these observations collected?"*

---

## 2.1 Sampling Principles and Strategies

The first step in conducting research is to identify topics or questions to be investigated. A clearly defined research question helps identify what subjects should be studied and what variables are important. It is equally important to consider *how* data are collected so that the data are reliable and help achieve the research goals.

### 2.1.1 Populations and Samples

| Term | Definition | Example |
|------|-----------|---------|
| **Population** | The entire group of interest | All swordfish in the Atlantic Ocean |
| **Sample** | A subset of the population that is actually observed | 60 swordfish selected from the population |
| **Census** | Collecting data for every unit in a population | Surveying every Duke undergrad from the last five years |

A census is rarely feasible because it is too expensive, or because it is difficult or impossible to identify the entire population of interest. Instead, a sample is taken and used to estimate population characteristics and answer research questions.

### 2.1.2 Parameters and Statistics

| Term | Definition |
|------|-----------|
| **Population parameter** | A numerical summary calculated from the entire population |
| **Sample statistic** | A numerical summary calculated from a sample |

These terms are critical for communicating claims and building statistical models, especially in later chapters on inference. Since measuring every unit in the population is usually prohibitive, most analyses work with sample statistics to estimate population parameters.

### 2.1.3 Anecdotal Evidence

**Anecdotal evidence** is data collected in a haphazard fashion, often based on unusual or memorable cases.

**Characteristics:**
- May be true and verifiable
- May only represent extraordinary cases
- Not a good representation of the population
- People are more likely to remember unusual cases (e.g., two students who took 7 years to graduate) than typical ones

> ⚠️ **Warning:** Be careful of data collected in a haphazard fashion. Instead of looking at the most unusual cases, examine a sample of many cases that better represent the population.

### 2.1.4 Sampling from a Population

The most basic form of random selection is equivalent to how raffles are conducted — writing each case's name on a ticket and drawing from a hat.

| Term | Definition |
|------|-----------|
| **Simple random sample** | Each case in the population has an equal chance of being included, and cases in the sample are independent of each other |
| **Bias** | Systematic error introduced when samples over- or under-represent certain groups |
| **Non-response rate** | The percentage of sampled individuals who do not respond to a survey |
| **Non-response bias** | When only a fraction of randomly sampled individuals respond, skewing results |
| **Representative** | A sample that accurately reflects the characteristics of the population |
| **Convenience sample** | Individuals who are easily accessible are more likely to be included; difficult to discern what sub-population it represents |

Sampling randomly helps minimize bias, but bias can still arise through non-response, convenience sampling, or unintentional selection preferences.

### 2.1.5 Four Sampling Methods

Almost all statistical methods are based on the notion of implied randomness. If data are not collected in a random framework from a population, the estimates and errors associated with those estimates are not reliable.

#### Simple Random Sampling

- Each case has an equal chance of selection
- Knowing one case is included provides no information about which other cases are included
- Most intuitive form of random sampling

#### Stratified Sampling

- Population is divided into **strata** (groups of similar cases)
- Random sampling (usually simple random) is applied within each stratum
- **Best when:** cases within each stratum are very similar with respect to the outcome of interest
- **Advantage:** yields more stable and precise estimates within each group, leading to a more precise overall population estimate
- **Downside:** requires more complex analysis methods than simple random sampling

#### Cluster Sampling

- Population is divided into **clusters**
- A fixed number of clusters are sampled
- **All** observations within each selected cluster are included
- **Best when:** there is a lot of case-to-case variability within a cluster but clusters themselves do not look very different from one another

#### Multistage Sampling

- Like cluster sampling, but a random sample is taken **within** each selected cluster rather than including all observations
- Reduces data collection costs substantially
- Still yields reliable information, though analysis requires more advanced methods

| Sampling Method | How It Works | Best When | Trade-off |
|----------------|-------------|-----------|-----------|
| **Simple random** | Every case has equal chance of selection | Population is relatively homogeneous | May be expensive if cases are spread out |
| **Stratified** | Divide into strata, then sample within each | Cases within strata are similar | More complex analysis required |
| **Cluster** | Sample entire clusters | Clusters are similar to each other; high within-cluster variability | More complex analysis required |
| **Multistage** | Sample clusters, then sample within clusters | Clusters are similar; want to reduce cost | More complex analysis required |

---

## 2.2 Experiments

| Term | Definition |
|------|-----------|
| **Experiment** | A study where researchers assign treatments to cases |
| **Randomized experiment** | Treatment assignment includes randomization (e.g., coin flip) |

Randomized experiments are fundamentally important when trying to show a **causal connection** between two variables.

### 2.2.1 Principles of Experimental Design

#### 1. Controlling

Researchers assign treatments and control other differences between groups. For example, instructing every patient to drink a 12-ounce glass of water with a pill to control for the effect of water consumption.

#### 2. Randomization

Researchers randomize patients into treatment groups to account for variables that cannot be controlled. This helps even out **confounding variables**.

> **Confounding variable:** A variable that is associated with both the explanatory and response variables. Because it is associated with both, it prevents the study from concluding that the explanatory variable caused the response variable.
>
> **Example:** Ice-cream sales and boating accidents appear highly correlated, but outside temperature is associated with both — we cannot conclude that high ice-cream sales cause more boating accidents.

Confounding variables may or may not be measured as part of the study. Regardless, drawing cause-and-effect conclusions is difficult in an observational study because of the ever-present possibility of confounding.

#### 3. Replication

- Collecting a sufficiently large sample to accurately estimate treatment effects
- Also refers to replicating an entire study to verify findings
- **Pseudoreplication:** When individual observations under different treatments are heavily dependent on each other, exaggerating reported sample sizes (e.g., counting 500 blood pressure measurements from 50 subjects as 500 independent observations)
- **Replication crisis:** Ongoing methodological crisis where past scientific findings in several disciplines have failed to be replicated

#### 4. Blocking

- Researchers group individuals based on a known influential variable into **blocks**
- Then randomize cases within each block to treatment groups
- Ensures equal representation across treatment groups

**Example:** Splitting patients into low-risk and high-risk blocks before randomizing half of each block to treatment and half to control.

### 2.2.2 Reducing Bias in Human Experiments

| Term | Definition |
|------|-----------|
| **Treatment group** | Receives the intervention or drug |
| **Control group** | Does not receive the treatment (or receives standard of care) |
| **Blind study** | Patients do not know which group they are in |
| **Placebo** | A fake treatment given to the control group to keep patients uninformed |
| **Placebo effect** | A slight but real improvement resulting from receiving a placebo |
| **Double-blind study** | Both patients and researchers/interacting doctors are unaware of who receives the treatment |

The emotional effect of (not) taking a drug can bias a study. A placebo (e.g., a sugar pill made to look like the real treatment) keeps patients uninformed. Double-blinding further guards against researcher bias — doctors who know a patient received the real treatment might inadvertently give that patient more attention or care.

> **Ethical note:** There are always multiple viewpoints on experiments and placebos. For instance, is it ethical to use a sham surgery when it creates risk? But if we do not use sham surgeries, we may promote a costly treatment with no real effect, diverting resources from known helpful treatments.

---

## 2.3 Observational Studies

| Term | Definition |
|------|-----------|
| **Observational study** | No treatment is explicitly applied or withheld; relies on observational data |
| **Prospective study** | Identifies individuals and collects information as events unfold |
| **Retrospective study** | Collects data after events have taken place (e.g., reviewing past medical records) |

**Key limitation:** Observational studies cannot establish **causal** relationships due to the ever-present possibility of confounding variables. They are generally only sufficient to show **associations** or form hypotheses for later experimental testing.

**Example — Sunscreen and skin cancer:** An observational study might find that more sunscreen use is associated with more skin cancer. This does *not* mean sunscreen causes skin cancer — sun exposure is a confounding variable associated with both sunscreen use and skin cancer risk.

| Study Type | Researchers Assign Treatment? | Can Establish Causation? |
|-----------|:---:|:---:|
| **Randomized experiment** | ✅ Yes | ✅ Yes |
| **Observational study** | ❌ No | ❌ No — only associations |

---

## 2.4 Chapter Review

### 2.4.1 Scope of Inference

The conclusions that can be drawn depend on how data were collected:

| | Random Assignment (Experiment) | No Random Assignment (Observational) |
|---|---|---|
| **Random Sample** | Generalizable to population + Causal conclusions | Generalizable to population only |
| **No Random Sample** | Causal conclusions only (for the sample) | Neither generalizable nor causal |

> Very few datasets come from the top-left box because ethics usually require that random assignment of treatments can only be given to volunteers. Both representative (ideally random) sampling and experiments (random assignment of treatments) are important for how statistical conclusions can be made.

### 2.4.2 Key Terms

| Term | Term | Term |
|------|------|------|
| anecdotal evidence | experiment | replication |
| bias | multistage sample | replication crisis |
| blind | non-response bias | representative |
| blocking | non-response rate | retrospective study |
| census | observational study | sample |
| cluster | placebo | sample bias |
| cluster sampling | placebo effect | sample statistic |
| confounding variable | population | simple random sample |
| control | population parameter | simple random sampling |
| control group | prospective study | strata |
| convenience sample | pseudoreplication | stratified sampling |
| double-blind | randomized experiment | treatment group |

---

## 2.5 Exercises Overview

The chapter includes **18 practice exercises** covering the following topics:

| # | Topic | Key Concepts Tested |
|---|-------|-------------------|
| 1–2 | **Parameters vs. statistics** | Identifying sample mean vs. claimed population mean |
| 3–6 | **Scope of inference** | Population, sample, generalizability, causation across diverse study scenarios |
| 7–8 | **Observations, variables, statistics, parameters** | Classifying elements from study descriptions |
| 9–10 | **Sampling strategies** | Suggesting appropriate sampling methods for course satisfaction and housing surveys |
| 11–12 | **Observational studies & confounding** | Describing relationships, identifying study types, naming confounding variables |
| 13 | **Evaluating sampling methods** | Assessing whether proposed sampling strategies are reasonable |
| 14 | **Random digit dialing** | Understanding survey methodology choices |
| 15 | **Attitudes study analysis** | Identifying cases, response/explanatory variables, study type, generalizability |
| 16 | **Critical analysis of news articles** | Evaluating causal claims from observational studies (smoking & dementia; sleep disorders & bullying) |
| 17 | **Sampling method identification** | Naming sampling methods and expected biases from described strategies |
| 18 | **Family size estimation bias** | Identifying whether a sampling approach over- or underestimates a population value |

> Answers to odd-numbered exercises can be found in Appendix A.2 of the textbook.

---

## Key Takeaways

1. **Sampling method determines generalizability** — Only a representative (ideally random) sample allows conclusions to be generalized back to the population.

2. **Random assignment determines causation** — Only randomized experiments can establish causal relationships. Observational studies can only show associations.

3. **Avoid anecdotal evidence** — Data collected haphazardly may be true but often represent extraordinary cases, not the population.

4. **Bias lurks everywhere** — Non-response bias, convenience sampling, and unintentional selection preferences can all skew results. Random sampling is the best defense.

5. **Confounding variables are the enemy of causation** — A variable associated with both the explanatory and response variables prevents causal conclusions. They may or may not be measured.

6. **Choose the right sampling method for the context** — Simple random sampling is most intuitive, stratified sampling improves precision when strata are homogeneous, and cluster/multistage sampling reduce costs when cases are geographically dispersed.

7. **Experimental design principles are non-negotiable** — Controlling, randomization, replication, and (when applicable) blocking should be incorporated into any study.

8. **Blinding reduces bias** — Single-blind keeps patients uninformed; double-blind keeps both patients and researchers uninformed, guarding against both placebo effects and researcher bias.

9. **Pseudoreplication inflates sample sizes** — Dependent observations should not be counted as independent. The entity being replicated matters.

10. **Always ask: "How were these observations collected?"** — The answer tells you a lot about what conclusions are justified before you even begin analyzing the data.
