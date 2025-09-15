![Girl drinking water](https://media.istockphoto.com/id/474251868/photo/african-black-child-drinking-fresh-water-from-tap.jpg?s=2048x2048&w=is&k=20&c=GU8vIanquM5innnLJpqa8AyJE98LPhQCVaQ8Gyhh_PY=)

#### **AUTHOR:** [NORMAN](https://www.linkedin.com/in/norman-mwapea-49502a264/)

# FLOW STATE: MAPPING TANZANIA'S WATER WELLS

Access to safe and reliable water remains a daily uncertainty for millions of Tanzanians.  
This project analyzes the **Tanzania Water Wells dataset** to clean, explore and model rural
water point data to support proactive maintenance, smarter investments and improved
community resilience.

## BACKGROUND

- **Population at risk:** ~70.5 million people in 2025; about one-third rely on wells and boreholes.
- **Problem:** Many wells fail within a few years because of poor construction, environmental stress, limited maintenance or lack of spare parts.
- **Impact:** Health risks (waterborne diseases), economic loss, and disproportionate burden on women and children fetching water.

The Tanzanian Water Sector Development Program (WSDP) and SDG-6 (Clean Water and Sanitation) recognize that **predictive insights into well functionality** are key to sustainable access.

## OBJECTIVES

- **Predict well status:** Functional vs. non-functional (binary classification after removing “needs repair”).
- **Optimize resource allocation:** Prioritize high-risk wells for maintenance.
- **Improve investment efficiency:** Focus funds where they have the greatest impact.
- **Support data-driven planning:** Provide actionable insights to policymakers, NGOs and donors.
- **Promote transparency and accountability:** Track well performance over time.

## STAKEHOLDERS

The beneficiaries of this project are:

1. Tanzanian Ministry of Water
2. Local Communities 
3. Non-Governmental Organizations (NGOs) 
4. Donors and Development Partners
5. Local Governments (District & Regional Councils)
6. Private Sector & Contractors
7. ommunity-Based Water Committees (CBWCs)
8. Data Scientists and Researchers
9. Monitoring & Evaluation Teams 
10. Policy Makers & Legislators 

At the center of these stakeholders are Tanzanian households, schools, and clinics, whose health, dignity, and productivity depend on reliable access to safe water.

## DATA SOURCE

- **Dataset:** [DrivenData “Pump It Up” competition](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/data/)
- **Provider:** Taarifa in collaboration with the Tanzanian Ministry of Water.
- **Scope:** 59,400 rural water points; 41 attributes on location, construction, management, usage, quality, etc.
- **Files used:**
  - train_values.csv – features
  - train_labels.csv – target status group
  - test_values.csv – test features

### KEY FEATURES
- **Target:** status_group (functional / functional needs repair / non-functional)
- **Location:** gps_height, latitude, longitude, region, district_code, basin
- **Management:** funder, installer, scheme_management, management_group
- **Water characteristics:** water_quality, quantity_group, source_type
- **Usage/Population:** population, payment_type

## DATA PREPARATION

1. **Merge** training features with target variable for unified analysis.
2. **Drop redundant columns** (e.g overlapping management fields, id, num_private); reduced from 41 to 18.
3. **Handle missing values** (installer, permit).
4. **Convert data types** (dates to datetime, extract year).
5. **Check duplicates** – none found.
6. **Handle outliers** – retain genuine extremes but clean obvious entry errors.
7. **Feature engineering:**
   - One-hot encode categorical variables.
   - Label-encode target.
   - Log-transform and scale numerical features.

## EXPLORATORY DATA ANALYSIS (EDA) HIGHLIGHTS

- **Elevation:** Most wells 1,000–1,500 m; few at extremes.
- **Population served:** 75 % of waterpoints serve ≤300 people; high demand increases breakdown risk.
- **Status:** ~15 000 non-functional points; many functional but some need preventive maintenance.
- **Management:** 32 579 managed by user groups; smaller shares by commercial, parastatal entities.
- **Payment:** “Never pay” and unknown schemes correlate with highest failure rates; annual/per-bucket payment schemes correlate with better functionality.
- **Water Quality:** Soft water dominates; salty/milky sources fail more often.
- **Technology:** Motorpumps (88.9 % functional) outperform wind-powered or India Mark III (≈27 % functional).
- **Permits:** Points with permits slightly more reliable (57.8 % vs. 52.0 % functional).
- **Regional/Basin disparities:** Iringa (78.2 % functional) vs. Mwanza (27.8 % functional); Lake Nyasa basin (74.8 %) vs. Ruvuma/Southern Coast (37.2 %).

## MODELING

We reframed the “Pump It Up” challenge as a binary classification task (Functional vs. Non-Functional wells) because the “Functional-needs-repair” class was both ambiguous and only 6.6 % of the data.  
Machine learning, rather than simple descriptive analysis, is appropriate because:

- The dataset is large (≈59 k records) and highly multivariate (geographic, managerial, technical, quality features).
- Relationships between predictors and well status are nonlinear and complex (e.g interaction of technology, region, and payment type).
- Stakeholders need proactive, record-level predictions (“which well is at risk?”) not just summary trends.

This justified trying ensemble methods (Random Forest, XGBoost) with imbalance handling, rather than only logistic regression or univariate thresholds.

### RESULTS
We tuned models with 5-fold cross-validation and compared them on metrics most relevant to the business need: **recall on non-functional wells** (catch as many failing wells as possible) and overall **accuracy/F1** (avoid too many false alarms).

| Model & Strategy                    | Test Accuracy | Minority Recall | F1-Score (Macro) | ROC-AUC |
|------------------------------------|---------------|-----------------|-----------------|---------|
| **XGBoost + class weights (GridSearchCV)** | 86.77 % | 82 % | 0.86 | 0.935 |
| Random Forest + SMOTE (GridSearchCV) | 86.01 % | 82 % | 0.85 | 0.931 |
| XGBoost + class weights (Randomized) | 86.48 % | 82 % | 0.86 | 0.934 |

- **Why these metrics:** recall on non-functional wells = fewer at-risk wells missed; ROC-AUC = model discrimination; F1 = balance between precision and recall.
- “82 % recall” means roughly 8 out of 10 failing wells would be flagged in advance for maintenance.

### LIMITATIONS

- **Data drift and timeliness:** The dataset reflects historical conditions. Management practices, technology usage, and environmental stress may have changed since collection, so predictions on today’s wells may be less accurate without periodic retraining.
- **Dropped class granularity:** The “functional but needs repair” category was excluded to stabilize and simplify the model. This improves performance but loses detail about borderline cases. In practice, some wells flagged as functional may still need attention.
- **Regional and basin heterogeneity:** Model performance can vary by region, basin, or extraction technology. A model trained on national data may underperform in areas with unique conditions or very few records.
- **Incomplete and inconsistent data:** Unknown categories can lead to biased predictions for underrepresented groups.
- **Non-static factors:** Sudden shocks (droughts, floods, contamination) and behavioral changes aren’t captured by static historical features, so the model may miss emerging risks.
- **Operational deployment risks:** If predictions are used directly to prioritize maintenance, false negatives (failing wells missed) could leave communities without water; false positives (healthy wells flagged) could waste scarce maintenance resources. A human-in-the-loop review is recommended.

If deployed in production, the model should be periodically retrained with fresh data and monitored for performance degradation, especially in under-represented regions or technologies.

### RECOMMENDATIONS
- **Operational use:** Adopt the XGBoost with class weights and GridSearchCV as the production model; track recall on non-functional wells as a key KPI.
- **Maintenance targeting:** Use predictions to schedule preventive maintenance visits, especially in regions and technologies historically prone to failure.
- **Data improvements:** Collect more records for the “needs repair” category if granular triage is required; fill missing fields to improve feature quality.
- **Policy planning:** Combine model outputs with cost and logistics data to optimize resource allocation across districts and basins.

This approach balances technical performance with actionable insights for stakeholders, shifting the focus from building new wells to sustaining existing ones.

## INSIGHTS AND RECOMMENDATIONS

- **Targeted Maintenance:** Prioritize older infrastructure, high-failure regions and basins.
- **Strengthen Community Management:** Build capacity in Village Water Committees.
- **Promote Reliable Payment Systems:** Annual or per-bucket contributions support sustainability.
- **Invest in Proven Technologies:** Gravity-fed, motorpumps, rope pumps; phase out high-failure types.
- **Monitor Water Quality:** Regular testing in poor-quality or abandoned sources.
- **Close Data Gaps:** Fill unknown management, payment or quality fields for better planning.
- **Model Deployment:** Adopt XGBoost with class weights; monitor recall on non-functional wells and retrain periodically.

For a more detailed view of this workflow, preview the notebook in the Notebooks folder or clone the repository.

# REPOSITORY STRUCTURE

.  
├── Data/  
│   ├── Test set values.csv                 # Test features  
│   ├── Training set labels.csv             # Training labels  
│   └── Training set values.csv             # Training features  
├── Image/  
│   └── Ahjin Logo.png                      # Company logo  
├── Notebooks/  
│   └── index.ipynb                         # Jupyter notebook (cleaning, EDA & modeling)  
├── .gitignore                              # Git ignore rules  
├── LICENSE                                 # License file  
├── PRESENTATION.pdf                        # Project presentation  
├── README.md                               # Project overview and instructions  
└── TANZANIA WATER WELLS DATA REPORT.pdf    # Data report  


