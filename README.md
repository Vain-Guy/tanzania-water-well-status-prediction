<div align="center">
  <img src="https://media.istockphoto.com/id/474251868/photo/african-black-child-drinking-fresh-water-from-tap.jpg?s=1024x1024&w=is&k=20&c=VBE3pYzkkeVuYl2DmacOzzyT6uRCyqq9A8FnLRdMAbE=" 
       alt="Girl drinking water from faucet"
       style="width:400px; border-radius:15px;">
</div>

# Tanzania Water Wells: Predictive Modeling for Sustainable Access


## Overview
Access to safe and reliable water remains a daily uncertainty for millions of Tanzanians.  
In 2025, Tanzania’s population is estimated at **70.5 million**, with nearly **one in three people** (~24–25 million) relying on wells and boreholes as their primary drinking water source.  
These wells are lifelines for **health, livelihoods, and dignity**, yet many fail within just a few years.

This project applies **machine learning** to predict whether a water well is **functional** or **non-functional**, enabling smarter interventions, resource allocation, and proactive maintenance.

## Business Understanding

### Problem Statement
Water points across Tanzania frequently break down due to poor maintenance, unsuitable technologies, and lack of funding. With limited resources, decision-makers need to **prioritize which wells require urgent attention**.

### Objectives
- Predict the **operational status** of water wells.  
- Identify **key drivers of functionality** (e.g., water quality, pump type, management structure).  
- Support NGOs, governments, and local communities in **targeting repairs and investments**.  

### Success Metrics
- **Recall for minority class (non-functional)**: Ensure broken wells are not overlooked.  
- **ROC-AUC**: Quantifies model discrimination beyond raw accuracy.  
- **Balanced performance** across all classes.  

### Stakeholders
- Tanzanian Ministry of Water  
- NGOs (WaterAid, World Bank, UNICEF, etc.)  
- Local communities & village water committees  

### Scope & Limitations
- Dataset: Historical (collected up to 2013), Tanzania-specific.  
- Predictions limited by available features (no direct funding/weather data).  
- Complementary to, not a replacement for, field-based monitoring.  

## Data Understanding

### Dataset
- Source: [DrivenData Pump It Up Challenge](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/data/)  
- **59,400+ records**, 40+ features.  
- Target: `status_group` (functional vs non-functional).  

### Features
- **Administrative**: `funder`, `installer`, `management`, `scheme_management`  
- **Technical**: `extraction_type`, `waterpoint_type`, `construction_year`  
- **Hydrological**: `quantity`, `quality`, `source_type`  
- **Geographical**: `gps_height`, `region`, `basin`  
- **Demographic**: `population`  

### Target Breakdown
- Functional: ~54%  
- Non-functional: ~46%  

![Target Distribution](results/target_distribution.png)

## Data Cleaning & Feature Engineering
- Dropped redundant columns (`scheme_management`, `id`, `num_private`).  
- Imputed missing values (`construction_year`, `gps_height`).  
- Consolidated rare categories in `installer` and `funder`.  
- OneHotEncoded categorical features.  
- Used **SMOTE** to balance class distribution.  

## Exploratory Data Analysis

- **Population**: Right-skewed; median = 150, mean = 269.  
- **Water Quality**: Salty & milky strongly associated with failure.  
- **Pump Types**: Rope and motorpumps outperform older handpumps.  
- **Management**: Community-managed schemes vary widely in reliability.  
- **Geographic disparities**: Iringa, Shinyanga, Dodoma show higher breakdowns.  

![Water Quality vs Status](results/water_quality_status.png)  
![Pump Type vs Status](results/pump_type_status.png)  
![Region Breakdown](results/region_breakdown.png)  

## Modeling

### Models Tested
- Logistic Regression  
- Random Forest (GridSearchCV & RandomizedSearchCV, with SMOTE)  
- XGBoost (weighted & tuned with RandomizedSearchCV)  

### Baseline
- Dummy Classifier: Accuracy ≈ 54%, Recall (minority) = 0%.  


## Results & Evaluation

### Metrics Summary
- **Random Forest (RandomizedSearchCV)**:  
  - Accuracy: 80%  
  - Minority Recall: 82%  
- **Random Forest (GridSearchCV)**: Overfitted (100% train, 86% test).  
- **XGBoost (Weighted)**: Best balance overall, ROC-AUC ≈ 0.92.  

![ROC Curves](results/roc_curves.png)  
![Confusion Matrix](results/confusion_matrix.png)  

### Feature Importance
Top predictors:  
- Water quality  
- Extraction type  
- Construction year  
- Region/basin  
- Population  

![Feature Importance](results/feature_importance.png)  

## Conclusions & Recommendations
- **Most wells are functional**, but sustainability challenges persist.  
- **Quality of source** and **technology type** are strong predictors of breakdown.  
- **Community management structures** need technical training & oversight.  
- **Geographic disparities** highlight regions requiring urgent support.  

**Recommendations**:  
1. Prioritize high-risk regions for **maintenance funding**.  
2. Scale up **reliable extraction technologies** (rope/motorpumps).  
3. Strengthen **community management capacity**.  
4. Deploy this **predictive model operationally** for proactive repairs.  

## Repository Structure
