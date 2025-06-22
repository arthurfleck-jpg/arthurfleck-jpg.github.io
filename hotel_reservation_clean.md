# Project Example - Hotel Reservation

Created: June 22, 2025 1:08 PM
Reviewed: No

## Hotel Reservation Data Cleaning Analysis

**Project description:** This project explores the process of preparing and cleaning a hotel reservation dataset to ensure data integrity and enable accurate downstream analysis. The goal is to identify and resolve data quality issues such as missing values, inconsistent formats, and potential outliers that may affect modeling or business decision-making. 

To ensure our transaction dataset yields reliable insights, we’ll perform four key data‑cleaning steps:

### 1. Dataset Overview

Before performing any data cleaning, we start with an initial inspection of the dataset's structure. This step helps us understand the overall quality and shape of the data. Our key tasks include:

- **df.info()**: Identify the data type of each column (e.g., `object`, `int64`, `float64`) and examine the number of non-null entries. This allows us to quickly spot missing values and assess whether certain fields need type conversions—such as transforming string or numeric date columns into proper datetime formats for future analysis.
- **df.isnull().sum()**: Quantify the number of missing values in each column. This helps us determine whether to impute, drop, or apply custom handling strategies for incomplete data.
- **df.describe()**: Generate summary statistics for all numeric columns (count, mean, standard deviation, min, median, and max). This helps in detecting potential outliers or unusual distributions that may warrant further inspection or correction.

```python
import pandas as pd

# Read the dataset
file_path = 'Hotel Reservations.csv'
df = pd.read_csv(file_path)

# 1.Dataset Overview

# Display general information about the dataset.
# This includes the number of entries, number of columns, data types of each column, and memory usage.
print("--- Dataset Info ---")
df.info()
print("\\n" + "="*50 + "\\n")

# Check for missing values in each column.
# This will count the number of NaN/null values for every column.
print("--- Missing Values ---")
print(df.isnull().sum())
print("\\n" + "="*50 + "\\n")

# Get basic descriptive statistics for numerical columns.
# This includes count, mean, standard deviation, min, max, and quartile values.
print("--- Basic Statistics ---")
print(df.describe())
print("\\n" + "="*50 + "\\n")

# Display all column names.
# This helps in getting a quick reference of all available features.
print("--- Column Names ---")
print(df.columns.tolist())
print("\\n" + "="*50 + "\\n") 
```

```
--- Dataset Info ---
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 36275 entries, 0 to 36274
Data columns (total 19 columns):
	 #   Column                               Non-Null Count  Dtype  
---  ------                                --------------  -----  
 0   Booking_ID                            36275 non-null  object 
 1   no_of_adults                          36275 non-null  int64  
 2   no_of_children                        36275 non-null  int64  
 3   no_of_weekend_nights                  36275 non-null  int64  
 4   no_of_week_nights                     36275 non-null  int64  
 5   type_of_meal_plan                     36275 non-null  object 
 6   required_car_parking_space            36275 non-null  int64  
 7   room_type_reserved                    36275 non-null  object 
 8   lead_time                             36275 non-null  int64  
 9   arrival_year                          36275 non-null  int64  
 10  arrival_month                         36275 non-null  int64  
 11  arrival_date                          36275 non-null  int64  
 12  market_segment_type                   36275 non-null  object 
 13  repeated_guest                        36275 non-null  int64  
 14  no_of_previous_cancellations          36275 non-null  int64  
 15  no_of_previous_bookings_not_canceled  36275 non-null  int64  
 16  avg_price_per_room                    36275 non-null  float64
 17  no_of_special_requests                36275 non-null  int64  
 18  booking_status                        36275 non-null  object 
dtypes: float64(1), int64(13), object(5)
memory usage: 5.3+ MB
\n==================================================\n
--- Missing Values ---
Booking_ID                              0
no_of_adults                            0
no_of_children                          0
no_of_weekend_nights                    0
no_of_week_nights                       0
type_of_meal_plan                       0
required_car_parking_space              0
room_type_reserved                      0
lead_time                               0
arrival_year                            0
arrival_month                           0
arrival_date                            0
market_segment_type                     0
repeated_guest                          0
no_of_previous_cancellations            0
no_of_previous_bookings_not_canceled    0
avg_price_per_room                      0
no_of_special_requests                  0
booking_status                          0
dtype: int64
\n==================================================\n
--- Basic Statistics ---
       no_of_adults  no_of_children  ...  avg_price_per_room  no_of_special_requests
count  36275.000000    36275.000000  ...        36275.000000            36275.000000
mean       1.844962        0.105279  ...          103.423539                0.619655
std        0.518715        0.402648  ...           35.089424                0.786236
min        0.000000        0.000000  ...            0.000000                0.000000
25%        2.000000        0.000000  ...           80.300000                0.000000
50%        2.000000        0.000000  ...           99.450000                0.000000
75%        2.000000        0.000000  ...          120.000000                1.000000
max        4.000000       10.000000  ...          540.000000                5.000000

[8 rows x 14 columns]
\n==================================================\n
--- Column Names ---
['Booking_ID', 'no_of_adults', 'no_of_children', 'no_of_weekend_nights', 'no_of_week_nights', 'type_of_meal_plan', 'required_car_parking_space', 'room_type_reserved', 'lead_time', 'arrival_year', 'arrival_month', 'arrival_date', 'market_segment_type', 'repeated_guest', 'no_of_previous_cancellations', 'no_of_previous_bookings_not_canceled', 'avg_price_per_room', 'no_of_special_request
```

