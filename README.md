# TSS Pandas Challenge #2 — Data Cleaning, Merging & Analysis

A hands-on Pandas project based on the **TSS Pandas Challenge #2** Kaggle competition.

This project focuses on working with messy, real-world-style student data and applying a practical data-analysis workflow using Python and Pandas.

## Kaggle Competition

**Competition:** TSS Pandas Challenge #2

**Kaggle URL:**
https://www.kaggle.com/competitions/tss-pandas-challenge-2

The competition is organized by The Software Society (TSS) and focuses on cleaning, combining, and analyzing imperfect student data.

According to the competition description, the main workflow is:

```text
Load → Inspect → Clean → Merge → Analyze
```

No Machine Learning model is required for this competition.

---

# Project Objective

The purpose of this project is to practice Pandas in a realistic data-cleaning and analysis scenario.

The dataset contains intentional data-quality problems such as:

* Missing values
* Duplicate records
* Inconsistent text formatting
* Extra whitespace
* Different capitalization styles
* Dates stored as text
* Missing mentor feedback after merging datasets

The goal is to identify and handle these issues using Pandas and then answer six analytical questions.

---

# Technologies Used

* Python 3
* Pandas
* Jupyter Notebook / Kaggle Notebook
* Git
* GitHub

Main Python library:

```python
import pandas as pd
```

---

# Dataset

The competition provides two CSV files.

## 1. `student-data-v2.csv`

This is the main student dataset.

It contains information about students and their academic performance.

### Columns

| Column               | Description                  |
| -------------------- | ---------------------------- |
| `student_id`         | Unique ID of the student     |
| `name`               | Student's name               |
| `department`         | Student's department         |
| `year`               | Current year of study        |
| `age`                | Student's age                |
| `attendance`         | Attendance percentage        |
| `python_marks`       | Python marks                 |
| `ml_marks`           | Machine Learning marks       |
| `projects_completed` | Number of completed projects |
| `join_date`          | Date the student joined      |

The dataset intentionally contains data-quality issues that need to be cleaned before analysis.

---

## 2. `mentor-feedback.csv`

This dataset contains mentor feedback for students.

### Columns

| Column           | Description                             |
| ---------------- | --------------------------------------- |
| `student_id`     | Student ID used to connect the datasets |
| `mentor`         | Assigned mentor                         |
| `feedback_score` | Mentor feedback score out of 10         |

Not every student has a mentor feedback record.

This allows us to practice a **left join** and handle missing values after merging.

---

# Project Workflow

The project should be completed in the following order:

```text
1. Load the datasets
        ↓
2. Inspect the data
        ↓
3. Handle missing values
        ↓
4. Remove duplicate records
        ↓
5. Standardize department names
        ↓
6. Merge student + mentor data
        ↓
7. Analyze feedback scores
        ↓
8. Convert and analyze dates
        ↓
9. Prepare submission file
        ↓
10. Submit answers to Kaggle
```

The order is important because some questions depend on the cleaned dataset produced in earlier questions.

---

# Question 1 — Missing Values

## Objective

Determine the total number of missing values across the entire student dataset.

### Steps

1. Load `student-data-v2.csv`.
2. Inspect the dataset.
3. Check missing values in every column.
4. Calculate the total number of missing values.

Useful Pandas concepts:

```python
df.isna()
df.isna().sum()
df.isna().sum().sum()
```

The final result should be the total number of missing cells across the entire dataset.

---

# Question 2 — Duplicate Records

## Objective

The dataset contains duplicate records.

Remove all exact duplicate rows and determine how many rows remain.

### Steps

1. Start with the original student DataFrame.
2. Identify duplicate rows.
3. Remove the duplicates.
4. Count the remaining rows.
5. Keep this cleaned DataFrame because later questions depend on it.

Useful Pandas function:

```python
df.drop_duplicates()
```

Example:

```python
df_cleaned = df.drop_duplicates().copy()
```

The resulting `df_cleaned` DataFrame should be used in the following questions.

---

