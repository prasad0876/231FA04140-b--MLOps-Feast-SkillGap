## Student Details

- **Name:** S. Varaprasad
- **Register Number:** 231FA04140
- **Section:** 09
  

## Problem Statement

CSE graduates may have academic knowledge but may not have all the technical, practical, and professional skills expected by the IT industry.

This project focuses on identifying curriculum-industry skill gaps using student skill scores. The system considers programming, databases, problem solving, communication, cloud computing, teamwork, aptitude, and data analysis.

The student skill data is processed and stored using **Feast Feature Store**. The stored features are then used by a Machine Learning model to classify students into **Low, Medium, or High skill-gap categories**.

## Dataset

### Dataset Size

The dataset contains **5,000 student records**:

- 4,000 training records
- 1,000 testing records

### Number of Skills

The dataset contains 8 main skill areas:

1. Programming
2. Databases
3. Problem Solving
4. Communication
5. Cloud Computing
6. Teamwork
7. Aptitude
8. Data Analysis

### Dataset Columns

```text
Student_ID
Programming
Databases
Problem_Solving
Communication
Cloud_Computing
Teamwork
Aptitude
Data_Analysis
Average_Skill_Gap
Skill_Gap_Category
Overall_Skill_Score
Training_Recommendation
```

### Target

The target variable is:

```text
Skill_Gap_Category
```

Possible categories:

- Low
- Medium
- High

### How Entries Were Created

The project uses a synthetic student skill-gap dataset. It contains student IDs and numerical skill scores representing curriculum and industry-related CSE skills.

## Feature Engineering

The feature engineering process uses numerical and categorical preprocessing.

- Numerical features: median imputation and StandardScaler
- Categorical features: most-frequent imputation and OneHotEncoder

The processed dataset contains the transformed numerical and categorical features required for model training.

## Feast Features

The Feast FeatureView stores the following 10 features:

| Feature | Meaning |
|---|---|
| `Programming` | Student programming skill score |
| `Databases` | Student database skill score |
| `Problem_Solving` | Student problem-solving skill score |
| `Communication` | Student communication skill score |
| `Cloud_Computing` | Student cloud computing skill score |
| `Teamwork` | Student teamwork skill score |
| `Aptitude` | Student aptitude score |
| `Data_Analysis` | Student data analysis skill score |
| `Overall_Skill_Score` | Overall student skill score |
| `Training_Recommendation` | Recommended training area |

## Example Feature Calculation

`Overall_Skill_Score` represents the average of the eight main skill scores.

For example:

```text
Programming       = 75.5
Databases         = 58.2
Problem Solving   = 57.5
Communication     = 61.8
Cloud Computing   = 64.3
Teamwork          = 72.2
Aptitude          = 36.3
Data Analysis     = 76.5
```

Calculation:

```text
(75.5 + 58.2 + 57.5 + 61.8 + 64.3 + 72.2 + 36.3 + 76.5) / 8
= 62.79
```

Therefore:

```text
Overall_Skill_Score = 62.79
```

## Feast Architecture

```text
                    Original Dataset
                           |
                           v
                  Feature Engineering
                           |
                           v
                  Parquet Offline Data
                           |
                           v
                   Feast FeatureView
                           |
                           v
              +-------------------------+
              |                         |
              v                         v
     Historical Features          Materialization
              |                         |
              v                         v
       Model Training             Online Store
                                        |
                                        v
                                 Online Retrieval
                                        |
                                        v
                                   Prediction
```

## Implementation

### Entity

The Feast entity is:

```text
student
```

The join key is:

```text
student_id
```

Each student is uniquely identified using the student ID.

### Data Source

The Feast data source is:

```text
data/student_features.parquet
```

It is used as the offline data source.

### FeatureView

The FeatureView is:

```text
student_skill_features
```

It contains the 10 student skill features listed above.

### Historical Retrieval

Historical features are retrieved using:

```python
store.get_historical_features()
```

The historical feature dataset contains 5,000 records and is used for Machine Learning model training.

### Model

The project uses:

```text
Logistic Regression
```

The model predicts:

```text
Skill_Gap_Category
```

The dataset is split into 80% training and 20% testing data.

### Online Retrieval

After materialization, online features are retrieved using:

```python
store.get_online_features()
```

The online store used in the project is SQLite.

## Feast Commands

The Feast objects are registered using:

```bash
feast apply
```

Materialization copies offline feature data into the online store.


The workflow starts with a student skill dataset, performs feature engineering, stores the features in Parquet format, registers them through Feast, retrieves historical features for Machine Learning, materializes the features into a SQLite online store, and retrieves them for online prediction.

The Logistic Regression model achieved **89.7% accuracy**. Online feature retrieval was successfully demonstrated, and the final prediction for `STU00001` was **Medium Skill Gap**.

## Results

### Historical Feature Output

Example historical feature output:

