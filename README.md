# Customer Shopping Behaviour Analysis

## 📌 Project Overview

This project analyzes **customer shopping behaviour** to identify patterns in purchasing, customer demographics, product categories, subscription status, purchase frequency, discounts, shipping preferences, and customer ratings.

The project demonstrates an end-to-end **Data Analytics workflow** using:

* 🐍 Python — Data cleaning and preprocessing
* 🗄️ MySQL — Data storage
* 📊 Power BI — Interactive dashboard and visualization
* 📓 Jupyter Notebook — Exploratory data preparation and analysis

The objective is to transform raw customer shopping data into meaningful insights that can support better understanding of customer behaviour and purchasing patterns.

---

## 🎯 Project Objectives

The main objectives of this project are to:

* Understand customer purchasing behaviour.
* Analyze customer demographics and age groups.
* Examine purchasing patterns across product categories.
* Analyze customer subscription status.
* Compare purchase amounts across different customer groups.
* Analyze purchase frequency.
* Examine customer review ratings.
* Understand the relationship between discounts and promotional-code usage.
* Analyze shipping preferences.
* Build an interactive Power BI dashboard for business-oriented analysis.

---

## 🛠️ Technologies Used

| Technology           | Purpose                                              |
| -------------------- | ---------------------------------------------------- |
| **Python**           | Data cleaning, preprocessing and feature engineering |
| **Pandas**           | Data manipulation and transformation                 |
| **Jupyter Notebook** | Data analysis workflow                               |
| **MySQL**            | Database storage                                     |
| **SQLAlchemy**       | Python–MySQL connection                              |
| **MySQL Connector**  | Database connectivity                                |
| **Power BI**         | Interactive dashboard and visualization              |

---

## 📂 Project Structure

```text
Customer-Shopping-Behaviour-Analysis/
│
├── customer_shopping_behaviour_analysis.ipynb
├── customer_behavior.pbix
├── customer_shopping_behavior.csv
└── README.md
```

> The notebook loads the raw dataset from `customer_shopping_behavior.csv`.

---

# 🔄 Project Workflow

```text
Raw Customer Data
       ↓
Python / Pandas
       ↓
Data Cleaning
       ↓
Feature Engineering
       ↓
MySQL Database
       ↓
Power BI
       ↓
Interactive Customer Behaviour Dashboard
       ↓
Business Insights
```

---

# 🐍 1. Data Preparation Using Python

The analysis begins by importing the customer shopping dataset into Pandas.

```python
import pandas as pd

df = pd.read_csv('customer_shopping_behavior.csv')
```

Initial inspection was performed using:

```python
df.head()
df.info()
df.describe(include='all')
df.isnull().sum()
```

These steps were used to understand the dataset structure, data types, descriptive statistics, and missing values.

---

## 🧹 Data Cleaning

### Missing Review Ratings

Missing values in the `Review Rating` column were handled using the **median review rating within each product category**.

```python
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(
    lambda x: x.fillna(x.median())
)
```

This approach preserves category-specific rating patterns instead of replacing all missing values with one overall median.

---

## 🏷️ Column Standardization

Column names were standardized by converting them to lowercase and replacing spaces with underscores.

```python
df.columns = df.columns.str.lower()
df.columns = df.columns.str.replace(' ', '_')
```

The purchase amount column was also renamed:

```python
df = df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'})
```

This produces cleaner and more convenient column names for analysis and database use.

---

# 🧮 2. Feature Engineering

Two additional analytical features were created in Python.

## Age Group

Customers were divided into four age groups using quartile-based segmentation:

* Young Adult
* Adult
* Middle-aged
* Senior

```python
labels = ['Young Adult', 'Adult', 'Middle-aged', 'Senior']

df['age_group'] = pd.qcut(
    df['age'],
    q=4,
    labels=labels
)
```

This makes demographic analysis easier in Power BI.

---

## Purchase Frequency in Days

The original purchase-frequency categories were converted into approximate numbers of days.

```python
frequency_mapping = {
    'Fortnightly': 14,
    'Weekly': 7,
    'Monthly': 30,
    'Quarterly': 90,
    'Bi-Weekly': 14,
    'Annualiy': 365,
    'Every 3 Months': 90
}

df['purchase_frequency_days'] = (
    df['frequency_of_purchases'].map(frequency_mapping)
)
```

This allows purchase frequency to be treated as a numerical analytical variable.

---

# 🎟️ Discount and Promotional Code Analysis

The notebook checks whether `discount_applied` and `promo_code_used` contain identical information:

```python
(df['discount_applied'] == df['promo_code_used']).all()
```

The result was used to identify redundancy between the two columns.

Because the two fields contained the same information, `promo_code_used` was removed:

```python
df = df.drop('promo_code_used', axis=1)
```

This reduces unnecessary duplication in the analytical dataset.

---

# 🗄️ 3. MySQL Database Integration

After preprocessing, the cleaned dataset is loaded into MySQL.

The notebook uses:

* `mysql-connector-python`
* `SQLAlchemy`
* Pandas `to_sql()`

A connection is created using SQLAlchemy:

```python
engine = create_engine(
    f"mysql+mysqlconnector://{username}:{password}@{host}:{port}/{database}"
)
```

The processed DataFrame is then loaded into the MySQL database:

```python
df.to_sql(
    table_name,
    engine,
    if_exists='replace',
    index=False
)
```

The notebook also checks the available database tables:

```python
print(pd.read_sql("SHOW TABLES", engine))
```

---

# 📊 4. Power BI Dashboard

The Power BI file provides an interactive customer behaviour dashboard based on the processed customer dataset.

The dashboard contains KPI cards, charts and slicers for exploring customer behaviour from different perspectives.

## KPI Metrics

The dashboard includes three key summary metrics:

* **Number of Customers**
* **Average Purchase Amount**
* **Average Review Rating**

These provide a high-level overview of customer activity and purchasing performance.

---

## 📈 Dashboard Visualizations

### Subscription Status

A **donut chart** displays the distribution of customers according to subscription status.

This helps identify the proportion of customers who have a subscription versus those who do not.

### Customers by Category

A **column chart** compares customer counts across product categories.

This can be used to identify categories with relatively larger customer participation.

### Purchase Amount by Age Group

A **clustered bar chart** compares purchase amounts across the four engineered age groups:

* Young Adult
* Adult
* Middle-aged
* Senior

### Purchase Amount by Category

A **column chart** compares purchase amounts across product categories, helping identify categories associated with higher or lower purchasing levels.

### Customers by Age Group

A **clustered bar chart** shows the distribution of customers across the engineered age groups.

---

# 🎛️ Interactive Filters

The Power BI dashboard includes interactive filters/slicers for:

* **Subscription Status**
* **Gender**
* **Category**
* **Shipping Type**

These filters allow users to examine specific customer segments and dynamically explore the dashboard.

---

# 💡 Key Analytical Questions

The project can be used to answer questions such as:

1. How many customers are represented in the dataset?
2. What is the average customer purchase amount?
3. What is the average review rating?
4. How are customers distributed across product categories?
5. How does purchasing vary across age groups?
6. What proportion of customers have subscriptions?
7. Which product categories have higher purchase amounts?
8. How does customer composition vary by gender?
9. How are different shipping types represented?
10. How does customer behaviour change when different dashboard filters are applied?

---

# 📊 Business Value

The analysis can help a retail business understand its customer base and purchasing behaviour.

Potential applications include:

* **Customer segmentation** based on age and demographics.
* **Category performance analysis** using customer counts and purchase amounts.
* **Subscription analysis** to understand the customer base.
* **Customer experience analysis** using review ratings.
* **Purchasing-frequency analysis** to understand how often customers shop.
* **Marketing analysis** involving discounts and promotional activity.
* **Operational analysis** of customer shipping preferences.

---

# 🔍 Skills Demonstrated

This project demonstrates practical skills in:

### Python

* Pandas
* Data inspection
* Missing-value treatment
* Data cleaning
* Feature engineering
* Categorical transformation

### SQL / MySQL

* Database connection
* Loading analytical datasets
* Working with database tables
* Python-to-MySQL integration

### Power BI

* Dashboard development
* KPI cards
* Donut charts
* Column charts
* Bar charts
* Interactive slicers
* Customer segmentation
* Business-focused data visualization

### Data Analytics

* Exploratory data analysis
* Customer segmentation
* Behavioural analysis
* Data transformation
* Business insight generation

---

# 🚀 End-to-End Data Analytics Pipeline

This project demonstrates how raw customer data can be transformed into a business intelligence solution:

**1. Collect**
Raw customer shopping data is imported into Python.

**2. Inspect**
The dataset is examined using Pandas to understand its structure and quality.

**3. Clean**
Missing review ratings and redundant fields are addressed.

**4. Transform**
Age groups and purchase-frequency measures are created.

**5. Store**
The processed dataset is loaded into MySQL.

**6. Visualize**
Power BI connects the data to create an interactive dashboard.

**7. Analyze**
Customer purchasing patterns, demographics, subscription status, categories and other behavioural dimensions can be explored.

---

# 📌 Conclusion

The **Customer Shopping Behaviour Analysis** project provides an end-to-end demonstration of a modern data analytics workflow.

By combining **Python, MySQL and Power BI**, the project moves from raw customer data through data preparation and database storage to interactive business intelligence.

The resulting dashboard provides a practical way to explore **customer demographics, purchasing behaviour, category performance, subscription status, review ratings and shipping preferences**, making the project suitable for demonstrating practical Data Analyst skills.

---


### Tools & Technologies

`Python` `Pandas` `Jupyter Notebook` `MySQL` `SQLAlchemy` `Power BI` `Data Analytics` `Customer Behaviour Analysis`

---

## ⭐ Project Highlights

> **Python → Data Cleaning → Feature Engineering → MySQL → Power BI → Customer Behaviour Insights**
