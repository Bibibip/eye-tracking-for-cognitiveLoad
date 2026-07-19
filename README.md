# Gaze-Based Cognitive Load Analysis 
### A gaze feature extraction module for multimodal cognitive load estimation and lie detection.
This module estimates **Cognitive Load** using **gaze movement features** extracted from eye-tracking data.

The pipeline preprocesses gaze coordinates from the OpenNeuro Eye Tracking Dataset, extracts cognitive load-related gaze features, and analyzes whether different cognitive load levels can be naturally separated in the feature space using **K-Means clustering**.

In future work, this module will be integrated with **Pupil**, **Blink**, and **rPPG** features for multimodal cognitive load estimation and lie detection.


# Pipeline

```
OpenNeuro Eye Tracking Dataset
(EEG + Eye Tracking + ECG + PPG + Behavioral Data)
                │
                ▼
load_data.py
(Eye Tracking Data Loading)
                │
                ▼
event_parser.py
(Fixation Event Detection)
                │
                ▼
feature_extract.py
(Gaze Feature Extraction)
                │
                ▼
feature.csv
                │
                ▼
kmeans_clustering.py
(Unsupervised Clustering)
```


# File Structure

| File | Description |
|------|-------------|
| `load_data.py` | Load eye-tracking data and split into trials |
| `event_parser.py` | Parse fixation events |
| `feature_extract.py` | Extract gaze-related features |
| `kmeans_clustering.py` | Perform K-Means clustering and visualization |


# Dataset

## OpenNeuro Digit Span Dataset

This project uses the **Digit Span Task** dataset provided by OpenNeuro.

The dataset includes the following physiological and behavioral signals:

- Eye Tracking
- EEG
- Pupillometry
- ECG
- Photoplethysmography (PPG)
- Behavioral Data

The current module utilizes **only the eye-tracking data**.

### Tasks

- Rest
- Digit Span Task

### Assumed Cognitive Load Levels

The cognitive load level was approximated using the task difficulty.

| Task | Cognitive Load |
|------|----------------|
| 5-Digit | Low |
| 9-Digit | Medium |
| 13-Digit | High |

> **Note:** The dataset does not provide official cognitive load labels. Therefore, task difficulty was used as a proxy for cognitive load during the analysis.


# Extracted Features

## Spatial Features

- Scanpath Length
- Gaze Dispersion
- Hull Area
- Center Distance Standard Deviation

## Movement Features

- Movement Mean
- Movement Coefficient of Variation (CV)
- Movement Skewness

## Temporal Features

- Velocity Mean
- Velocity Standard Deviation

## Fixation Features

- Mean Fixation Duration
- Fixation Count

After correlation analysis, redundant features were removed, resulting in a final set of **10 representative features**.


# Preprocessing

- Confidence > 0.6
- Use normalized gaze coordinates only
- Remove out-of-screen gaze samples
- Split data into trials
- Remove trials containing fewer than five samples


# Clustering

K-Means clustering was applied to the extracted gaze features.

The optimal number of clusters was determined using the **Silhouette Score**.

| k | Silhouette Score |
|---|------------------|
| 2 | 0.375 |
| 3 | 0.204 |
| 4 | 0.224 |
| 5 | 0.231 |

The highest Silhouette Score was obtained with **k = 2 (0.375)**.

This suggests that the current gaze features tend to form **two major clusters** rather than clearly separating into the assumed three cognitive load levels (Low, Medium, High). These results indicate that gaze features alone may not be sufficient for reliable cognitive load estimation, highlighting the need for multimodal fusion with physiological signals such as pupil dynamics, blink behavior, and rPPG.


# Cognitive Load-Related Features

| Feature | Expected Trend Under High Cognitive Load |
|----------|------------------------------------------|
| Fixation Duration | Increase |
| Scanpath Length | Varies depending on task |
| Gaze Dispersion | Increase |
| Velocity | Task-dependent |
| Blink Rate | Decrease *(Future Work)* |
| Pupil Diameter | Increase *(Future Work)* |

> Most previous studies estimate cognitive load based on baseline changes or machine learning models rather than fixed thresholds.


# Current Limitations

- Gaze features alone are insufficient to clearly distinguish cognitive load levels.
- No ground-truth cognitive load labels are provided in the dataset.
- The resulting clusters do not perfectly correspond to task difficulty.
- Pupil and blink features are not yet incorporated.

# Future Work

- Integrate pupil-based features
- Integrate blink-related features
- Perform feature-level fusion with rPPG
- Temporal synchronization across modalities
- Cognitive load classification
- Lie detection pipeline