Based on the output, we can identify the following information:

**1. Dataset Info:** The dataset contains **36,275 rows** and **19 columns**.

**2. Missing Values:**

- All columns show 36,275 non-null entries, indicating that there are **no missing values** in the dataset.
- This suggests a **high-quality dataset**, and we do **not need to take additional action** for missing value imputation or removal.

**3. Basic Statistics:**

- `no_of_adults`: The **minimum value is 0**, which could be a data quality concern as it is not likely for children to make a booking without adults. These rows might require further inspection.
- `no_of_children`: The **maximum value is 10**, which appears reasonable and within expectations for group or family bookings.
- `avg_price_per_room`: The **minimum value is 0**, which could represent free bookings—possibly due to promotions, staff stays, or loyalty redemptions. This warrants further exploration to confirm. The **maximum value is 540**, which could be valid for high-end room types.

**4. Column Names:**

- All column names are present and formatted consistently. No obvious issues were detected.

From the basic descriptive statistics, we observe some **potential outliers**, especially in the `no_of_adults` and `avg_price_per_room` fields, where **the minimum value is 0**. These anomalies require closer inspection in the next steps of the cleaning process.

### 2. Handling Duplicate Values

In this step, we conduct two important checks:

1. Verify if the dataset contains any **completely duplicated rows**.
2. Examine whether the `Booking_ID` column includes **duplicate values**, as booking identifiers are typically unique for each reservation.

```python
# Task 2: Handling Duplicates

# --- Check for duplicate rows ---

# Check for the number of completely duplicate rows in the dataset.
duplicate_rows = df.duplicated().sum()
print(f"--- Duplicate Rows Check ---")
print(f"Number of duplicate rows: {duplicate_rows}")
print("\\n" + "="*50 + "\\n")

# --- Check for duplicate Booking_IDs ---

# Check for duplicate values in the 'Booking_ID' column.
booking_id_duplicates = df['Booking_ID'].duplicated().sum()
print(f"--- Duplicate Booking_ID Check ---")
print(f"Number of duplicate Booking_IDs: {booking_id_duplicates}")
print("\\n" + "="*50 + "\\n") 
```

```
--- Duplicate Rows Check ---
Number of duplicate rows: 0
\n==================================================\n
--- Duplicate Booking_ID Check ---
Number of duplicate Booking_IDs: 0
\n==================================================\n
```

Based on the results, we can confirm that our dataset maintains **excellent integrity**. There are **no fully duplicated rows**, and each `Booking_ID` is **unique**, reinforcing the reliability of our data before further transformation or modeling.

### 3. Replace or Correct Missing Values

Since there are no missing values in this dataset, no replacements or corrections are needed. 

### 4. Detect Outlier Values

As discussed earlier, the columns `no_of_adults` and `avg_price_per_room` contain zero values, which are likely potential outliers. Before deciding how to treat them, it's important to evaluate whether these values make sense in the context of hotel bookings.

Let’s first focus on cases where `no_of_adults = 0`:

- In most cases, minors are not allowed to book hotel rooms without adults. So bookings with zero adults are likely invalid.
- We should pay close attention to two related fields: `no_of_children` and `booking_status`:
    - If `no_of_adults` is 0 but `no_of_children` is greater than 0, this is a very unusual and likely invalid booking.
    - If most of these bookings also have a `booking_status` of "Canceled", that could further indicate they were not legitimate.

We will filter records where `no_of_adults = 0`, then examine `no_of_children` and `booking_status` for patterns. These records should be treated as outliers, unless a reasonable explanation is found.

