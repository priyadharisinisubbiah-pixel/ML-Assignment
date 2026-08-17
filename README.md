# Weather Monitoring Dataset – Data Cleaning

## 1. Dataset Overview

This project focuses on cleaning and preprocessing a raw weather monitoring dataset before using it for further data analysis or machine learning applications.

The dataset contains weather-related observations such as temperature, pressure, humidity, wind speed, and weather condition, along with date and time information.

### Dataset Source

* **Source:** Kaggle
* **Availability:** Public dataset
* **Input file:** `weather_monitoring_raw_data.csv`
* **Output file:** `cleaned_weather_monitor.csv`

The raw dataset used in this project is publicly available through Kaggle.

---

## 2. Dataset Structure Before Preprocessing

The raw dataset initially contained:

* **Rows:** 3,500
* **Columns:** 11

### Columns

| Column              | Description                   |
| ------------------- | ----------------------------- |
| `record_id`         | Unique record identifier      |
| `date`              | Date of the observation       |
| `time`              | Time of the observation       |
| `season`            | Season during the observation |
| `month`             | Month number                  |
| `day_of_week`       | Day of the week               |
| `temperature`       | Recorded temperature          |
| `pressure`          | Atmospheric pressure          |
| `humidity`          | Relative humidity             |
| `wind_speed`        | Wind speed                    |
| `weather_condition` | Observed weather condition    |

The original dataset contains both categorical/timing columns and numerical columns. The data types were inspected before preprocessing.

---

## 3. Problems Identified in the Dataset

The following data quality problems were identified and addressed during preprocessing:

### 3.1 Missing Categorical and Timing Values

Categorical and timing-related columns were checked for missing values.

If a row contained missing values in these columns, the complete row was removed because these values are important for identifying the time and categorical context of a weather observation.

After this step:

* Rows reduced from **3,500 to 3,416**
* **84 rows** were removed

The cleaning code specifically removes rows containing missing values in non-numerical columns.

### 3.2 Outliers in Numerical Columns

Outliers were identified in the numerical columns using the **Interquartile Range (IQR)** method.

For each numerical column:

* First quartile (Q1) was calculated.
* Third quartile (Q3) was calculated.
* IQR was calculated as:

`IQR = Q3 - Q1`

The acceptable range was defined as:

`Lower Bound = Q1 - 1.5 × IQR`

`Upper Bound = Q3 + 1.5 × IQR`

Values outside this range were considered outliers.

### 3.3 Missing Numerical Values

Numerical columns could contain missing values either originally or as a result of replacing detected outliers.

These missing numerical values were handled separately during preprocessing.

---

## 4. Preprocessing Techniques Applied

The following preprocessing techniques were applied to the raw dataset.

### Step 1 – Load the Dataset

The raw CSV file was loaded using Pandas.

```python
df = pd.read_csv(file_path)
```

### Step 2 – Identify Data Types

The dataset was examined to distinguish between numerical and non-numerical columns.

Numerical columns were identified using:

```python
num_cols = df.select_dtypes(include=[np.number]).columns
```

Non-numerical columns were identified using:

```python
cat_cols = df.select_dtypes(exclude=[np.number]).columns
```

### Step 3 – Remove Rows with Missing Categorical/Timing Data

Rows containing missing values in categorical or timing columns were removed.

```python
df.dropna(subset=cat_cols, inplace=True)
```

This reduced the dataset from **3,500 rows to 3,416 rows**.

### Step 4 – Detect Numerical Outliers Using IQR

The IQR method was used to identify abnormal numerical values.

Values outside the calculated lower and upper bounds were considered outliers.

### Step 5 – Replace Outliers Using Linear Interpolation

Detected outliers were temporarily replaced with `NaN`.

The missing values created by outlier removal were then replaced using **linear interpolation**:

```python
df[col] = df[col].interpolate(
    method='linear',
    limit_direction='both'
)
```

This approach uses surrounding numerical values to estimate a suitable replacement value instead of simply deleting the entire record.

### Step 6 – Fill Remaining Numerical Missing Values

If numerical missing values remained after interpolation, they were filled using the **median** of the corresponding numerical column.

```python
df[col] = df[col].fillna(df[col].median())
```

The median was selected so that the replacement would be less affected by extreme numerical values.

---

## 5. Dataset Size After Preprocessing

After preprocessing, the dataset contained:

* **Rows before preprocessing:** 3,500
* **Columns before preprocessing:** 11
* **Rows after preprocessing:** 3,416
* **Columns after preprocessing:** 11

Therefore, **84 rows were removed** because of missing categorical/timing information.

The number of columns remained unchanged because the preprocessing process did not remove any columns.

### Summary

| Stage                                                    |  Rows | Columns |
| -------------------------------------------------------- | ----: | ------: |
| Raw Dataset                                              | 3,500 |      11 |
| After removing rows with missing categorical/timing data | 3,416 |      11 |
| Final Cleaned Dataset                                    | 3,416 |      11 |

---

## 6. Final Cleaned Dataset

The final cleaned dataset is saved as:

`cleaned_weather_monitor.csv`

The cleaned dataset contains **3,416 rows and 11 columns**.

The final dataset has been processed to:

* Remove records with missing categorical/timing information
* Detect numerical outliers using the IQR method
* Replace detected numerical outliers using linear interpolation
* Fill remaining numerical missing values using the median
* Preserve all 11 original columns

The cleaning notebook saves the resulting dataset as `cleaned_weather_monitor.csv`.

---

## 7. Files in This Project

```text
├── weather_monitoring_raw_data.csv
├── cleaned_weather_monitor.csv
├── Untitled2.ipynb
└── README.md
```

### File Description

* `weather_monitoring_raw_data.csv` – Original raw dataset obtained from Kaggle.
* `cleaned_weather_monitor.csv` – Final dataset after preprocessing.
* `Untitled2.ipynb` – Python/Jupyter Notebook containing the data cleaning implementation.
* `README.md` – Documentation describing the dataset and preprocessing workflow.

---

## 8. Preprocessing Workflow

```text
Raw Kaggle Dataset
        ↓
Load Dataset
        ↓
Inspect Rows, Columns and Data Types
        ↓
Identify Missing Categorical/Timing Values
        ↓
Remove Rows with Missing Categorical/Timing Data
        ↓
Identify Numerical Columns
        ↓
Detect Numerical Outliers Using IQR
        ↓
Replace Outliers with NaN
        ↓
Linear Interpolation
        ↓
Fill Remaining Numerical Missing Values with Median
        ↓
Final Cleaned Dataset
        ↓
cleaned_weather_monitor.csv
```

## 9. Conclusion

The raw weather monitoring dataset was cleaned using a combination of row removal, IQR-based outlier detection, linear interpolation, and median imputation.

The preprocessing reduced the dataset from **3,500 rows to 3,416 rows**, while maintaining all **11 original columns**. The resulting `cleaned_weather_monitor.csv` can be used as the cleaned dataset for subsequent data analysis and machine learning tasks.