```text
student_id   event_timestamp              Programming   Databases   Problem_Solving   Communication   Cloud_Computing   Teamwork   Aptitude   Data_Analysis   Overall_Skill_Score   Training_Recommendation
STU00001     2026-01-01 00:00:00+00:00   75.5          58.2        57.5              61.8            64.3              72.2       36.3       76.5           62.79                 Aptitude
STU00002     2026-01-01 00:00:01+00:00   65.9          57.7        62.7              63.5            63.1              70.2       50.2       88.6           65.24                 Aptitude
STU00003     2026-01-01 00:00:02+00:00   77.7          36.3        58.6              65.0            41.1              64.4       57.2       66.9           58.40                 Databases
STU00004     2026-01-01 00:00:03+00:00   90.8          59.7        68.5              78.2            68.4              70.0       68.2       56.7           70.06                 Databases
STU00005     2026-01-01 00:00:04+00:00   64.5          76.7        83.8              52.8            31.2              76.4       81.4       42.9           63.71                 Cloud_Computing
```
### Historical feature output shape:

(5000, 12)
Model Accuracy

### The Logistic Regression model achieved:

Accuracy = 89.70%
Online Feature Output

For:

Student ID: STU00001

the retrieved online features include:
```text
Communication            = 61.8
Databases                = 58.2
Teamwork                 = 72.2
Data Analysis            = 76.5
Cloud Computing          = 64.3
Overall Skill Score      = 62.79
Training Recommendation  = Aptitude
Programming              = 75.5
Aptitude                 = 36.3
Problem Solving          = 57.5

```
### Final Prediction
Student ID: STU00001
Predicted Skill-Gap Category: Medium


## Required Analysis Questions and Answers

### 1. What is the entity in your Feast implementation?

The entity in the Feast implementation is:

```text
student
```

The join key used to uniquely identify each student is:

```text
student_id
```

Each student is represented by a unique student ID such as `STU00001`.

### 2. List the features stored in your FeatureView.

The `student_skill_features` FeatureView stores the following 10 features:

```text
Programming
Databases
Problem_Solving
Communication
Cloud_Computing
Teamwork
Aptitude
Data_Analysis
Overall_Skill_Score
Training_Recommendation
```

### 3. Explain how one feature was calculated.

The `Overall_Skill_Score` feature is calculated as the average of the eight main skill scores.

For `STU00001`:

```text
Programming       = 75.5
Databases         = 58.2
Problem Solving   = 57.5
Communication     = 61.8
Cloud Computing   = 64.3
Teamwork          = 72.2
Aptitude          = 36.3
Data Analysis     = 76.5
```

Calculation:

```text
(75.5 + 58.2 + 57.5 + 61.8 + 64.3 + 72.2 + 36.3 + 76.5) / 8
= 62.79
```

Therefore:

```text
Overall_Skill_Score = 62.79
```

### 4. What is the difference between your original dataset and the feature dataset?

The original dataset contains the complete student skill-gap information, including student ID, eight skill scores, average skill gap, skill-gap category, overall skill score, and training recommendation.

The Feast feature dataset contains the features required by the FeatureView along with timestamp information such as:

```text
student_id
event_timestamp
created_timestamp
```

The feature dataset is stored in Parquet format and is used by Feast for historical and online feature retrieval.

### 5. What is the purpose of the offline store?

The offline store is used to store historical feature data.

In this project, the feature data is stored in Parquet format:

```text
data/student_features.parquet
```

It allows historical features to be retrieved for Machine Learning model training.

### 6. What is the purpose of the online store?

The online store stores materialized feature values so that the latest features can be retrieved quickly during prediction.

In this project, SQLite is used as the online store.

The online features are retrieved using:

```python
store.get_online_features()
```

### 7. What is the purpose of `feast apply`?

The command:

```bash
feast apply
```

registers and applies the Feast definitions in the feature repository.

It registers the entity, FeatureView, and other Feast objects so they can be used by the feature store.

### 8. What does materialization do?

Materialization copies feature data from the offline data source into the online store.

This makes the features available for fast online retrieval during prediction.

In this project, the materialized features are stored in the SQLite online store.

### 9. What is the advantage of retrieving features through Feast instead of manually calculating them separately during training and prediction?

Using Feast provides a consistent feature source for both model training and prediction.

The main advantages are:

1. The same feature definitions can be used during training and prediction.
2. Historical features can be retrieved for model training.
3. Online features can be retrieved for real-time prediction.
4. Feature calculations do not need to be manually repeated in different parts of the system.
5. It reduces the risk of differences between training features and prediction features.

### 10. State two limitations of your current dataset.

Two limitations of the current dataset are:

1. **Synthetic Dataset:** The dataset is synthetic, so it may not completely represent real student performance or actual industry requirements.

2. **Limited Industry Evidence:** The current dataset contains a fixed set of skills and does not continuously include new curriculum or industry evidence. Industry skill requirements can change over time.

### 11. State two ways your feature store could be improved when more curriculum and industry evidence becomes available.

The feature store could be improved by:

1. **Adding real-world and updated skill data:** Include more student performance data and current industry skill requirements so that the features better represent real-world employability needs.

2. **Adding new feature categories:** Add features from certifications, projects, internships, coding assessments, job descriptions, and other curriculum-industry evidence. These could be maintained through additional or updated Feast FeatureViews.
