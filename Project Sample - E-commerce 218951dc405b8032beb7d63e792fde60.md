---
layout: default
title: E-Commerce Transaction Analysis
permalink: /ecommerce-analysis/
---

# E-commerce Transaction Analysis

Created: June 20, 2025 1:43 PM

**Project description:** This project explores customer transaction behavior in an e‑commerce setting by first rigorously cleaning and standardizing raw purchase data, then uncovering key patterns through distribution, trend, and correlation analyses. Beginning with a full overview of data completeness and structure, we convert purchase timestamps to a unified datetime format, harmonize categorical fields (e.g., product categories and payment methods), and remove duplicate records to ensure accuracy. With our cleaned dataset, we then quantify how sales are distributed across categories and payment options, track monthly transaction volumes to reveal seasonality or growth trends, and investigate relationships between variables to inform strategic pricing, marketing, and product decisions.

## 1. Clean Transaction data before further analysis

To ensure our transaction dataset yields reliable insights, we’ll perform four key data‑cleaning steps:

### 1. Dataset Overview

First, we inspect the dataset’s structure, missing values, basic statistics, and column names:

```python
import pandas as pd

# Read the dataset
file_path = 'ecommerce_dataset_updated.csv'
df = pd.read_csv(file_path)
original_row_count = df.shape[0]

# Dataset Overview
print("Dataset Info:")
print(df.info())

print("\nMissing Values:")
print(df.isnull().sum())

print("\nBasic Statistics:")
print(df.describe())

# Display Column Names
print("\nColumn Names:")
print(df.columns.tolist())
```

```
Dataset Info:
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3660 entries, 0 to 3659
Data columns (total 8 columns):
 #   Column            Non-Null Count  Dtype
---  ------            --------------  -----
 0   User_ID           3660 non-null   object
 1   Product_ID        3660 non-null   object
 2   Category          3660 non-null   object
 3   Price (Rs.)       3660 non-null   float64
 4   Discount (%)      3660 non-null   int64
 5   Final_Price(Rs.)  3660 non-null   float64
 6   Payment_Method    3660 non-null   object
 7   Purchase_Date     3660 non-null   object
dtypes: float64(2), int64(1), object(5)
memory usage: 228.9+ KB
None

Missing Values:
User_ID             0
Product_ID          0
Category            0
Price (Rs.)         0
Discount (%)        0
Final_Price(Rs.)    0
Payment_Method      0
Purchase_Date       0
dtype: int64

Basic Statistics:
       Price (Rs.)  Discount (%)  Final_Price(Rs.)
count  3660.000000   3660.000000       3660.000000
mean    254.800675     18.825137        206.906579
std     141.682621     14.731338        122.687844
min      10.090000      0.000000          5.890000
25%     134.012500      5.000000        104.512500
50%     253.845000     15.000000        199.185000
75%     377.595000     25.000000        304.117500
max     499.960000     50.000000        496.820000

Column Names:
['User_ID', 'Product_ID', 'Category', 'Price (Rs.)', 'Discount (%)', 'Final_Price(Rs.)', 'Payment_Method', 'Purchase_Date']
```

### 2. Datetime Conversion

Convert the `Purchase_Date` column from object to datetime—coercing errors to `NaT` and assuming day‑first format—to enable robust time‑based analysis:

```python
# Convert Purchase_Date to datetime
# Robust conversion, coercing errors to NaT, assuming day comes first
# Handles inconsistent date formats
# If your date is month-first, remove dayfirst=True
# If you want to see which rows failed, you can check df[df['Purchase_Date'].isna()]
df['Purchase_Date'] = pd.to_datetime(df['Purchase_Date'], errors='coerce', dayfirst=True)
```

### 3. Handling Inconsistent Data

Standardize `Category` and `Payment_Method` by lowercasing, trimming whitespace, and removing internal spaces. This unifies entries such as “Sports” vs. “sports” and “Credit Card” vs. “CreditCard”:

