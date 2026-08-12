## Student Details
  Name           :s.varaprasad
  Register Number:231FA04140
  Section        :09
  

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
Final Prediction
Student ID: STU00001

Predicted Skill-Gap Category: Medium
