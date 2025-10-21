# MFS-Interview-2025 Repository
*Interview date: 22 Oct 2025*

## Purpose
Repository for artifacts related to final round interview presentation for MFS on Predicting Employee Turnover.

## Repository Contents
* ```00-README.md```: (You are here) Current file explaining repository contents and purpose
* ```01-IBMAttritionData_raw.csv```: Raw, open-source HR data from IBM containing employee demographics, career details, satisfaction metrics, and attrition label (Yes/No)
* ```02-MFS-EmployeeTurnover-wHyperparamTuning.ipynb```: Jupyter notebook containing details about methodoloy, assumptions, and Python modeling code 
* ```03-ModelComparisonSummary.csv```: Summary table detailing model performance metrics
* ```04-FeatureImportance-AllModels.csv```: Combination of feature importance scores from XGBoost, RandomForest, GradientBoosting, and LogisticRegression models
* ```04a-LogisticRegression-FeatureImportance.csv```: Feature importance scores for LinearRegression model
* ```04b-RandomForest-FeatureImportance.csv```: Feature importance scores for RandomForest model
* ```04c-GradientBoosting-FeatureImportance.csv```: Feature importance scores for GradientBoosting model
* ```04d-XGBoost-FeatureImportance.csv```: Feature importance scores for XGBoost model
* ```05-IBMAttritionData_cleaned.csv```: Post-processed IBM HR data (dropped columns, engineered features)
* ```06-MFS-EmployeeTurnover-Dashboard.twb```: Tableau dashboard summarizing findings and recommendations for HR stakeholders
* ```07-PredictingEmployeeTurnover-Keough.pdf```: Final presentation on workflow, findings, and recommendations for predicting employee turnover

## Workflow Overview

### 1. Load & Explore Data
I began the analysis by importing my locally-stored copy of the IBM HR Attrition dataset (downloaded from Kaggle) and explored the contents to get a feel for the variables, their types, and their null-counts. After verifying there were no missing values to impute of omit, I moved on to EDA.

### 2. Exploratory Data Analysis (EDA)
Next, I performed EDA on the HR dataset to dive deeper into the vairables and their relationship to the target variable, Attrition. Below, I have provided an overview of the types of visualizations I created. These visualizations can be found in the Jupyter Notebook within this repository.

**Bar Charts**
* Attrition by Department
* Attrition by Years at Company (Tenure)
* Attrition by Gender
* Attrition by Education Field
* Attrition by Job Role
* Attrition by Marital Status
* Attrition by Overtime
* Attrition by Business Travel Frequency

**Box-and-Whisker Plots**
* Attrition by Age
* Attrition by Distance from Home
* Attrition by Monthly Income
* Attrition by Years in Current Role
* Attrition by Years Since Last Promotion
* Attrition by Percent Salary Hike
* Attrition by Performance Rating
* Attrition by Job Satisfaction

**Correlation Heat Map**
In addition to simple bar charts and box-and-whisker plots to visualize categorical and numeric variables, respectively, I also created a correlation heat map from a subset of the IBM HR data to include *only* vairable of numeric data type.

### 3. Preprocessing & Feature Engineering
From exploring the dataset, it was clear that certain columns did not lend any insight into the data and could thus be removed:
* ```EmployeeCount```: Equal to '1' across all rows, as each row pertains to an individual employee
* ```EmployeeNumber```: An arbitrary number assigned to an employee (likely for identification purposeos)
* ```StandardHours```: Equal to '80' across all rows, as each employee works 80 hours per biweekly pay period
* ```Over18```: Equal to 'Y' across all rows, as each employee is over the age of 18

I then encoded the target variable, Attrition, as ```Yes = 1``` and ```No = 0``` for modeling purposes and performed some feature engineering to strengthen analysis:
* ```OverallSatisfaction```: An average value summarizing the employee's overall satisfaction based on ```JobSatisfaction```, ```EnvironmentSatisfaction```, ```RelationshipSatisfaction```, ```WorkLifeBalance```, and ```JobInvolvement```. The constituent fields were subsequently dropped from the dataset. 
* ```Income```: A categorical bucketing based on the ```MonthlyIncome``` field (categorized into 'Low', 'Medium', and 'High'). The various 'Income' and 'Rate' fields were subsequently dropped from the dataset.
* ```PromotionRatio```: A ratio of ```YearsSinceLastPromotion``` to ```YearsAtCompany```, providing a measure of the gap in time between promotions, where a ratio value closer to zero would indicate a more recently-promoted employee.