```python
# Task 3: Outlier Detection

# --- Step 3.1: Investigate rows with no_of_adults == 0 ---

print("--- Investigating 'no_of_adults == 0' ---")

# Filter the DataFrame for rows where the number of adults is zero.
zero_adults_df = df[df['no_of_adults'] == 0]

# Check the number of such bookings.
print(f"Number of bookings with no adults: {len(zero_adults_df)}")

# Display these rows to understand their characteristics.
if not zero_adults_df.empty:
    print("Details of bookings with no adults:")
    print(zero_adults_df[['no_of_adults', 'no_of_children', 'booking_status', 'market_segment_type']])
print("\\n" + "="*50 + "\\n")
```

```
Number of bookings with no adults: 139
Details of bookings with no adults:
       no_of_adults  no_of_children booking_status market_segment_type
32                0               2   Not_Canceled              Online
287               0               2       Canceled              Online
653               0               2   Not_Canceled              Online
937               0               2   Not_Canceled              Online
954               0               2   Not_Canceled              Online
...             ...             ...            ...                 ...
34720             0               2   Not_Canceled              Online
34730             0               2       Canceled              Online
34890             0               2   Not_Canceled              Online
35691             0               2       Canceled              Online
35797             0               2   Not_Canceled              Online

[139 rows x 4 columns]
```

After examining the dataset, we found some important observations:

- There are **139 records** where `no_of_adults` equals 0.
- In these records, the value of `no_of_children` is consistently 2 while these bookings are not all canceled — they include both `Canceled` and `Not_Canceled` statuses.

***Analysis:*** A booking with 0 adults and 2 children is unrealistic and likely represents incorrect or misentered data. One reasonable assumption is that the values were mistakenly recorded and were meant to be 2 adults and 0 children.

Since these values contradict typical hotel booking practices and could distort downstream analysis — such as per-person price calculations — these records should be considered invalid and removed from the dataset.

```python
# Handle Outliers by removing rows with no_of_adults == 0 ---
print("--- Handling Outliers: Removing 'no_of_adults == 0' ---")

# Get the number of rows before removal
rows_before_removal = len(df)
print(f"Original number of rows: {rows_before_removal}")

# Remove the rows where no_of_adults is 0.
# This action is based on the investigation in the previous step.
df = df[df['no_of_adults'] != 0]

# Get the number of rows after removal and confirm
rows_after_removal = len(df)
print(f"Removed {rows_before_removal - rows_after_removal} rows based on the investigation.")
print(f"New number of rows: {rows_after_removal}")

print("\\n" + "="*50 + "\\n")
```

Next, we examine cases where ****`avg_price_per_room = 0`**:**

Free rooms are sometimes a legitimate part of hotel operations, such as:

- Complimentary rooms offered to returning customers
- Rooms booked through business agreements (e.g., airline crew partnerships)

To verify this, we analyzed `market_segment_type` and `repeated_guest` columns to understand the context of these zero-priced bookings.

```python
# --- Step 3.2: Investigate rows with avg_price_per_room == 0 ---
print("--- Investigating 'avg_price_per_room == 0' ---")

# Filter the DataFrame for rows where the average price per room is zero.
# Note: This check is performed on the dataframe that has already been cleaned of 'no_of_adults == 0' rows.
zero_price_df = df[df['avg_price_per_room'] == 0]

# Check the number of such bookings.
print(f"Number of bookings with zero price: {len(zero_price_df)}")

# Display these rows to understand their context.
# We are interested in market segment, booking status, etc.
if not zero_price_df.empty:
    print("Details of bookings with zero price:")
    # Displaying a sample of 5 rows if the dataframe is large
    print(zero_price_df[['avg_price_per_room', 'market_segment_type', 'repeated_guest', 'booking_status']].head())

print("\\n" + "="*50 + "\\n
```

```
Number of bookings with zero price: 541
Details of bookings with zero price:
       avg_price_per_room market_segment_type  repeated_guest booking_status
63                    0.0       Complementary               0   Not_Canceled
145                   0.0       Complementary               1   Not_Canceled
209                   0.0       Complementary               0   Not_Canceled
266                   0.0       Complementary               1   Not_Canceled
267                   0.0       Complementary               0   Not_Canceled
...                   ...                 ...             ...            ...
35983                 0.0       Complementary               1   Not_Canceled
36080                 0.0       Complementary               1   Not_Canceled
36114                 0.0              Online               0   Not_Canceled
36217                 0.0              Online               0   Not_Canceled
36250                 0.0              Online               0   Not_Canceled

[541 rows x 4 columns]
```

