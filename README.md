Nata Supermarkets -- Customer Analytics
1. Project Overview
This project analyses customer-level data from Nata Supermarkets to
understand customer demographics, purchasing behaviour, customer
segments, and the factors associated with total customer spending.
The notebook follows an end-to-end analytics workflow:
Data Loading → Data Cleaning → Exploratory Analysis → Customer
Segmentation → Correlation Analysis → Regression Modelling → Model
Evaluation → Business Strategies
The analysis uses K-Means clustering to segment customers and
Multiple Linear Regression to build a preliminary model for total
customer purchases.
---
2. Objectives
The main objectives of the analysis are to:
Understand the structure and quality of the supermarket customer
dataset.
Identify and treat implausible observations and income outliers.
Analyse customer demographics such as education and marital status.
Segment customers using K-Means clustering.
Identify variables associated with total customer spending.
Examine multicollinearity among numerical variables.
Build a preliminary linear regression model to explain/predict total
purchases.
Evaluate the regression model using training metrics, 10-fold
cross-validation, and a held-out test set.
Translate analytical findings into customer-segment-based
supermarket strategies.
---
3. Dataset
The notebook expects an Excel file named:
``` text
nata_supermarket_data.xlsx
```
The analysis reads the `marketing` sheet:
``` python
df = pd.read_excel(
    '/content/nata_supermarket_data.xlsx',
    sheet_name='marketing'
)
```
The original dataset contains customer demographic, household, purchase,
channel, campaign-response, and complaint-related variables.
Important variables include:
Variable                         Description
---
`ID`                             Customer identifier
`Year_Birth`                     Customer year of birth
`Education`                      Education category
`Marital_Status`                 Marital-status category
`Income`                         Customer income
`Kidhome`                        Number of children in the household
`Teenhome`                       Number of teenagers in the household
`Dt_Customer`                    Customer enrolment date
`Recency`                        Days since the customer's last purchase
`MntWines`                       Spending on wine
`MntFruits`                      Spending on fruits
`MntMeatProducts`                Spending on meat products
`MntFishProducts`                Spending on fish products
`MntSweetProducts`               Spending on sweet products
`MntGoldProds`                   Spending on gold products
`NumDealsPurchases`              Purchases made using deals
`NumWebPurchases`                Purchases through the web
`NumCatalogPurchases`            Purchases through the catalogue
`NumStorePurchases`              Purchases through stores
`NumWebVisitsMonth`              Monthly web visits
`AcceptedCmp1`--`AcceptedCmp5`   Campaign acceptance indicators
`Complain`                       Complaint indicator
---
4. Technologies and Libraries
The project is implemented in Python using:
Pandas -- data manipulation and analysis
NumPy -- numerical calculations
Matplotlib -- data visualisation
Seaborn -- statistical visualisation
Scikit-learn -- preprocessing, clustering, regression, and model
evaluation
Main Scikit-learn components used:
``` python
StandardScaler
KMeans
train_test_split
LinearRegression
mean_squared_error
mean_absolute_error
r2_score
cross_val_score
KFold
```
---
5. Data Preparation
5.1 Data Loading
The marketing sheet is loaded into a Pandas DataFrame.
5.2 Data Type Conversion
`ID` is converted from numerical format to string because it is an
identifier.
`Dt_Customer` is converted into a datetime variable.
5.3 Removing Unnecessary Variables
The last three columns of the original dataset are removed because they
are not required for the analytical objective.
5.4 Birth-Year Cleaning
The distribution of `Year_Birth` is examined to identify implausible
observations.
Customers with:
``` text
Year_Birth < 1940
```
are removed from the analysis.
5.5 Income Outlier Treatment
Income is examined using both a distribution plot and a boxplot.
The Interquartile Range (IQR) method is used:
``` text
IQR = Q3 − Q1

Upper Bound = Q3 + 1.5 × IQR
```
Observations with income above the calculated upper bound are removed.
5.6 Missing-Value Check
The notebook checks all variables using:
``` python
df.isnull().sum()
```
This is performed before the modelling workflow.
5.7 Marital-Status Consolidation
The very small categories:
`Absurd`
`Alone`
`YOLO`
are consolidated into:
``` text
Single
```
This reduces sparse categorical groups before segmentation.
---
6. Preparing the Machine-Learning Dataset
Customer `ID` is removed because it is an identifier and does not
represent customer behaviour.
The enrolment date is converted into the number of days from:
``` text
31 December 2014
```
This transforms the date into a numerical measure representing customer
tenure.
---
7. Feature Standardisation
K-Means is distance-based, so numerical variables with different scales
are standardised using:
``` python
StandardScaler()
```
Categorical variables and binary campaign/complaint indicators are
excluded from standardisation.
The categorical variables:
`Education`
`Marital_Status`
are subsequently converted into dummy variables using one-hot encoding.
---
8. Customer Segmentation -- K-Means
8.1 Why K-Means?
K-Means is used to group customers who have similar demographic and
behavioural characteristics.
The algorithm assigns each customer to a cluster based on the selected
features.
8.2 Choosing the Number of Clusters
The notebook uses the Elbow Method.
For values of K from 1 to 9, the Sum of Squared Errors (SSE), also known
as inertia, is calculated.
The elbow plot is then used to select the number of clusters.
The notebook selects:
``` python
optimal_k = 3
```
8.3 Final K-Means Model
The final clustering model is:
``` python
KMeans(
    n_clusters=3,
    random_state=206,
    n_init=10
)
```
Each customer receives a cluster label.
Cluster means are then calculated to profile the resulting customer
segments.
---
9. Creating the Target Variable
A new variable called:
``` text
total_purchases
```
is created by adding spending across six product categories:
``` text
Wine
+ Fruits
+ Meat Products
+ Fish Products
+ Sweet Products
+ Gold Products
```
Conceptually:
``` text
Total Purchases =
MntWines
+ MntFruits
+ MntMeatProducts
+ MntFishProducts
+ MntSweetProducts
+ MntGoldProds
```
The six original product-spending variables are then removed from the
modelling dataset to avoid redundancy when total purchases is used as
the dependent variable.
---
10. Correlation Analysis
Correlation analysis is conducted to identify numerical variables that
have stronger linear associations with `total_purchases`.
The notebook also generates a correlation heatmap.
An important interpretation principle used in the analysis is:
> Correlation indicates association, not causation.
Therefore, a strong correlation between two variables does not by itself
establish that one variable causes changes in the other.
---
11. Multicollinearity Analysis
A custom function is used to identify pairs of variables with high
absolute correlation.
The notebook calls the function with:
``` python
threshold=0.5
```
This helps identify potential multicollinearity among explanatory
variables.
Multicollinearity can make regression coefficients more difficult to
interpret because predictor variables may contain overlapping
information.
---
12. Exploratory Visualisations
The notebook generates several visual analyses, including:
Customer demographics
Year-of-birth distribution
Income distribution
Education distribution
Marital-status distribution
Data-quality analysis
Income boxplot
Customer enrolment-date distribution
Customer segmentation
Elbow plot
Cluster profiles
Spending behaviour
Income vs. total purchases by cluster
Income vs. total purchases by education
Income vs. total purchases by marital status
Statistical relationships
Correlation matrix heatmap
---
13. Preliminary Regression Model
The objective of the regression model is to understand the relationship
between total customer spending and selected predictors.
The dependent variable is:
``` text
total_purchases
```
Based on the exploratory and correlation analysis, the preliminary model
uses:
``` text
Income
Cluster membership
```
as predictors.
The conceptual model is:
``` text
Total Purchases = β₀ + β₁(Income) + β₂(Cluster) + ε
```
The model is implemented using:
``` python
LinearRegression()
```
Cluster membership is represented using dummy variables.
The notebook uses `cluster_1` as an included cluster indicator, with the
omitted cluster acting as the reference category for interpretation.
---
14. Train-Test Split
The dataset is divided into:
``` text
80% → Training data
20% → Test data
```
using:
``` python
train_test_split(
    X,
    y,
    test_size=0.2,
    random_state=206
)
```
The training data is used to estimate the regression model, while the
test data is kept separate for evaluating performance on unseen
observations.
---
15. Model Evaluation
The regression model is evaluated using:
Mean Squared Error -- MSE
Measures the average squared prediction error.
Root Mean Squared Error -- RMSE
The square root of MSE. Larger errors receive greater weight.
Mean Absolute Error -- MAE
Measures the average absolute difference between actual and predicted
values.
R-squared -- R²
Measures the proportion of variation in total purchases explained by the
model.
The notebook calculates these metrics on both:
Training data
Test data
---
16. 10-Fold Cross-Validation
The notebook additionally performs 10-fold cross-validation.
``` python
KFold(
    n_splits=10,
    shuffle=True,
    random_state=206
)
```
R² is used as the cross-validation scoring metric.
The notebook reports:
Individual fold R² scores
Mean R²
Standard deviation of R²
Cross-validation provides a more robust view of model stability than
relying only on training performance.
---
17. Business Strategies
The analytical results are translated into customer-segment-based
supermarket strategies.
High-Income Customers
High-income customers tend to purchase more meat, wine, and fish.
Potential supermarket strategy:
Place these categories in different sections/shelves.
Encourage customers to move through different areas of the store.
Increase exposure to complementary products and potential additional
purchases.
Upper-Middle-Income Customers
This segment consumes meat, wine, and fish, but the increase in
consumption may be limited.
Potential supermarket strategy:
Use targeted discounts.
Provide promotional offers.
Use loyalty-based incentives to encourage higher purchase frequency.
Lower-Middle-Income Customers
This segment tends to spend less on relatively expensive food
categories.
Potential supermarket strategy:
Create ready-made value bundles.
Combine products such as fruits, vegetables, and other frequently
purchased grocery items.
Use bundling to provide convenience and perceived value.
These strategies are recommendations derived from the analytical
segmentation and are intended to be tested and validated through actual
customer response.
---
18. How to Run the Notebook
Step 1 -- Upload the dataset
Place:
``` text
nata_supermarket_data.xlsx
```
in the expected working directory.
For Google Colab, the notebook currently expects:
``` text
/content/nata_supermarket_data.xlsx
```
Step 2 -- Open the notebook
Open:
``` text
BA_Assgn_draft_4 (2).ipynb
```
in Google Colab or another Jupyter-compatible environment.
Step 3 -- Install required packages if necessary
``` bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl
```
Step 4 -- Run the notebook
Run the cells sequentially from top to bottom.
The notebook will:
Load the dataset.
Clean and preprocess the data.
Perform exploratory analysis.
Create customer segments.
Analyse correlations.
Build the regression model.
Evaluate model performance.
Generate business insights.
---
19. Project Structure
A recommended project structure is:
``` text
Nata-Supermarkets-Analytics/
│
├── BA_Assgn_draft_4.ipynb
├── nata_supermarket_data.xlsx
├── README.md
└── outputs/
    ├── figures/
    └── results/
```
The current notebook itself contains the complete analytical workflow
and visualisations.
---
20. Key Analytical Flow
``` text
Raw Customer Data
       ↓
Data Cleaning
       ↓
Outlier Treatment
       ↓
Categorical Consolidation
       ↓
Feature Transformation
       ↓
Standardisation + One-Hot Encoding
       ↓
Elbow Method
       ↓
K-Means (3 Clusters)
       ↓
Customer Segment Profiling
       ↓
Create Total Purchases
       ↓
Correlation & Multicollinearity Analysis
       ↓
Preliminary Linear Regression
       ↓
Train/Test Evaluation
       ↓
10-Fold Cross-Validation
       ↓
Business Strategies
```
---
21. Limitations and Interpretation Notes
The analysis uses correlation to identify associations; correlation
should not be interpreted as causation.
The regression model is explicitly a preliminary baseline model,
not a complete demand-forecasting system.
K-Means results depend on the selected variables, preprocessing
choices, scaling, and chosen value of K.
The business strategies should be validated through experiments,
customer response, and actual sales data before large-scale
implementation.
The income and birth-year cleaning rules are based on the
preprocessing approach implemented in the notebook.
---
22. Conclusion
This project combines descriptive analytics, customer segmentation,
correlation analysis, and regression modelling to develop a data-driven
understanding of Nata Supermarkets' customers.
The overall workflow moves from data quality and customer profiling
to customer segmentation and spending analysis, and finally
translates the analytical results into segment-specific supermarket
strategies.
The approach demonstrates how supermarket customer data can be converted
into actionable insights for:
Customer segmentation
Merchandising
Promotional planning
Channel strategy
Basket-building
Customer targeting
Retail decision-making