### 4. Modeling
To begin the modeling phase, I first separated the target variable, Attrition, from the rest of the dataset and split each into training (80%) and testing (20%) sets (```X_train```, ```X_test```, ```y_train```, ```y_test```), stratifying the split by Attrition to address class imbalance. Stratification ensures that the proportions of different classes in the target variable are preserved in both the training and testing sets.

Next, I standardized the numeric features, one-hot encoded the categorical features, and created the appropriate transformer and preprocessor pipelines for the models. I decided to compare several classificatin models (Logistic Regression, Random Forest, Gradient Boosting, SVM, and XGBoost) to determine which best performed the task of predicting employee turnover. After training and performing hyperparameter tuning on the models, I evaluated the models on the test set and compared metrics to find that Logistic Regression not only had the best predictive accuracy and F1 score across the evaluated models, but it also had the best *recall*, which is critical for this use case because it indicates the model enables HR decision-makers to catch more employees who are at risk of leaving, even at the expense of a few false positives.

### 5. Explainability
For the four models that support feature importance extraction -- ```Logistic Regression```, ```Random Forest```, ```XGBoost```, and ```GradientBoosting``` -- I calculated the scores of the most influential features for predicting employee turnover and created bar charts for each model that displayed these features in order of descending importance (see Jupyter notebook). 

Across all models, the most importance features for predicting employee turnover included Job Role, Overall Satisfaction, Overtime, Business Travel, and Years Since Last Promotion. 

### 6. Data Export

Lastly, I exported the following datasets for use in constructing my Tableau dashboard:
* **Model Performance Summary**: Summary table of model metrics for each of the five models evaluated
* **Feature Importance**: Feature importance scores for each of the four models that support feature importance extraction
* **Cleaned IBM HR dataset**: Post-processed dataset after cleansing and feature engineering

### 7. Tableau Dashboard
The final step of my workflow was creating a Tableau dashboard to showcase my findings, insights, and business recommendations to HR stakeholders. See file number 06 within this repository for the packaged Tableau workbook, or navigate to this [Tableau public link](https://public.tableau.com/views/MFS-EmployeeTurnover-Dashboard/MainPage?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link).


## Assumptions

In modeling employee turnover with this IBM HR Attrition dataset, we are implicitly making several assumptions, which I will categorize into Data, Modeling, and Business Assumptions.

**Data Assumptions**

*Representative Sample of Employees*
* We assume that the IBM dataset represents the population of employees we want to make predictions for, but since this is mock data, it may not reflect real-world HR dynamics

*Sampling Bias & Completeness*
* We assume the data has not excluded certain groups (e.g., interns, senior executives); if sampling bias exists, this could lead to over- or under-predicting for certain groups
* We also assume that all columns are measured correctly and consistently (e.g., satisfaction fields), when in reality, HR survey data is often missing

*Temporal Stability & Stationarity*
* A large assumption made with this dataset is that the relationships between features and attrition remain stable over time, when things like management or policy changes can alter employee satisfaction features dramatically

**Modeling Assumptions**

*Class Balance*
* The dataset is very class-imbalanced (i.e., attrition is relatively rare), thus we assume model metrics are still valid under class imbalance

*Feature Independence*
* Models like Logistic Regression assume features are not strongly correlated, however, there are predictors like 'MonthlyIncome' and 'JobLevel' which are inherently correlated and can thus distort coefficients

*Independent & Identically Distributed (IID) Samples*
* Most algorithms assume each employee in the dataset is independent from the others, when in reality, some will inevitably be linked. For example, employees on the same team or who share a manager may not be independent, as they share their environment and leadership

**Business Assumptions**

*Actionable Predictions & Causality Direction*
* We assume that model patterns like 'low job satisfaction leads to higher attrition risk' are inherently actionable, but it is possible that these conditions are instead caused by an unmeasured factor like poor management
* We also assume features must influence attrition and not the other way around; for example, low performance ratings may occur because the employee was already planning to leave, rather than the low performance rating causing the employee to leave

*Ethical & Privacy Considerations*
* We are assuming that using these features for prediction is ethical and complies with privacy standards, like not using demographic information in ways that could create bias
