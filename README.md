# Gaze and Webcam Pipeline for Cognitive Load Research

This repository contains the gaze-analysis code and a webcam-based data
collection prototype developed for cognitive-load research with a digit-span
task. It is a **code-only** repository: source datasets, trained Branch1
models, MediaPipe model assets, personal calibration files, and generated
trial outputs are intentionally excluded.

The project currently has two components:

- `Gaze/`: offline gaze preprocessing and trial-level feature extraction for
  the OpenNeuro Digit Span dataset.
- `webcamTest/`: a local webcam recorder for collecting iris, blink, and
  pupil-proxy signals and creating a participant-specific pupil reference.

## Repository scope

The repository is intended to document and share the implementation. It is
not yet a standalone end-to-end cognitive-load classifier. In particular,
classification accuracy has not been validated using webcam-collected data.

The original Branch1 model expects 41 trial-level features from pupil, gaze,
and blink signals. The webcam feature schema has been checked against that
input order, but webcam signals differ from the eye-tracking hardware signals
used during model training.

## Project structure

```text
.
├── Gaze/
│   ├── src/                 # Offline loading, event parsing, trial building, gaze features
│   └── experiments/          # Exploratory analysis and feature inspection
├── webcamTest/
│   ├── webcam_trial.py       # Webcam capture and manual trial recorder
│   ├── calibration.py        # Pupil calibration utilities
│   ├── signal_processing.py  # Iris, EAR blink, and frame-level signal extraction
│   ├── extract_features.py   # Branch1-compatible trial feature extraction
│   ├── trial_recorder.py     # Trial and digit-onset recording state
│   └── requirements.txt      # Dedicated webcam environment dependencies
├── .gitignore
└── .gitattributes
```

## Offline gaze pipeline

The `Gaze/` code processes normalized gaze coordinates from the OpenNeuro
Digit Span dataset and creates one feature vector per trial.

Main feature groups include:

- Gaze movement statistics
- Gaze dispersion and center-distance statistics
- Gaze velocity and acceleration statistics
- Fixation duration statistics
- Convex-hull area

The dataset uses digit-span difficulty as a cognitive-load proxy:

| Digit span | Load label |
|---:|---|
| 5 | Low |
| 9 | Medium |
| 13 | High |

## Webcam calibration prototype

The webcam pipeline uses MediaPipe Face Landmarker to collect:

- Iris landmark positions as a webcam gaze proxy
- Iris-landmark pixel distance as a pupil-size proxy
- Eye aspect ratio (EAR) as a blink proxy

Run the webcam recorder from the repository root:

```powershell
.\.venv-webcam\Scripts\python.exe .\webcamTest\webcam_trial.py
```

Create the environment if needed:

```powershell
python -m venv .venv-webcam
.\.venv-webcam\Scripts\python.exe -m pip install -r .\webcamTest\requirements.txt
```

### Required local asset

The MediaPipe Face Landmarker asset is not committed. Before running the
webcam recorder, place it at:

```text
webcamTest/model/face_landmarker.task
```

### Manual pupil calibration

The webcam preview window must have keyboard focus.

1. Keep the face visible and look naturally at the camera for about two seconds.
2. Press `c` once to start calibration.
3. For a 5-digit trial, press `s`; press `d` once at each digit onset (five
   times, approximately one second apart); then press `e` once.
4. Repeat the same procedure for 9-digit and 13-digit trials.

`e` ends the current trial only. `q` exits the program. At least 27 valid
digit samples are required. A successful run creates:

```text
webcamTest/output/pupil_reference.json
webcamTest/output/trial_raw.csv
```

The reference JSON stores a participant-specific mean and standard deviation
for later z-score normalization of digit-level pupil samples.

## Integration contract for the future task UI

When the final task UI is available, it should call the recorder at the
corresponding task events:

```python
recorder.start_trial()                # trial start
recorder.mark_digit(index, onset)     # immediately when a digit is displayed
raw_df = recorder.end_trial()         # trial end
```

This preserves the timing needed by `extract_features.py` to identify the
pupil sample associated with each digit onset.

## Current limitations

- Webcam pupil size is an iris-landmark pixel-distance proxy, not the physical
  `diameter_3d` measure used in the original training data.
- Webcam gaze is based on iris position in the camera image, whereas the
  offline data uses screen-normalized gaze coordinates.
- Webcam blink events use EAR thresholds rather than the original blink signal.
- The repository does not include the training data or deployed Branch1 model.
- Webcam-based classification accuracy remains to be evaluated.

These limitations mean that the current code is suitable for implementation,
calibration, and integration testing, but not yet for validated deployment.

## Ignored local files

The following are intentionally excluded from Git:

- Virtual environments (`.venv/`, `.venv-webcam/`)
- MediaPipe model assets (`webcamTest/model/`)
- Calibration and trial outputs (`webcamTest/output/`)
- Python caches and the temporary normalized model cache