After removing invalid zero-adult entries, we found **541 records** where `avg_price_per_room` is 0. Among them:

- The vast majority are from the `Complementary` market segment, which aligns with the logic of free room offerings.
- Nearly all of them have a booking status of `Not_Canceled`, indicating the stays were completed.
- The records include both returning (`repeated_guest == 1`) and new guests (`repeated_guest == 0`).
- A very small number of zero-priced bookings came from the `Online` segment.

***Analysis:*** Unlike the invalid adult count, zero-priced rooms have a clear **business explanation**. Most of them fall under the `Complementary` segment, supporting the case for valid, promotional, or partner-related stays.

For the few bookings with `avg_price_per_room == 0` under the `Online` channel, possible explanations include: *Online promotions (e.g., campaign giveaways)*; *Bundle offers (e.g., buy-one-get-one);* *Data entry errors*

Given the reasonable business context, especially for `Complementary` bookings, we should **not treat these as outliers**. Even the few from the `Online` channel should be **retained**, since their small number and potential justification do not warrant deletion.

After resolving known outliers, we proceed with a broader check to identify any overlooked anomalies across different data types:

- For `object` (categorical) columns, we print the value counts to detect rare values or inconsistent naming that may indicate input errors.
- For `int64` numeric columns, we use boxplots to visually detect potential outliers based on statistical distribution.

```python
# --- Step 3.4: Analyze Categorical Data Distribution ---
print("--- Analyzing Categorical Data ---")
# Get a list of categorical columns (dtype='object')
categorical_cols = df.select_dtypes(include=['object']).columns

for col in categorical_cols:
    # We skip Booking_ID as it is a unique identifier
    if col != 'Booking_ID':
        print(f"\\nValue counts for column: {col}")
        print(df[col].value_counts())

print("\\n" + "="*50 + "\\n")

# --- Step 3.5: Visualize Numerical Data with Box Plots ---
print("--- Visualizing Numerical Data for Outliers ---")

# Define numerical columns for box plot visualization
numerical_cols = ['lead_time', 'avg_price_per_room', 'no_of_previous_cancellations', 'no_of_children', 'no_of_weekend_nights', 'no_of_week_nights']

# Create a directory to save plots if it doesn't exist
if not os.path.exists('plots'):
    os.makedirs('plots')

# Generate and save box plots
for col in numerical_cols:
    plt.figure(figsize=(10, 6))
    sns.boxplot(x=df[col])
    plt.title(f'Box Plot for {col}')
    plot_path = os.path.join('plots', f'{col}_boxplot.png')
    plt.savefig(plot_path)
    plt.close() # Close the plot to free up memory
    print(f"Box plot for {col} saved to {plot_path}")

print("\\n" + "="*50 + "\\n")
```

```
--- Analyzing Categorical Data ---
\nValue counts for column: type_of_meal_plan
type_of_meal_plan
Meal Plan 1     27698
Not Selected     5130
Meal Plan 2      3303
Meal Plan 3         5

Name: count, dtype: int64
\nValue counts for column: room_type_reserved
room_type_reserved
Room_Type 1    28127
Room_Type 4     6057
Room_Type 6      964
Room_Type 2      559
Room_Type 5      265
Room_Type 7      157
Room_Type 3        7

Name: count, dtype: int64
\nValue counts for column: market_segment_type
market_segment_type
Online           23080
Offline          10527
Corporate         2017
Complementary      387
Aviation           125

Name: count, dtype: int64
\nValue counts for column: booking_status
booking_status
Not_Canceled    24295
Canceled        11841
Name: count, dtype: int64
```

From this check, we find the following insights:

- `type_of_meal_plan`: Values include "Meal Plan 1", "Not Selected", "Meal Plan 2", and "Meal Plan 3". The last one appears very infrequently (only 5 records), which may require special attention during modeling but does not indicate an error.
- `room_type_reserved` :"Room_Type 1" is the dominant category, while types like "Room_Type 3" and "Room_Type 7" are less common. This distribution appears reasonable and aligned with typical hotel operations.
- `market_segment_type`: Most bookings come from "Online" and "Offline" channels, which are expected.
- `booking_status`: Contains only two categories: "Not_Canceled" and "Canceled", with no typos or format issues.

There are no signs of spelling errors, inconsistent labeling, or invalid values in the categorical features. These fields appear clean and ready for use in downstream analysis or modeling.

Next, we can do a further analysis on numeric columns by the output boxplot:

![lead_time_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/lead_time_boxplot.png)

