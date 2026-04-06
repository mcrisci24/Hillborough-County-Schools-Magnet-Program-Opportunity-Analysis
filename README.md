# Magnet vs Traditional Schools Analysis  
**Author:** Mark Crisci  

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

