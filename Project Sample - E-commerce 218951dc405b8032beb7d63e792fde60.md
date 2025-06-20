---
layout: default
title: E-Commerce Transaction Analysis
permalink: /Project Sample - E-commerce 218951dc405b8032beb7d63e792fde60/
---

# E-commerce Transaction Analysis

Created: June 20, 2025 1:43 PM

**Project description:** This project explores customer transaction behavior in an e-commerce setting. We begin by rigorously cleaning and standardizing raw purchase data, then uncover key patterns through distribution, trend, and correlation analyses. First, we get a full overview of the data's completeness and structure, convert purchase timestamps to a unified datetime format, harmonize categorical fields (e.g., product categories and payment methods), and remove duplicate records to ensure accuracy. With our cleaned dataset, we then quantify how sales are distributed across categories and payment options, track monthly transaction volumes to reveal seasonality or growth trends, and investigate relationships between variables to inform strategic pricing, marketing, and product decisions.

## 1. Clean Transaction data before further analysis

To ensure our transaction dataset yields reliable insights, we’ll perform four key data‑cleaning steps:

### 1.1. Dataset Overview

First, we inspect the dataset’s structure, missing values, basic statistics, and column names:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

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

### 1.2. Datetime Conversion

Convert the `Purchase_Date` column from object to datetime—coercing errors to `NaT` and assuming day‑first format—to enable robust time‑based analysis:

```python
# Convert Purchase_Date to datetime
# Robust conversion, coercing errors to NaT, assuming day comes first
# Handles inconsistent date formats
# If your date is month-first, remove dayfirst=True
# If you want to see which rows failed, you can check df[df['Purchase_Date'].isna()]
df['Purchase_Date'] = pd.to_datetime(df['Purchase_Date'], errors='coerce', dayfirst=True)
```

### 1.3. Handling Inconsistent Data

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

### 1.4. Handling Duplicates

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

### 2.1. Distribution Analysis on Category and Payment Method

First, we examine how transactions are distributed across `Category` and `Payment_Method`:

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
<img src="/images/cat_distribution.png" alt="Category Distribution"/>
<img src="/images/pay_distribution.png" alt="Payment Distribution"/>


### 2.2.Monthly Transaction Trend

Next, we examine how transactions change in terms of sales (based on `Final_Price(Rs.)`) and products sold in the past months

```python
# Monthly sales trend analysis
print('\nMonthly Sales Trend:')
# Ensure Purchase_Date is datetime type
if not pd.api.types.is_datetime64_any_dtype(df['Purchase_Date']):
    df['Purchase_Date'] = pd.to_datetime(df['Purchase_Date'], errors='coerce', dayfirst=True)
# Extract year and month
monthly_sales = df.dropna(subset=['Purchase_Date']).copy()
monthly_sales['YearMonth'] = monthly_sales['Purchase_Date'].dt.to_period('M')
trend = monthly_sales.groupby('YearMonth')['Final_Price(Rs.)'].sum().sort_index()

# Line plot for monthly sales trend
trend.index = trend.index.astype(str)
plt.figure(figsize=(10,6))
plt.plot(trend.index, trend.values, marker='o')
plt.title('Monthly Sales Trend')
plt.xlabel('Month')
plt.ylabel('Total Sales (Rs.)')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# Calculate monthly product count
monthly_count = monthly_sales.groupby('YearMonth')['Product_ID'].count().sort_index()
print('\nMonthly Product Count:')
print(monthly_count)

# Dual-axis plot: total sales and product count per month
fig, ax1 = plt.subplots(figsize=(10,6))
color = 'tab:blue'
ax1.set_xlabel('Month')
ax1.set_ylabel('Total Sales (Rs.)', color=color)
ax1.plot(trend.index, trend.values, marker='o', color=color, label='Total Sales (Rs.)')
ax1.tick_params(axis='y', labelcolor=color)
plt.xticks(rotation=45)

ax2 = ax1.twinx()
color = 'tab:orange'
ax2.set_ylabel('Product Count', color=color)
ax2.plot(trend.index, monthly_count.values, marker='s', color=color, label='Product Count')
ax2.tick_params(axis='y', labelcolor=color)

plt.title('Monthly Sales & Product Count Trend (Dual Axis)')
fig.tight_layout()
plt.show() 
```
<img src="/images/dual_axis.png" alt="Sales and Products Sold"/>

### 2.3. Correlation Matrix Analysis
Finally, we are conducting a correlation matrix across `price`, `discount`, and `final_price`

```python
# Correlation matrix and heatmap for price, discount, and final price
print('\nCorrelation Matrix (Price, Discount, Final Price):')
numeric_cols = ['Price (Rs.)', 'Discount (%)', 'Final_Price(Rs.)']
corr_matrix = df[numeric_cols].corr()
print(corr_matrix)

plt.figure(figsize=(6, 5))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', fmt='.2f', square=True)
plt.title('Correlation Heatmap: Price, Discount, Final Price')
plt.tight_layout()
plt.show()
```
<img src="/images/matrix.png" alt="Correlation Matrix"/>

### 3. Export a Data Visualisation
