# Gaze-Based Cognitive Load Analysis 
### A gaze feature extraction module for multimodal cognitive load estimation.
This repository provides a preprocessing and feature extraction pipeline for gaze-based cognitive load analysis using the OpenNeuro Digit Span dataset.

The current work focuses on extracting gaze features from eye-tracking data, performing exploratory data analysis (EDA), selecting representative features, and investigating whether gaze features alone naturally separate different cognitive load levels using unsupervised clustering.

The extracted gaze features are intended to be integrated with pupil, blink, and rPPG features in a future multimodal cognitive load estimation framework.


# Pipeline

```
OpenNeuro Digit Span Dataset
(Eye Tracking)
                │
                ▼
load_data.py
(Data Loading)
                │
                ▼
event_parser.py
(Event Parsing)
                │
                ▼
trial_builder
(Trial Segmentation)
                │
                ▼
feature.csv
        │
        ├──────────────► EDA
        │
        ├──────────────► Correlation Analysis
        │
        ├──────────────► Feature Selection
        │
        ▼
kmeans_clustering.py
(Exploratory Clustering)
```


# File Structure

| File | Description |
|------|-------------|
| `load_data.py` | Load gaze data and preprocess raw eye-tracking signals |
| `event_parser.py` | Parse event markers from the Digit Span task |
| `feature_extract.py` | Build trial windows and extract gaze features |
| `kmeans_clustering.py` | Exploratory K-Means clustering and visualization |


# Dataset

## OpenNeuro Digit Span Dataset

This project uses the OpenNeuro Digit Span dataset.

The dataset contains multiple physiological signals collected during a working memory experiment:

- Eye Tracking
- Pupillometry
- EEG
- ECG
- Photoplethysmography (PPG)
- Behavioral Data

This repository currently uses only gaze information extracted from the eye-tracking recordings.

### Tasks

- Rest
- Digit Span Task

### Cognitive Load Assumption

The dataset does not provide explicit cognitive load labels.
Following previous working memory studies, task difficulty was used as a proxy:

| Task | Cognitive Load |
|------|----------------|
| 5-Digit | Low |
| 9-Digit | Medium |
| 13-Digit | High |


# Extracted Features

## Spatial Features

- Gaze Dispersion
- Hull Area
- Center Distance Statistics
- Scanpath Length

## Movement Features

- Movement Mean
- Movement Standard Deviation
- Movement Maximum
- Movement Coefficient of Variation
- Movement Kurtosis

## Velocity Features

- Mean Velocity
- Velocity Standard Deviation
- Maximum Velocity

## Fixation Features

- Fixation Count
- Mean Fixation Duration
- Maximum Fixation Duration
  
# Feature Selection
Exploratory analysis was performed to remove redundant features before downstream modeling.

The following analyses were conducted:

- Distribution analysis
- Boxplot visualization
- Correlation analysis
- Variance Inflation Factor (VIF)

Highly correlated or redundant features were removed, reducing the original feature set while preserving complementary gaze information.

# Preprocessing

- Remove invalid gaze samples
- Trial segmentation using event markers
- Compute gaze movement features
- Generate one feature vector per trial


# Clustering

K-Means clustering was applied as an exploratory analysis to investigate whether gaze features naturally form groups corresponding to cognitive load.

The Silhouette Score was used to compare different values of k.

| k | Silhouette Score |
|---|------------------|
| 2 | 0.375 |
| 3 | 0.204 |
| 4 | 0.224 |
| 5 | 0.231 |

The highest score was obtained when k = 2, suggesting that gaze features alone do not clearly separate the three assumed cognitive load levels.

These observations indicate that gaze information alone may be insufficient for reliable cognitive load estimation.


# Current Progress

✅ OpenNeuro gaze data preprocessing
✅ Event parsing and trial segmentation
✅ Gaze feature extraction
✅ Exploratory Data Analysis (EDA)
✅ Correlation and VIF-based feature selection
✅ Exploratory K-Means clustering

# Future Work

- Integrate pupil features
- Integrate blink features
- Integrate rPPG features
- Feature-level multimodal fusion
- Subject-independent Group K-Fold evaluation
- Supervised cognitive load classification
- Lie detection framework
