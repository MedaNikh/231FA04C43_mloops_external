# MLOps-Feast-SkillGap

## Student Details
- Name: M.Nikhitha
- Register Number: 231FA04C43
- Section: 15

> Rename the GitHub repository to `<231FA04c43>MLOps-Feast-SkillGap`.

## Problem Statement
This project converts a curriculum-industry skill-gap dataset into a Feast-based feature store. The feature store provides reusable features for historical model training and online prediction.

## Dataset
The included dataset is a starter example. Replace it with the dataset created in the previous activity before final submission.
Student_ID	Programming	DSA	DBMS	Communication	Cloud	ML	DevOps	Teamwork	Employable
S001	3	2	6	5	5	4	3	10	0
S002	3	8	2	2	3	5	5	10	0
S003	2	10	5	10	8	5	9	6	1
S004	2	4	8	7	6	4	5	7	0
S005	3	3	8	3	7	7	6	2	0
S006	9	10	3	8	3	10	6	7	1
S007	5	3	2	5	6	3	5	3	0
S008	8	6	9	7	4	7	7	5	1
S009	6	3	4	10	5	4	9	8	0
S010	6	10	5	7	2	5	2	7	0
S011	8	6	3	5	7	5	9	8	0
S012	9	4	6	4	5	10	10	6	1
S013	8	8	7	5	4	10	9	3	1
S014	2	3	4	4	8	3	8	8	0
S015	9	10	6	10	2	3	10	6	1


- Number of skills: 20 in the starter dataset
- Columns: `skill_id`, `skill`, `curriculum_score`, `industry_demand`, `practical_score`, `event_timestamp`
- Target: `job_ready`
- Entry creation: curriculum and industry scores are represented on a 0-100 scale. `gap_score` is engineered as industry demand minus curriculum score, and `job_ready` is a binary target derived from the starter data.

## Feature Engineering
| Feature | Meaning |
|---|---|
| `curriculum_score` | Curriculum coverage/proficiency score |
| `industry_demand` | Industry demand score |
| `practical_score` | Practical/project skill score |
| `gap_score` | Industry demand minus curriculum score |
| `readiness_score` | Combined curriculum, demand and practical-skill score |

### Example calculation
For `gap_score`:

`gap_score = industry_demand - curriculum_score`

A positive gap means industry demand is higher than curriculum coverage.

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
       +----------------------+
       |                      |
       v                      v
Historical Retrieval     Materialization
       |                      |
       v                      v
Model Training           Online Store
                              |
                              v
                       Online Retrieval
                              |
                              v
                          Prediction
```

## Implementation

### Entity
The Feast entity is `skill_id`. Each skill is uniquely identified by this ID.

### Data Source
The engineered feature data is stored in Parquet and registered with Feast as a `FileSource`. SQLite is used as the local online store.

### FeatureView
The `skill_features` FeatureView contains curriculum, industry, practical, gap and readiness features.

### Historical Retrieval
`historical_retrieval.py` uses `get_historical_features()` to create point-in-time correct training features.

### Model
`train_model.py` trains a simple Random Forest classifier using the Feast historical feature output.

### Online Retrieval
`online_retrieval.py` uses `get_online_features()` after materialization.

## Running the Project

### 1. Create and activate a virtual environment

Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

Linux/macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Create the Parquet feature data

```bash
python prepare_data.py
```

### 4. Register Feast objects

```bash
cd feature_repo
feast apply
cd ..
```

### 5. Retrieve historical features

```bash
python historical_retrieval.py
```

### 6. Train the model

```bash
python train_model.py
```

### 7. Materialize features into the online store

```bash
python materialize.py
```

### 8. Retrieve online features and make one prediction

```bash
python online_retrieval.py
```

## Required Analysis Questions

### 1. What is the entity?
`skill_id` is the entity because it uniquely identifies each skill.

### 2. What features are stored in the FeatureView?
`curriculum_score`, `industry_demand`, `practical_score`, `gap_score`, and `readiness_score`.

### 3. How was one feature calculated?
`gap_score` is calculated as:

`industry_demand - curriculum_score`

### 4. Difference between original and feature dataset
The original dataset contains the raw curriculum/industry observations. The feature dataset adds engineered, model-ready values such as `gap_score` and `readiness_score` and includes an event timestamp required for point-in-time feature retrieval.

### 5. Purpose of the offline store
The offline store keeps historical feature data used to build training datasets and perform historical retrieval.

### 6. Purpose of the online store
The online store keeps the latest materialized feature values for low-latency prediction-time retrieval.

### 7. Purpose of `feast apply`
`feast apply` registers or updates the Feast entities, data sources and FeatureViews in the feature store.

### 8. What does materialization do?
Materialization loads historical feature values into the online store so that they can be retrieved during prediction.

### 9. Why use Feast instead of calculating features separately?
Feast provides one managed definition of features that can be reused for training and online prediction, reducing training-serving inconsistencies.

### 10. Two limitations of the current dataset
1. The starter dataset is small and may not represent the full curriculum or job market.
2. Skill scores are simplified numerical representations and may not capture real-world hiring complexity.

### 11. Two possible improvements
1. Add more curriculum, job-posting and industry evidence.
2. Add richer features such as job frequency, salary demand, experience level, certifications and time-varying skill demand.

## Results

After running the scripts, paste the actual outputs here:

- Historical feature output: see `outputs/historical_features.csv`
- Model accuracy: see `outputs/model_metrics.txt`
- Online feature output: see `outputs/online_features.json`
- Final prediction: see `outputs/final_prediction.txt`

## Repository Structure

```text
<RegisterNumber>MLOps-Feast-SkillGap/
├── data/
│   └── skill_gap.csv
├── feature_repo/
│   ├── feature_store.yaml
│   └── feature_store.py
├── outputs/
│   └── .gitkeep
├── .gitignore
├── historical_retrieval.py
├── materialize.py
├── online_retrieval.py
├── prepare_data.py
├── train_model.py
├── requirements.txt
└── README.md
```
