---
title: "EDS 222 Final Project: Grape Quality and Environmental Predictors"
format: html
editor: visual
author: Joshua Ferrer-Lozano
date: 12/1/2025
---

## Overview
This repository contains the code and documentation for my EDS 222 final project.  
The project explores how environmental and physiological factors influence sugar content (°Brix) in grapes, using a **Gamma regression model**.  
The work is presented as a technical blog post built with Quarto.


**Main Research Question:** *How does sun exposure, soil moisture, and other environmental and physiological factors influence Brix, and does soil moisture moderate the effect of sun exposure?*

Other Possible Questions to Investigate:

- Confounding: Does irrigation or canopy management vary by region/variety enough to require additional adjustment?  
- Interaction interpretability: Is the sun × soil moisture interaction stable across varieties, or should I allow it to vary (three-way interaction or stratification)?  
- Nonlinearity: Do sun exposure or soil moisture show nonlinear relationships with °Brix?  
- Collinearity: How correlated are rainfall and soil moisture, and does including both degrade estimates?  
- Temporal pathway: Should harvest date be treated as a mediator (ripening progression) or a direct predictor?  


## Data

The data I will utilize will be [Grape Quality](https://www.kaggle.com/datasets/mrmars1010/grape-quality) data sourced from Kaggle with a usability score of 10/10.

This dataset records various temporal, categorical, and numerical variables related to grape development, including environmental conditions and physiological grape traits.

## Key Variables:

### Predictors
- `variety`: categorical predictor (grape type)  
- `region`: categorical predictor/stratifier  
- `acidity_ph`: numeric predictor (acidity level)  
- `sun_exposure_hours`: numeric predictor (environmental factor)  
- `soil_moisture_percent`: numeric predictor (environmental factor)  
- `rainfall_mm`: numeric predictor (environmental factor)  
- `cluster_weight_g`: numeric predictor (physiological trait)  
- `berry_size_mm`: numeric predictor (physiological trait)  
- `harvest_date`: temporal predictor (seasonal effect)  

### Response
- `sugar_content_brix`: numeric outcome (positive continuous, sugar concentration)  

### Potential Confounders
- Region  
- Variety  



## Hypotheses

- **Scientific Hypothesis:** After adjusting for variety and region, greater sun exposure and lower soil moisture are associated with higher sugar content (Brix).  
- **Null Hypothesis:** There is no association between sun exposure or soil moisture and Brix after adjustment.  
- **Alternative Hypothesis:** More sun exposure increases Brix and higher soil moisture decreases Brix, controlling for variety and region.  
- **Additional Hypothesis:** Later harvest dates will be associated with higher Brix.  




## DAG

region → sun_exposure_hours → sugar_content_brix → quality_score
region → rainfall_mm → soil_moisture_percent → sugar_content_brix
variety → sugar_content_brix
variety → acidity_ph → quality_score → sugar_content_brix
acidity_ph → quality_score
cluster_weight_g → sugar_content_brix
berry_size_mm → quality_score
harvest_date → sugar_content_brix

![DAG](DAG.jpg)

Confounding: 'region', 'variety'
Collider: 'berry_size', 'cluster_weight','acidity_ph'
Mediator: 'sun_mediator','harvest_date'



## Statistical Model

We will use a **Gamma Regression** because Briz is a positive continuous variable and may be skewed. A beta regression model, would "squish" Brix values from (0,1). Plus gamma regression models model biological scenarios better.

**Model family:** Gamma  
**Link function:** Log (canonical link)



\[
Y_i \sim \text{Gamma}(\mu_i, \kappa)
\]





\[
\log(\mu_i) = \beta_0 + \beta_1 \cdot \text{Sun}_i + \beta_2 \cdot \text{Moisture}_i + \beta_3 \cdot (\text{Sun}_i \times \text{Moisture}_i) + \beta_4 \cdot \text{Rainfall}_i + \beta_5 \cdot \text{pH}_i + \beta_6 \cdot \text{HarvestDate}_i + \beta_7 \cdot \text{ClusterWeight}_i + \beta_8 \cdot \text{BerrySize}_i + \boldsymbol{\beta_9}^\top \mathbf{Variety}_i + \boldsymbol{\beta_{10}}^\top \mathbf{Region}_i
\]




## Statistical Hypotheses

Hypotheses Statistical Notation:

Test whether sun exposure increases Brix after adjustment
H0: B_Sun = 0 vs H1: B_Sun > 0

Test whether soil_moisture_% decreases Brix
H0: B_Moisture = 0 vs H1: B_Moisture < 0

## Statistical Hypotheses
- **Sun exposure effect:**  
  - \(H_0:\ \beta_{1}=0\)  
  - \(H_A:\ \beta_{1}>0\)  

- **Soil moisture effect:**  
  - \(H_0:\ \beta_{2}=0\)  
  - \(H_A:\ \beta_{2}<0\)  

- **Interaction effect (Sun × Soil Moisture):**  
  - \(H_0:\ \beta_{3}=0\)  
  - \(H_A:\ \beta_{3}\neq 0\)



## Potential Workflow
1. Data Cleaning  
2. Exploratory Analysis: Visualizations  
3. Model Fitting: Gamma Regression  
4. Diagnostic Tests: Predicted vs observed plots and residual checks  
5. Inference: Interpret the coefficients with confidence intervals  
6. Blog post: Present results in Quarto with narrative and figures  


## Questions Raised
- Are rainfall and soil moisture too collinear to include together?  
- Should harvest date be treated as a mediator (ripening progression) or a direct predictor?  
- Is the sun × soil moisture interaction consistent across grape varieties, or should stratification be considered?  
- Do nonlinear effects (splines or polynomials) better capture sun exposure or soil moisture relationships?  



## Old info


## Which statistical model do you propose to use? Refer to the descriptions on the course website. How does your response variable from above fit with the response characteristics of the statistical model? If the data need to be transformed, how would you do that? *

The model I will be using for my analysis will be the Beta Regression model. Because of the variability of the Brix scores, scaling these scores between 0 and 1 will be the first priority.


We considered both beta regression (after rescaling °Brix to (0,1)) and gamma regression (on the raw scale). Beta regression aligns with the course’s emphasis on bounded outcomes, while gamma regression may be more natural for concentration data. In either case, we adjust for region and variety as confounders, and explore stratification to assess differences across groups.


Family: Beta
Link function: Logit

Statistical Model:


Y_i ~ Beta(mu,phi,(1 - mu_i)phi)

logit(u_i) = Beta_0 + Beta_1 * Sun_i + Beta_2 * Moisture_i + Beta_3 * (Sun_i + Moisture_i) + Beta_4 * Rainfall_i + Beta_5 * ph_i Beta_6 * HarvestDate + Beta_7 * ClusterWeight_i + Beta_8 * Berry_Size + Variety_i + Region_i


## How I need to aggregate or otherwise process my data?

Data is pretty clean and has multiple variables that are numerical and categorcial. Its all about the framing of the project.

## Methodology

Currently, groupbys by score, wine type and region are generally a plausible stratification/cluster method.

Adjusted for:
- variety
- region

**Interaction**: sun_exposure_hours x soil_moisture_%

Hypotheses Statistical Notation:

Test whether sun exposure increases Brix after adjustment
H0: B_Sun = 0 vs H1: B_Sun > 0

Test whether soil_moisture_% decreases Brix
H0: B_Moisture = 0 vs H1: B_Moisture < 0


## Describe the dataset you're proposing to use. What is it? Where did you find it? What does each observation represent? If you're combining multiple datasets, how will you connect them? * 
 
## What is the question you're going to investigate? What predictor and response variables will be relevant?

In viticulture, sugar content (Brix) is a key indicator of grape ripeness and quality. 