# Question 3 — Standardizing Department Names

## Objective

The `department` column contains inconsistent formatting.

Examples include:

```text
" aiml "
"Aiml"
"aiml"
"AIML"
"Cse"
```

These values represent the same departments but have different formatting.

### Required cleaning

1. Remove leading and trailing whitespace.
2. Convert department names to uppercase.
3. Count the number of students belonging to the `AIML` department after cleaning.

Useful Pandas string operations:

```python
.str.strip()
.str.upper()
```

Example workflow:

```python
df_cleaned["department"] = df_cleaned["department"].str.strip()

df_cleaned["department"] = df_cleaned["department"].str.upper()
```

Then count the standardized `AIML` values.

---

# Question 4 — Merging DataFrames

## Objective

Combine the cleaned student dataset with the mentor feedback dataset.

The two datasets are connected using:

```text
student_id
```

A **left join** must be used.

### Why a left join?

We want to keep **every student**, even if that student does not have mentor feedback.

Conceptually:

```text
Student Dataset
       +
Mentor Feedback
       ↓
    LEFT JOIN
       ↓
All Students
+ Matching Feedback
+ NaN where feedback is unavailable
```

Useful Pandas operation:

```python
pd.merge()
```

Example structure:

```python
df_merged = pd.merge(
    df_cleaned,
    df_mentor,
    on="student_id",
    how="left"
)
```

After merging, determine how many students have a missing `feedback_score`.

Useful function:

```python
df_merged["feedback_score"].isna().sum()
```

---

# Question 5 — GroupBy on Merged Data

## Objective

Using the merged dataset, calculate the average mentor feedback score for each department.

Students without feedback should automatically be ignored when calculating the average.

### Steps

1. Group the merged DataFrame by department.
2. Calculate the mean feedback score.
3. Compare the department averages.
4. Identify the highest average.
5. Round the final answer according to the competition instructions.

Important Pandas concepts:

```python
groupby()
mean()
max()
```

Example structure:

```python
department_feedback = (
    df_merged
    .groupby("department")["feedback_score"]
    .mean()
)
```

This question is particularly useful for learning how `groupby()` and aggregation work on real datasets.

---

# Question 6 — Working With Dates

## Objective

The `join_date` column is initially stored as text.

The dates use the format:

```text
DD-MM-YYYY
```

Example:

```text
13-07-2023
```

The column needs to be converted into a proper Pandas datetime column.

Useful function:

```python
pd.to_datetime()
```

Because the dataset uses day-first dates, make sure the conversion correctly interprets the day and month.

Example:

```python
df_merged["join_date"] = pd.to_datetime(
    df_merged["join_date"],
    dayfirst=True
)
```

After conversion, count students who joined between:

```text
1 August 2023
        ↓
31 August 2023
```

Useful Pandas concepts:

```python
pd.Timestamp()
```

and Boolean filtering.

---

# Submission

The competition provides a `sample_submission` CSV file.

Use the provided file as the template.

The submission contains two columns:

```text
id,answer
```

and one row for each question:

```text
Q1
Q2
Q3
Q4
Q5
Q6
```

The final submission should contain the answers calculated using your Pandas code.

## Important Submission Rules

* Do not change the column names.
* Keep the question IDs as `Q1` through `Q6`.
* Provide one answer for each question.
* Follow the required decimal formatting.
* Do not add extra columns.
* Submit the completed CSV through the Kaggle competition page.

---

# Pandas Concepts Practiced

This project provides practice with the following Pandas concepts.

## Loading Data

```python
pd.read_csv()
```

## Inspecting Data

```python
df.head()
df.tail()
df.shape
df.columns
df.info()
df.describe()
df.dtypes
```

## Missing Values

```python
df.isna()
df.isna().sum()
df.isna().sum().sum()
```

## Duplicates

```python
df.duplicated()
df.drop_duplicates()
```

## String Cleaning

```python
.str.strip()
.str.upper()
.str.lower()
.str.replace()
```

## Merging

```python
pd.merge()
```

