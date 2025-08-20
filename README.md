
# Insurance Customer Response Prediction

## Project Overview

This project focuses on building a robust machine learning model to predict whether a customer will be interested in purchasing a vehicle insurance policy. By leveraging historical customer data, the goal is to enable the insurance company to optimize its marketing campaigns, reduce wasted spend, and increase conversion rates by precisely targeting high-potential leads.

## Business Problem

An insurance company aims to launch a new vehicle insurance product. To ensure an efficient and high-impact marketing campaign, they need to identify which customers from their existing database (who currently do not hold vehicle insurance with them) are most likely to respond positively to an offer. This predictive capability directly translates to significant cost savings and revenue growth for the business.

## Dataset

The dataset contains comprehensive information about individual customers, their demographics, vehicle details, and previous interactions.

*   **Size:** 381,109 customer records and 12 features.
*   **Key Features:** `Gender`, `Age`, `Driving_License`, `Region_Code`, `Previously_Insured`, `Vehicle_Age`, `Vehicle_Damage`, `Annual_Premium`, `Policy_Sales_Channel`, `Vintage`.
*   **Target Variable:** `Response` (1 = Interested, 0 = Not Interested).
*   **Challenge:** The dataset exhibited significant **class imbalance**, with only **12.26%** of customers showing interest.

## Project Phases & Methodology

1.  **Dataset Overview & Initial Discoveries:**
    *   Initial inspection of data types, dimensions, and statistical summaries.
    *   Identified the target variable (`Response`) and recognized the severe class imbalance.
    *   Discovered `Previously_Insured` as a **data leakage** feature due to its perfect correlation with non-response.

2.  **Exploratory Data Analysis (EDA):**
    *   **Univariate Analysis:** Explored distributions of individual features (`Age` skewness, `Annual_Premium` extreme skewness).
    *   **Bivariate Analysis:** Crucially examined feature relationships with `Response`.
        *   **Key Predictors:** `Vehicle_Damage` (most influential), `Vehicle_Age`, and `Age` showed strong correlations.
        *   Confirmed `Previously_Insured` as a leaker, leading to its removal.
    *   **Multivariate Analysis:** Utilized correlation heatmaps to understand inter-feature relationships and multicollinearity (found `Vehicle_Damage` and `Previously_Insured` highly correlated).

3.  **Data Cleaning & Preprocessing:**
    *   **Column Removal:** `id` (identifier) and `Previously_Insured` (data leak) were dropped.
    *   **Transformation:** `Annual_Premium` was log-transformed (`np.log1p`) to handle its extreme skewness.
    *   **Data Splitting:** Data was split into 80% training and 20% testing sets, with `stratify=y` to preserve the class imbalance ratio.
    *   **Feature Engineering:**
        *   **Age Bins:** `Age` was grouped into `Age_Group` categories (`Young_Adult`, `Middle_Aged`, `Senior`).
        *   **Interaction Term:** `Premium_per_Age` (`Annual_Premium / Age`) was created.
        *   **Categorical Reclassification:** `Region_Code` and `Policy_Sales_Channel` were correctly treated as categorical features (one-hot encoded) rather than numerical, which significantly improved model performance.
    *   **Pipelining:** A `ColumnTransformer` pipeline was implemented for robust scaling (`StandardScaler`) of numerical features and one-hot encoding (`OneHotEncoder`) of categorical features.

4.  **Model Selection & Optimization:**
    *   **Model Candidates:** Logistic Regression (baseline), Decision Tree, Random Forest, and **LightGBM**.
    *   **Imbalance Handling:** All models utilized `class_weight='balanced'` or `is_unbalanced=True` to mitigate bias towards the majority class.
    *   **Evaluation Metrics:** Focused on **ROC-AUC**, **Precision**, **Recall**, and **F1-Score** due to data imbalance (accuracy was deemed misleading).
    *   **Threshold Tuning:** A critical step where the optimal probability threshold was identified (e.g., ~0.21 for LightGBM) using the Precision-Recall curve to maximize the F1-Score, balancing lead identification with marketing efficiency.

5.  **Results and Interpretation:**
    *   **Champion Model:** **LightGBM (Tuned)**
    *   **Performance Metrics (on Test Set):**
        *   **ROC-AUC:** **84.70%** (Increased from 82.63% (tuned LR) due to Feature Engineering & LightGBM's power)
        *   **F1-Score:** **44.81%** (Achieved a significant balance between Precision and Recall)
        *   Precision: 31.87%
        *   Recall: 75.42%
    *   **Key Business Insights:**
        *   Customers with **previous `Vehicle_Damage`** are the strongest predictors for positive response.
        *   `Vehicle_Age` and `Age` are also significant factors.
        *   The model successfully identifies a high proportion of genuinely interested customers while maintaining reasonable precision for targeted campaigns.

## Project Deliverables

*   **Jupyter Notebooks:** Documenting each step of the analysis, preprocessing, modeling, and evaluation.
*   **`insurance_predictions_full_dashboard.xlsx`:** An Excel file containing the full dataset enriched with the model's `Prediction_Probability` and `Predicted_Response` for all 381,109 customers. This file is ready for Power BI/Tableau.
*   **PowerPoint Presentation (`.pptx`):** A professional summary of the project, problem definition, methodology, results, and actionable business recommendations.
*   **Power BI Dashboard (Concept/Screenshots):** Interactive visualizations (e.g., Lead Quality vs. Model Score, Customer Profile by Probability) to empower business users.