![avg_price_per_room_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/avg_price_per_room_boxplot.png)

![no_of_previous_cancellations_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/no_of_previous_cancellations_boxplot.png)

![no_of_children_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/no_of_children_boxplot.png)

![no_of_weekend_nights_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/no_of_weekend_nights_boxplot.png)

![no_of_week_nights_boxplot.png](Project%20Example%20-%20Hotel%20Reservation%2021a951dc405b80989ef8e2f9ee393b87/no_of_week_nights_boxplot.png)

From the boxplots of numeric columns like `lead_time` and `avg_price_per_room`, we identified some extreme values:

- For `lead_time`, some bookings are made far in advance — over 365 days. After further investigation, these long lead time bookings are mostly for September and often canceled. This pattern suggests they might be speculative holiday reservations. Since they are plausible and provide useful behavioral insights, we will **retain** these records for analysis.
- For `avg_price_per_room`, the maximum value is **540**, which stands out as a strong outlier. Considering the distribution of room prices and the isolated nature of this value, we treat this as a probable **data entry error** and will **remove** it.

```python
# --- Remove rows with the maximum value of avg_price_per_room (based on box plot observation) ---
# After visualizing the box plot, we determined that the maximum value of avg_price_per_room is an extreme outlier.
# We will remove all rows where avg_price_per_room equals this maximum value.
print("--- Removing rows with the maximum value of avg_price_per_room ---")
max_price = df['avg_price_per_room'].max()
max_price_rows = df[df['avg_price_per_room'] == max_price]
print(f"Maximum avg_price_per_room: {max_price}")
print(f"Number of rows to remove: {len(max_price_rows)}")
if not max_price_rows.empty:
    print("Sample rows to be removed:")
    print(max_price_rows[['avg_price_per_room']].head())

rows_before = len(df)
df = df[df['avg_price_per_room'] != max_price]
rows_after = len(df)
print(f"Removed {rows_before - rows_after} rows with the maximum avg_price_per_room.")
print(f"New number of rows: {rows_after}")
```

### 5. Summary

After applying all cleaning steps, we export the cleaned dataset and summarize key metrics before and after cleaning.

```python
# Export the cleaned DataFrame to a new CSV file
cleaned_file_path = 'cleaned_Hotel Reservation.csv'
df.to_csv(cleaned_file_path, index=False)
print(f"Cleaned data exported to {cleaned_file_path}")

# --- Cleaning Summary: Before vs After ---
# This summary table compares key metrics before and after data cleaning.
# Please update the numbers below based on your actual data if needed.

# Example metrics (replace with actual values if you track them during cleaning):
# - Number of missing values
# - Number of outliers removed (e.g., no_of_adults == 0)
# - Number of duplicate rows

print("Cleaning Summary: Before vs After\n")
print("| Metric                | Before   | After    |\n|-----------------------|----------|----------|")
print("| Missing Values        | 0        | 0        |")
print("| Outliers (no adults)  | 139      | 0        |")
print("| Duplicate Rows        | 0        | 0        |")
print("| Duplicate Booking_IDs | 0        | 0        |")
print(f"| Outliers (Overpriced)| {max_price_rows.shape[0]}      | 0        |")
print("| Total Rows            | 36275    | " + str(len(df)) + "    |\n") 
```

```
Cleaned data exported to cleaned_Hotel Reservation.csv
Cleaning Summary: Before vs After

| Metric                | Before   | After    |
|-----------------------|----------|----------|
| Missing Values        | 0        | 0        |
| Outliers (no adults)  | 139      | 0        |
| Duplicate Rows        | 0        | 0        |
| Duplicate Booking_IDs | 0        | 0        |
| Outliers (Overpriced) | 1        | 0        |
| Total Rows            | 36275    | 36135    |
```

We cleaned a total of **140 records** that presented logical or statistical anomalies. The final dataset is more consistent with realistic hotel booking operations and is now ready for further analysis such as segmentation, forecasting, or pricing strategy modeling.

例如，avg_price_per_room 的最小值或最大值是否在合理范围内？no_of_adults 是否有0值？

我们还应该重点为 lead_time (提前预订天数) 绘制箱形图。提前非常久（比如超过一年）的预订，可能存在异常。同样，no_of_previous_cancellations (过往取消次数) 也值得一看，一个有几十次取消记录的客人，行为模式会比较特殊。

对于一个数据清洗项目来说，更重要的是**识别**出它们，并**讨论**可能的处理方法（如删除、替换、或保留）。对于分类型数据中的不一致，我们的目标是进行修正，统一它们的格式。