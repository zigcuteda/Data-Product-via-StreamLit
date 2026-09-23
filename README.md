# Data Product Engineering: Student Performance Predictor

## 1. Project Overview

### Problem Definition
Educational institutions frequently struggle to identify the underlying environmental, behavioral, and academic factors (e.g., sleep, parental involvement, study habits) that most strongly dictate a student's academic success. 

### Target Users
* Teachers
* Academic Advisors
* School Administrators

### Why Machine Learning?
The relationship between 19 different variables (demographics, extracurriculars, previous scores) and a final exam score is highly complex. Machine learning models, specifically ensemble methods, can capture these multi-dimensional interactions and systemic distributions far better than simple rule-based systems.

### Success Criteria
Develop and deploy an interactive dashboard allowing educators to input student metrics to instantly receive a predicted exam score, maintaining an error margin of less than 3 points (MAE < 3).

---

## 2. Data Engineering & Architecture

* **Dataset:** `StudentPerformanceFactors.csv` (6,607 records, 20 features) , [Kaggle link](https://www.kaggle.com/datasets/lainguyn123/student-performance-factors)
* **Target Variable:** `Exam_Score`
* **Data Cleaning:** Categorical columns with missing values (`Teacher_Quality`, `Parental_Education_Level`, `Distance_from_Home`) were imputed using the Mode. Numerical missing values were imputed using the Median.
* **Feature Engineering:** Applied One-Hot Encoding for all categorical variables to transform them into a machine-readable format.
* **Data Leakage Prevention:** All imputation and encoding transformations were structured inside a Scikit-Learn `Pipeline` and fitted strictly on the Training Set (80%) before transforming the Test Set (20%).

---

## 3. Machine Learning Model Development

Three regression models were evaluated to predict the continuous exam score.

### Performance Comparison (Test Set)
1. **Baseline Model (Linear Regression):** MAE = 0.45, R² = 0.77
2. **Model 2 (Random Forest Regressor):** MAE = 1.08, R² = 0.67
3. **Model 3 (Gradient Boosting Regressor):** MAE = 0.69, R² = 0.74

### Final Selection & Justification: Gradient Boosting Regressor
While Linear Regression scored highest on the raw data, it struggles heavily with out-of-distribution inputs (e.g., predicting scores > 100 for perfect students). Gradient Boosting was selected as it provides a robust R² (0.74) while maintaining realistic, bounded predictions suitable for a production dashboard. 

* **Hyperparameters:** `n_estimators=150`, `learning_rate=0.1`.

---

## 4. Key Performance Indicators (KPIs)

To guarantee the success of the data product, performance was evaluated across four distinct domains:

1. **Business KPI (Cost Saving/Retention): Early Intervention Rate**
   * **Measurement:** Percentage of students predicted to score below 65 who were successfully flagged.
   * **Target:** > 85%
   * **Result:** The deployed system flags all inputs predicting < 65, allowing schools to allocate tutoring resources efficiently, saving costs on late-stage remediation.

2. **ML/Model KPIs: MAE and R-Squared**
   * **MAE Target:** < 3.0 points | **Actual:** 0.69 points. 
   * **R² Target:** > 0.70 | **Actual:** 0.74. 
   * **Interpretation:** On average, the model's prediction is off by less than 1 point, and 74% of the variance in a student's final score is explained by the provided variables.

3. **Data Quality KPI: Feature Completeness Pipeline Success**
   * **Measurement:** Percentage of missing values remaining after Pipeline transformation.
   * **Target:** 0% | **Actual:** 0%.
   * **Interpretation:** The Scikit-Learn `SimpleImputer` successfully handled all null values seamlessly in production.

4. **Product/Engineering KPI: Inference Latency**
   * **Measurement:** Time elapsed between execution and UI rendering.
   * **Target:** < 500 ms | **Actual:** < 100 ms.
   * **Interpretation:** The cached model architecture ensures instant inference, providing an excellent user experience.

---

## 5. The Data Product

The final model is exposed through a production-ready **Streamlit dashboard** (`app.py`). 

### Core Features
* **Categorized Inputs:** Variables are logically grouped into Academic Engagement, Lifestyle Environment, and Institutional Factors.
* **Integrated Data Dictionary:** Built-in explanatory tooltips for every variable ensure seamless user navigation.
* **Dynamic Thresholding:** Output alerts dynamically classify the prediction trajectory as "High-Performing" (>= 80), "Average" (65-79), or "At-Risk" (< 65).
* **System Metrics Tab:** Separates the prediction tool from the backend engineering metrics, maintaining a clean UI for the end-user.

---

## 6. Execution (Google Colab)

To run this data product directly in your browser:

1. **Download the Files:** Download the `.ipynb` notebook and the `StudentPerformanceFactors.csv` dataset from this repository.
2. **Launch Colab:** Open the `.ipynb` file in [Google Colab](https://colab.research.google.com/).
3. **Configure the Data Path:** Upload the `StudentPerformanceFactors.csv` file to your Google Drive. In the notebook (Cells 2 and 3), update the `path` variable to match your specific Google Drive file location.
4. **Execute:** Run the cells sequentially. The final cell will generate a `localtunnel` endpoint URL - click this link to access the live Streamlit dashboard.
