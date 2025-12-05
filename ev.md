---
layout: default
title: "EV Research"
permalink: /ev/
---

# Electric Vehicle (EV) Research

This project investigates the economic, policy, and spatial drivers of electric vehicle (EV) adoption across the United States using state-level data from 2016–2023.

## Overview

My EV research focuses on understanding **how economic conditions, infrastructure, and policy incentives shape EV adoption patterns across U.S. states**, with three complementary components:

- **Cross-sectional analysis (2023):** Identifying key factors associated with current EV adoption.
- **Spatial-temporal panel analysis (2016–2023):** Studying diffusion and regional spillover effects.
- **Forecasting for California:** Predicting long-term EV adoption using logistic and diffusion models.

Together, these analyses provide both a **current snapshot** of EV adoption and a **forward-looking projection** for future growth.


## Data

We assembled a comprehensive state-level dataset from publicly available government and research sources. Key datasets include:

- **EV adoption by state (2016–2023)** – registered electric vehicles. [DATA](https://github.com/mcjian09/EV-Adoption-Across-United-States/blob/main/data/EV_ADOPTION16-23.csv)
- **EV adoption with state-level covariates (2023)** – income, fuel prices, infrastructure, incentives, urbanization. [DATA](https://github.com/mcjian09/EV-Adoption-Across-United-States/blob/main/data/2023_EV_Data_by_state.csv)
  
Primary data sources include:
- U.S. Department of Energy (AFDC)  
- U.S. Energy Information Administration (EIA)  
- Federal Highway Administration (FHWA)  
- U.S. Census Bureau  
- Michigan State University IPPSR (policy diffusion networks)

## Methods
 
Our analysis combines classical statistical modeling with spatial and diffusion-based methods:

- **Correlation analysis and linear regression**
- **Lasso regression and stepwise feature selection**
- **Spatial-temporal panel modeling** with temporal and spatial lags
- **Growth modeling for forecasting**
- **Bass diffusion model** for innovation vs. imitation dynamics

---

## Key Findings

- **Income, gasoline prices, and incentives** show strong positive associations with EV adoption.
- **Electricity prices** are negatively correlated with adoption.
- **Urban road density** is positively related to higher adoption, while charging station density alone is not a strong direct predictor at the state level.
- EV adoption exhibits **strong temporal persistence and regional clustering**, consistent with **technology diffusion theory**.
- **California’s EV adoption is projected to reach ~50% by 2035** under a logistic growth model.
- Bass diffusion results suggest that **imitation effects dominate innovation**, indicating strong social contagion in EV adoption.

---

## Manuscript

- **M. Jian and J. Sun (2025).** *Electric Vehicle Adoption Across the U.S.: Economic Patterns, Market Dynamics, and Future Projections.* Under review.

  <br>
<iframe src="https://drive.google.com/file/d/1VfTiQssjp7QLhpjPG7fcQuse7lAFHf9E/preview" style="width:100%; border:none;"></iframe>
<!-- <iframe src="https://docs.google.com/document/d/e/2PACX-1vTbouskMIylLUBVscahfJsFYOIng851Dpxmg3UOwtr4UFkr0wHjMprCgOtdvuaQY4NEVlbzl1xPJkXs/pub?embedded=true" style="width:100%; height:16300px; border:none;"></iframe> -->
