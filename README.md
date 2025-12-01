---
title: "EDS 222 Final Project: Grape Quality and Environmental Predictors"
format: html
editor: visual
author: Joshua Ferrer-Lozano
date: 12/1/2025
---

## Overview
This repository contains the code and documentation for my EDS 222 final project.  
The project explores how environmental and physiological factors influence sugar content (°Brix) in grapes, using a **beta regression model**.  
The work is presented as a technical blog post built with Quarto.

## Describe the dataset you're proposing to use. What is it? Where did you find it? What does each observation represent? If you're combining multiple datasets, how will you connect them? * 
 
## What is the question you're going to investigate? What predictor and response variables will be relevant?

In viticulture, sugar content (Brix) is a key indicator of grape ripeness and quality. 

**Main Research Question:** *How does sun exposure, soil moisture, and other environmental and physiological factors influence Brix, and does soil moisture moderate the effect of sun exposure?*

Other Possible Questions to Investigate:

Outcome scaling: Do any °Brix values equal 0 or 1, and how should I transform them for beta regression?

Confounding: Does irrigation or canopy management vary by region/variety enough to require additional adjustment?

Interaction interpretability: Is the sun × soil moisture interaction stable across varieties, or should I allow it to vary (three-way interaction or stratification)?

Nonlinearity: Do sun exposure or soil moisture show nonlinear relationships with °Brix?

Collinearity: How correlated are rainfall and soil moisture, and does including both degrade estimates?

Temporal pathway: Should harvest date be treated as a confounder?


## Key Variables/Predictors:


-   `quality_score`: numeric outcome (rescaled to 0-1 for beta regression)
-   `variety`: categoorical predictor (grape type)
-   `region`: categorical predictor/stratifier
-   `sugar_content_brix`: numeric predictor (sugar concentration)
-   `acidity_ph`: numeric predictor (acidity level)
-   `sun_exposure_hours`: numeric predictor (environmental factor)
-   `soil_moisture_percent`: numeric predictor (environmental factor)
-   `rainfall_mm`: numeric predictor (environmental factor)
-   `cluster_weight_g`: numeric predictor (physiological trait)
-   `berry_size_mm`: numeric predictor (physiological trait)
-   `harvest_date`: temporal predictor (seasonal effect)

# Data

The data I will utilize will be [Grape Quality](https://www.kaggle.com/datasets/mrmars1010/grape-quality) data sourced from Kaggle with a usability score of 10/10.

This data set records various temporal, binary, categorical and numerical variables related to inputs and outputs of grape development. From environmental variables to physiological grape qualities.

## Which statistical model do you propose to use? Refer to the descriptions on the course website. How does your response variable from above fit with the response characteristics of the statistical model? If the data need to be transformed, how would you do that? *

The model I will be using for my analysis will be the Beta Regression model. Because of the variability of the Brix scores, scaling these scores between 0 and 1 will be the first priority.

Family: Beta
Link function: Logit

Statistical Model:


Y_i ~ Beta(mu,omega,(1 - mu_i)omega)

logit(u_i) = Beta_0 + Beta_1 * Sun_i + Beta_2 * Moisture_i + Beta_3 * (Sun_i + Moisture_i) + Beta_4 * Rainfall_i + Beta_5 * ph_i Beta_6 * HarvestDate + Beta_7 * ClusterWeight_i + Beta_8 * Berry_Size + Variety_i + Region_i


## How I need to aggregate or otherwise process my data?

Data is pretty clean and has multiple variables that are numerical and categorcial. Its all about the framing of the project.

## Methodology

Currently, groupbys by score, wine type and region are generally a plausible stratification/cluster method.

Adjusted for:
- variety
- region


Interaction: sun_exposure_hours x soil_moisture_%

Hypotheses Statistical Notation:

Test whether sun exposure increases Brix after adjustment
H0: B_Sun = 0 vs H1: B_Sun > 0

Test whether soil_moisture_% decreases Brix
H0: B_Moisture = 0 vs H1: B_Moisture < 0

## DAG

region → sun_exposure_hours → sugar_content_brix → quality_score
region → rainfall_mm → soil_moisture_percent → sugar_content_brix
variety → sugar_content_brix
variety → acidity_ph → quality_scoresugar_content_brix
acidity_ph → quality_score
cluster_weight_g → sugar_content_brix
berry_size_mm → quality_score
harvest_date → sugar_content_brix