Especially:

```python
how="left"
```

## Grouping

```python
df.groupby()
```

## Aggregation

```python
mean()
sum()
count()
min()
max()
```

## Dates

```python
pd.to_datetime()
pd.Timestamp()
```

## Boolean Filtering

```python
df[df["column"] == value]
```

and:

```python
(df["column"] >= value) & (df["column"] <= value)
```

---

# Recommended Working Method

Do not solve all six questions independently.

Follow the data pipeline.

### Step 1 — Load

```text
student-data-v2.csv
mentor-feedback.csv
```

### Step 2 — Inspect

Understand:

* number of rows
* number of columns
* column names
* data types
* missing values
* duplicates
* inconsistent values

### Step 3 — Clean

Fix the student dataset:

```text
Missing values
      ↓
Duplicates
      ↓
Department formatting
```

### Step 4 — Merge

Combine:

```text
Clean Student Data
        +
Mentor Feedback
        ↓
Left Join
```

### Step 5 — Analyze

Perform:

```text
Feedback analysis
      +
Department grouping
      +
Date filtering
```

### Step 6 — Submit

Prepare the required CSV:

```text
Q1 → Answer
Q2 → Answer
Q3 → Answer
Q4 → Answer
Q5 → Answer
Q6 → Answer
```

---

# What I Learned

This project is designed to develop practical Pandas skills rather than simply memorizing functions.

The main lessons from this project include:

* How to investigate an unfamiliar dataset
* How to detect missing data
* How to remove duplicate records
* How to standardize inconsistent text
* How to combine multiple DataFrames
* How to understand and use a left join
* How to handle missing values after a merge
* How to perform grouped analysis
* How to convert strings into datetime values
* How to filter data using date ranges
* How to prepare a structured submission file

---

# Project Structure

A simple project structure can be:

```text
Pandas_Kaggle_Competition/
│
├── student-data-v2.csv
├── mentor-feedback.csv
├── sample_submission_v2.csv
├── TSS_Pandas_Challenge_2_Data_Cleaning_and_Analysis.ipynb
├── submission.csv
└── README.md
```

The notebook contains the Pandas analysis and the CSV files contain the competition data and submission template.

---

# Learning Approach

The solutions in this project are written using Python and Pandas.

The goal is not just to obtain the correct competition answers.

The main goal is to understand:

```text
What is wrong with the data?
        ↓
Why is it a problem?
        ↓
Which Pandas operation can solve it?
        ↓
How can I verify that my cleaning worked?
        ↓
How can I use the cleaned data for analysis?
```

This makes the project useful as a practical Pandas learning exercise.

---

# Competition Rules

According to the competition rules:

* Use Python and Pandas.
* You may use official Python and Pandas documentation.
* You may use Kaggle Notebooks or your own local environment.
* Write and understand your own solution.
* Do not copy another participant's code or answers.
* No Machine Learning model is required.
* Follow the required submission format.
* Do not share final answers with other participants before the competition ends.

For the complete and current rules, refer to the official Kaggle competition page.

---

# Competition Link

**TSS Pandas Challenge #2**

https://www.kaggle.com/competitions/tss-pandas-challenge-2

---

# Final Workflow

```text
              RAW DATA
                  │
                  ▼
              LOAD DATA
                  │
                  ▼
            INSPECT DATA
                  │
                  ▼
        ┌──────────────────┐
        │   DATA CLEANING  │
        │                  │
        │ Missing Values   │
        │ Duplicates       │
        │ Text Formatting  │
        └──────────────────┘
                  │
                  ▼
            MERGE DATASETS
                  │
                  ▼
             GROUP & ANALYZE
                  │
                  ▼
             DATE ANALYSIS
                  │
                  ▼
          PREPARE SUBMISSION
                  │
                  ▼
              KAGGLE
```

## Key Takeaway

The most important lesson from this competition is:

> **Load → Inspect → Clean → Merge → Analyze**

This is a practical workflow for working with imperfect tabular data using Pandas.
