# Magnet vs Traditional Schools Analysis  
**Data Scientist:** Mark Crisci 
-- **Analysis conducted on behalf of thesising NCF student:** Beverly Jacobs     

## Overview

This project analyzes disparities in magnet school enrollment across Hillsborough County Public Schools. The goal is to determine whether differences in enrollment are driven by:

- Race  
- Socioeconomic status (SES)  
- School-level structural factors (program availability, diversity)  

The analysis uses aggregated enrollment data and logistic regression models to estimate the probability of magnet enrollment.

---

## Data Description

The dataset contains school-level counts of students by:

- School name and year  
- Race/ethnicity (`race_df`)  
- Meal status (proxy for SES)  
- Magnet vs traditional enrollment  

From these, the following variables are constructed:

- `pct_magnet` — proportion of students in magnet programs  
- `pct_race` — racial composition within each school  
- `pct_free` — proportion of students receiving free/reduced lunch  
- `num_programs` — number of magnet programs offered by a school  
- `diversity` — measure of racial diversity within each school  

---

## Data Processing Pipeline

1. Clean demographic labels and standardize race categories  
2. Aggregate enrollment counts by school, year, and race  
3. Compute proportions (magnet, race composition, SES)  
4. Construct school-level dataset  
5. Merge magnet program information manually  
6. Remove invalid or unstable groups (e.g., Indigenous American perfect separation)  

---

## Modeling Approach

All models are estimated using binomial logistic regression:

\[
\text{logit}(P(\text{magnet})) = X\beta
\]

### Models included:

#### 1. Race-only model
Evaluates baseline differences in enrollment by race.

#### 2. Structural model (`log.obj.full`)
Includes:
- Race  
- SES (`pct_free`)  
- Number of magnet programs  
- School diversity  

#### 3. Interaction model (`log.obj.int`)
Adds interaction terms:
- Race × SES  

This tests whether SES effects differ across racial groups.

---

## Model Validation

A 70/30 train-test split is used.

Performance metrics:

- Mean Absolute Error (MAE)  
- Root Mean Squared Error (RMSE)  
- Weighted MAE (accounts for school size)  

Visualization:
- Predicted vs Actual probability plot  
- Residual analysis  

---

## Key Findings

- **Program availability is the strongest predictor** of magnet enrollment  
- **Race remains significant** even after controlling for SES and structure  
- **SES effects vary by race**, indicating interaction effects  
- The model **underpredicts high-performing schools and overpredicts low-performing schools**, suggesting missing structural variables  

---

## Important Limitations

The dataset does not include:

- Program capacity limits  
- Admission criteria (GPA, test scores)  
- Application behavior (who applies vs who does not)  
- Transportation accessibility  

Because of this, the model captures **access patterns**, not the full selection process.

---

## Special Case: Indigenous American Category

The Indigenous American group shows 100% magnet enrollment due to a sample size of 11 students.

This results in **perfect separation**, which is not statistically stable and is excluded from modeling.

---

## Repository Structure
├── FINAL_ALL_INCLUDED (2).Rmd # Main analysis rmarkdown file
├── MagTrad.csv # Original Raw Data File
├── MagnetTraditionalCleaned.csv # Cleaned dataset used for modeling
├── FINAL_ALL_INCLUDED--2-.pdf # Generated analysis and plots
└── README.md # Project documentation

---

## How to Run

1. Open `analysis.Rmd` in RStudio  
2. Install required packages:
   ```r
   install.packages(c("dplyr", "ggplot2", "readr", "scales"))
---



## How to Run

1. Open `analysis.Rmd` in RStudio  
2. Install required packages:
   ```r
   install.packages(c("dplyr", "ggplot2", "readr", "scales"))
---



# Further Machine Learning Analysis Conducted Using SAS Software

## What “Access” Means in This Study

Throughout this analysis, the phrase “access drives outcomes” refers to the set of structural conditions that determine whether a student realistically has the opportunity to enroll in a magnet program.

Access is not a single variable. It is a combination of measurable school-level factors that either enable or constrain participation. In this dataset, access is captured by:

- The number of magnet programs available at a school (`num_programs`)
- The socioeconomic composition of the school (`pct_free`)
- The racial composition of the school (`race_df`, `pct_race`)
- The diversity of the school environment (`diversity`)

These variables collectively define the **opportunity structure** within which students make enrollment decisions.

---
# Neural Network Model Interpretation and Model Comparison

To further evaluate whether more flexible, non-linear models could improve predictive performance, a neural network model was trained using the same feature set as the previous models.

The results indicate that the neural network performed substantially worse than the tree-based and regression-based models.

---

## Model Performance Comparison

From the model comparison table:

- Gradient Boosting (Best Model)
  - RMSE ≈ 0.0094
- Random Forest
  - RMSE ≈ 0.0109
- Decision Tree
  - RMSE ≈ 0.0231
- Neural Network
  - RMSE ≈ 0.0637

The neural network exhibits an error rate approximately **6–7 times larger** than the best-performing models.

---

## Interpretation of Neural Network Results

The neural network achieves:

- Train R² ≈ 0.957  
- Validation R² ≈ 0.957  
- Test R² ≈ 0.957  

At first glance, this appears strong. However, this is misleading when considered alongside the error metrics.

The model shows:

- High bias (poor fit relative to other models)
- Smooth, nearly linear prediction curves
- Inability to capture sharp structural effects in the data

The predicted vs. actual plots confirm that:

- The neural network systematically underestimates high-probability observations
- It fails to capture extreme values (near 0 or 1)
- Predictions are overly smoothed toward the mean

---

## Why the Neural Network Performs Worse

This result is not surprising given the structure of the data.

### 1. The Data is Structured, Not Continuous

The key predictors in this dataset are:

- Categorical (race_df)
- Aggregated proportions (pct_free, pct_race)
- Discrete institutional variables (num_programs)

These variables produce **step-like, segmented relationships**, not smooth nonlinear functions.

Neural networks are designed to approximate smooth nonlinear surfaces. In contrast, this dataset contains:

- Threshold effects
- Structural breaks
- Institutional constraints

Tree-based models capture these patterns directly, while neural networks smooth over them.

---

### 2. The True Relationship is Piecewise, Not Highly Nonlinear

The underlying process generating magnet enrollment is driven by:

- Access constraints
- Program availability
- School-level composition

These relationships behave more like:

- Conditional rules
- Partitioned decision spaces

rather than continuous nonlinear transformations.

For example:

- Going from 0 to 1 program has a large effect  
- Going from 1 to 2 programs may have a smaller effect  

This type of structure is naturally captured by trees, not neural networks.

---

### 3. Sample Size is Relatively Small

The dataset contains:

- 281 training observations  
- 144 validation observations  
- 46 test observations  

Neural networks typically require large datasets to outperform simpler models. In small to moderate samples:

- They are unstable  
- They underfit or overfit easily  
- They fail to learn meaningful structure  

---

### 4. Signal is Already Strong and Simple

Earlier models demonstrated that:

- Race
- Socioeconomic composition
- Program availability
- Diversity

already explain most of the variation in magnet enrollment.

This means:

There is little hidden nonlinear structure left for the neural network to learn.

---

## Key Insight from Model Comparison

The fact that gradient boosting and random forest models outperform the neural network suggests that:

> Magnet enrollment is governed by structured, rule-based relationships rather than complex nonlinear interactions.

This reinforces the central conclusion of the analysis:

- Outcomes are driven by institutional structure  
- Not by subtle nonlinear individual-level effects  

---

## Substantive Interpretation

The machine learning results provide strong supporting evidence for the broader finding:

Magnet enrollment patterns are highly predictable once school-level structural variables are included.

This indicates that:

- Access to magnet programs is largely determined by the institutional environment  
- The distribution of opportunity is embedded in school structure  
- Individual-level variation plays a secondary role relative to structural constraints  

---

## Final Conclusion from Predictive Modeling

Across all models, the consistent result is that:

- Tree-based models perform best  
- Linear models perform well  
- Neural networks perform worst  

This hierarchy reflects the nature of the data-generating process.

The results suggest that magnet enrollment is best understood as a function of:

- Discrete institutional features  
- Segmented opportunity structures  
- Uneven distribution of resources across schools  

rather than continuous, highly nonlinear individual-level relationships.

---

## Takeaway

The predictive modeling results reinforce the core argument of this study:

Educational opportunity is structured, not random, and not purely individual.

It is shaped by the composition and organization of schools themselves.
## Concrete Example 1: Program Availability

Consider two schools:

- School A: 0 magnet programs
- School B: 3 magnet programs

Even if two students are identical in every individual characteristic (race, SES, etc.), their probability of enrolling in a magnet program differs dramatically because:

- At School A, enrollment is structurally impossible or extremely limited
- At School B, multiple entry points exist

This is reflected in the model:

- The coefficient on `num_programs` is large and highly significant
- Tree-based models rank `num_programs` as the most important predictor

Interpretation:

An increase in magnet programs does not just increase probability slightly — it fundamentally changes whether participation is feasible at all.

---

## Concrete Example 2: Socioeconomic Composition

Now consider two schools with identical program offerings:

- School C: 30% free/reduced lunch (lower poverty)
- School D: 85% free/reduced lunch (higher poverty)

Even with the same number of programs, access differs because:

- Schools with higher poverty often face barriers such as:
  - Lower application rates
  - Limited transportation access
  - Information gaps about programs

This appears in your models:

- `pct_free` is highly significant
- In the interaction model, its effect changes depending on race
- Tree models show threshold behavior (not linear)

Interpretation:

Socioeconomic context affects not just eligibility, but the ability to convert opportunity into actual enrollment.

---

## Concrete Example 3: Racial Composition and School Structure

Consider two schools:

- School E: 60% White, multiple magnet programs, lower poverty
- School F: 60% Black, fewer programs, higher poverty

At the individual level, a Black student in School F may appear to have lower magnet enrollment probability than a White student in School E.

However, this difference is not purely attributable to individual race.

Instead:

- School E provides more institutional access (more programs, lower barriers)
- School F provides fewer structural opportunities

This explains why:

- Race appears significant in the GLM
- But tree-based models show that structural variables dominate prediction

Interpretation:

Race is partially acting as a proxy for differences in school-level opportunity structures.

---

## Why This Matters for Model Interpretation

The key modeling insight is:

- The original GLM assumes that predictors act independently and smoothly
- Tree-based models reveal that outcomes depend on **combinations of conditions**

For example:

- The effect of `pct_free` is not constant
- The effect of `num_programs` is not linear
- The impact of race changes depending on school context

This means:

Magnet enrollment is not determined by a single variable, but by whether a student is located within a favorable structural configuration.

---

## Final Interpretation

When we say:

> “Access drives outcomes, not just individual traits”

we mean:

A student’s probability of enrolling in a magnet program is largely determined by:

- Whether their school offers programs
- Whether their school environment supports participation
- Whether structural barriers are present or absent

Two students with identical individual characteristics can have very different outcomes if they are placed in different school contexts.

---

## Summary

Access, in this study, is operationalized as the combination of:

- Program availability (num_programs)
- Socioeconomic environment (pct_free)
- School composition (race and diversity)

The results consistently show that these structural factors dominate prediction across all models.

This indicates that educational opportunity is not evenly distributed, but is instead shaped by the organization and composition of schools themselves.
