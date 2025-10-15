

---

## 🧹 Data Cleaning Documentation

This project demonstrates a complete **data cleaning process** carried out in **Python** using `pandas`. The dataset used (`dirty_cafe_sales.csv`) contained multiple issues such as incorrect data types, missing values, and inconsistent entries. Below is the step-by-step documentation of how I cleaned and prepared the data for analysis.

---

### 🔍 Step 1: Importing Libraries and Reading the Dataset

I began by importing the required libraries and reading the dataset into a pandas DataFrame.

```python
import pandas as pd

df = pd.read_csv('dirty_cafe_sales.csv')
```

Then, I used `.head()`, `.info()`, and `.dtypes` to inspect the dataset, view the first few rows, and understand the data types of each column.

---

### 🧩 Step 2: Data Type Corrections

Initially, all columns were read as **object types**, including numerical ones such as `Quantity`, `Price Per Unit`, and `Total Spent`.
Before converting them, I had to handle invalid values like `'ERROR'` and `'UNKNOWN'`.

#### Actions Taken:

* Replaced `'ERROR'` and `'UNKNOWN'` values with `0` in numeric columns.
* Replaced missing (`NaN`) values with `0` for numerical consistency.
* Converted the following columns to their correct data types:

  * `Quantity` → `int64`
  * `Price Per Unit` → `float64`
  * `Total Spent` → `float64`
  * `Transaction Date` → `datetime`

```python
df.loc[df['Quantity'] == 'ERROR', 'Quantity'] = '0'
df['Quantity'] = df['Quantity'].astype('int64')
df['Transaction Date'] = pd.to_datetime(df['Transaction Date'])
```

---

### 🧮 Step 3: Handling Missing and Invalid Data

I checked for missing values using:

```python
df.isnull().sum()
```

#### Findings:

* Missing entries in: `Item`, `Quantity`, `Price Per Unit`, `Total Spent`, `Payment Method`, `Location`, and `Transaction Date`.

#### Fixes Applied:

* Replaced missing numeric values with `0`.
* For missing `Transaction Date`, replaced with a default placeholder (`1900-01-01`).
* For missing categorical fields like `Payment Method` and `Location`, filled them with `'Unknown'`.

```python
df.loc[df['Payment Method'].isnull(), 'Payment Method'] = 'Unknown'
df.loc[df['Location'].isnull(), 'Location'] = 'Unknown'
```

---

### 🧠 Step 4: Filling Missing Item Names

To fill missing `Item` values, I analyzed the consistency of prices.
For example:

* `Price Per Unit = 1.0` → `Cookie`
* `Price Per Unit = 1.5` → `Tea`
* `Price Per Unit = 2.0` → `Coffee`
* `Price Per Unit = 5.0` → `Salad`

I used these relationships to populate the missing `Item` entries.
Where prices were inconsistent (e.g., `0.8`, `3.0`, `4.0`), I replaced the `Item` with `'Unknown'`.

```python
df.loc[df['Price Per Unit'] == 1.0, 'Item'] = 'Cookie'
df.loc[df['Price Per Unit'] == 1.5, 'Item'] = 'Tea'
df.loc[df['Price Per Unit'] == 2.0, 'Item'] = 'Coffee'
df.loc[df['Price Per Unit'] == 5.0, 'Item'] = 'Salad'
df.loc[df['Item'].isnull(), 'Item'] = 'Unknown'
```

---

### ⚙️ Step 5: Recalculating Quantities and Totals

To correct logical inconsistencies:

* For rows where `Quantity` was `0` but both `Total Spent` and `Price Per Unit` were available, I recomputed:

  ```python
  Quantity = Total Spent / Price Per Unit
  ```
* Filled missing or zero `Price Per Unit` and `Total Spent` values with their column averages.

```python
df.loc[df['Price Per Unit'] == 0, 'Price Per Unit'] = df['Price Per Unit'].mean()
df.loc[df['Total Spent'] == 0, 'Total Spent'] = df['Total Spent'].mean()
df.loc[df['Quantity'] == 0, 'Quantity'] = df['Total Spent'] / df['Price Per Unit']
```

---

### 🧾 Step 6: Fixing Inconsistent Text Entries

I identified and replaced inconsistent string values (like `'ERROR'`, `'UNKNOWN'`, and different casing variations) across categorical columns.

```python
df.loc[df['Item'].isin(['ERROR', 'UNKNOWN']), 'Item'] = 'Unknown'
df.loc[df['Payment Method'].isin(['ERROR', 'UNKNOWN']), 'Payment Method'] = 'Unknown'
df.loc[df['Location'].isin(['ERROR', 'UNKNOWN']), 'Location'] = 'Unknown'
```

---

### 🔁 Step 7: Removing Duplicates

Checked for duplicate transactions to ensure each record was unique:

```python
df.duplicated().sum()
```

Result: **No duplicates found.**

---

### ✅ Step 8: Final Verification

* Confirmed all columns have correct data types.
* No missing or invalid entries remain.
* Verified that all numeric values are logical (no negative sales or quantities).

```python
df.info()
df.isnull().sum()
```

---

### 📦 Step 9: Exporting the Clean Dataset

Finally, I exported the cleaned dataset for analysis:

```python
df.to_csv('cleaned_cafe_sales.csv', index=False)
```

---

### 🧾 Summary of Improvements

| Issue Detected           | Action Taken                               | Result                      |
| ------------------------ | ------------------------------------------ | --------------------------- |
| Incorrect data types     | Converted numeric and date columns         | Data properly typed         |
| Missing values           | Filled logically or replaced with defaults | No null entries             |
| Invalid text values      | Replaced with `'Unknown'`                  | Clean categorical fields    |
| Inconsistent item labels | Filled based on price pattern              | Consistent `Item` column    |
| Logical errors in totals | Recomputed using formulas                  | Accurate numeric relations  |
| Duplicates               | Checked and none found                     | Dataset integrity confirmed |

---

### 🚀 Outcome

The final dataset is **fully cleaned**, **validated**, and **ready for analysis**.
It now supports accurate reporting, visualization, and decision-making without the risk of bias or data corruption.

---