```python

# Check unique values in Category and Payment_Method
print('Unique values in Category before standardization:', df['Category'].unique())
print('Unique values in Payment_Method before standardization:', df['Payment_Method'].unique())

# Standardize Category and Payment_Method columns
# Convert to lowercase and strip spaces
# For Payment_Method, also remove internal spaces for consistency
# You can add more replacements if needed

df['Category'] = df['Category'].str.lower().str.strip()
df['Payment_Method'] = df['Payment_Method'].str.lower().str.replace(' ', '').str.strip()

# Check unique values after standardization
print('Unique values in Category after standardization:', df['Category'].unique())
print('Unique values in Payment_Method after standardization:', df['Payment_Method'].unique())
```

```
Unique values in Category before standardization: ['Sports' 'Clothing' 'Toys' 'Beauty' 'Books' 'Home & Kitchen'
 'Electronics']
Unique values in Payment_Method before standardization: ['Net Banking' 'Credit Card' 'UPI' 'Cash on Delivery' 'Debit Card']
Unique values in Category after standardization: ['sports' 'clothing' 'toys' 'beauty' 'books' 'home & kitchen'
 'electronics']
Unique values in Payment_Method after standardization: ['netbanking' 'creditcard' 'upi' 'cashondelivery' 'debitcard']
```

### 4. Handling Duplicates

Identify and remove exact duplicate rows—keeping only the first occurrence—to ensure each transaction is represented once:

```python
# Detect and remove duplicate rows
# Keep the first occurrence
num_duplicates = df.duplicated().sum()
print(f'Number of duplicate rows found: {num_duplicates}')
df = df.drop_duplicates(keep='first')

# Final statistics
final_row_count = df.shape[0]
print(f'Original number of rows: {original_row_count}')
print(f'Number of rows after cleaning: {final_row_count}')
print(f'Number of rows removed during cleaning: {original_row_count - final_row_count}') 
```

```
Number of duplicate rows found: 0
Original number of rows: 3660
Number of rows after cleaning: 3660
Number of rows removed during cleaning: 0
```

---

## 2. Transaction Analysis

With the data cleaned, we can now perform a straightforward transaction analysis covering three key areas

- Distribution Analysis
- Monthly Transaction Trend
- Correlation Analysis

### 1. Distribution Analysis on Category and Payment Method

First, we examine how transactions are distributed across product categories and payment methods:

```python
# Distribution analysis for Category
print('\nCategory Distribution:')
category_counts = df['Category'].value_counts()
category_percent = df['Category'].value_counts(normalize=True) * 100
print(pd.DataFrame({'Count': category_counts, 'Percentage': category_percent.round(2)}))

# Distribution analysis for Payment_Method
print('\nPayment Method Distribution:')
payment_counts = df['Payment_Method'].value_counts()
payment_percent = df['Payment_Method'].value_counts(normalize=True) * 100
print(pd.DataFrame({'Count': payment_counts, 'Percentage': payment_percent.round(2)}))

# Plot pie charts for Category and Payment_Method
def plot_pie(data, title):
    plt.figure(figsize=(7, 7))
    data.value_counts().plot.pie(autopct='%1.1f%%', startangle=90, counterclock=False)
    plt.title(title)
    plt.ylabel('')
    plt.show()

# Pie chart for Category
df['Category'].value_counts().plot.pie(
    autopct='%1.1f%%', startangle=90, counterclock=False, figsize=(7,7), title='Category Distribution (Pie Chart)')
plt.ylabel('')
plt.show()

# Pie chart for Payment_Method
df['Payment_Method'].value_counts().plot.pie(
    autopct='%1.1f%%', startangle=90, counterclock=False, figsize=(7,7), title='Payment Method Distribution (Pie Chart)')
plt.ylabel('')
plt.show() 
```
<img src="images/cat_distribution.png?raw=true"/>
<img src="images/pay_distribution.png?raw=true"/>

### 3. Support the selection of appropriate statistical tools and techniques

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo.
