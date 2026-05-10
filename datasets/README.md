# Datasets Directory

## Overview

This directory contains documentation for the X-ray datasets used to train and evaluate the fracture detection models.  
The actual image files are **not included** in this repository due to size constraints (over 40GB total).  
This README explains where to obtain the datasets and how to prepare them for training.

---

## Available Datasets

| Dataset        | Body Region     | Total Images | Classes | Format | Size   |
|----------------|-----------------|--------------|---------|--------|--------|
| Hips & Pelvis  | Hips, Pelvis    | 12,218       | 3       | YOLO   | ~8 GB  |
| Shoulder & Arm | Shoulder, Arm   | 27,480       | 1       | YOLO   | ~32 GB |

---

## Accessing the Original Datasets

### Hips & Pelvis Dataset
- **Kaggle Link:** [Hips and Pelvis Fracture Detection Dataset](https://www.kaggle.com/datasets/mohamedahmed12232/hips-and-pelvis-fraction-detection)
- **Contents:** Combined hip and pelvis X-ray images with YOLO annotations
- **Classes:** 0 = fracture, 1 = no fracture, 2 = pelvic_fracture

### Shoulder & Arm Dataset
- **Kaggle Link:** [Shoulder and Arm Fracture Dataset](https://www.kaggle.com/datasets/andrewwageh111/arm-and-shoulder-fracture-dataset)
- **Contents:** Filtered shoulder and arm X-ray images
- **Classes:** 0 = fracture (binary classification)

---

## Dataset Preparation Workflow

Original Dataset (Kaggle)  
↓  
Dataset Sampling (70/15/15 split)  
↓  
Outlier Detection & Filtering  
↓  
Clean Dataset Ready for Training


---

## Step 1: Download from Kaggle

```bash
# Install Kaggle CLI (if not installed)
pip install kaggle

# Download Hips & Pelvis dataset
kaggle datasets download mohamedahmed12232/hips-and-pelvis-fraction-detection

# Download Shoulder & Arm dataset  
kaggle datasets download andrewwageh111/arm-and-shoulder-fracture-dataset

# Unzip files
unzip hips-and-pelvis-fraction-detection.zip -d datasets/hips_pelvis/raw/
unzip arm-and-shoulder-fracture-dataset.zip -d datasets/shoulder_arm/raw/
```
---

## Step 2: Dataset Sampling 

Due to Kaggle's 12-hour session limit, a sampling script is provided to create a balanced subset:

```bash
python ../scripts/dataset_sampling.py
```
---

## Sampling Configuration

| Parameter        | Value            |
|------------------|------------------|
| Target total     | 6,000 images     |
| Train ratio      | 70% (4,200)      |
| Validation ratio | 15% (900)        |
| Test ratio       | 15% (900)        |
| Random seed      | 42 (reproducible)|

---

## Output Structure

SAMPLED_DATASET_BALANCED/
├── data.yaml
├── train/
│   ├── images/  (4,200 images)
│   └── labels/  (4,200 .txt files)
├── val/
│   ├── images/  (900 images)
│   └── labels/  (900 .txt files)
└── test/
    ├── images/  (900 images)
    └── labels/  (900 .txt files)
---

## Step 3: Outlier Detection & Filtering

Low-quality images are automatically detected and removed using OpenCV:

```bash
python ../scripts/outlier_filtering.py
```
---

### Detection Thresholds

| Filter      | OpenCV Method       | Threshold | Why                                |
|-------------|---------------------|-----------|------------------------------------|
| Blur        | Laplacian variance  | < 50      | Blurry X-rays hide fracture features |
| Over-zoomed | BBox area           | < 0.02    | Fracture too small to learn from   |
| Too tight   | BBox area           | > 0.95    | Missing anatomical context         |
| Distorted   | Aspect ratio        | > 6:1     | Warped bounding boxes              |

---

### Sample Output

```text
Scanning train: 100%|██████████| 4200/4200
TRAIN: 752/4200 outlier images (17.9%)
  WARNING  blurry_image_001.jpg -> BLURRY (score=32.4)
  WARNING  tiny_fracture_023.jpg -> OVER-ZOOMED / tiny bbox (ratio=0.0156)

Validation: 148/900 outlier images (16.4%)
Test: 142/900 outlier images (15.8%)

Total outliers found: 1042 (17.4%)
```

### Results After Filtering

| Split      | Original | Removed | Clean  |
|------------|----------|---------|--------|
| Train      | 4,200    | ~750    | ~3,450 |
| Validation | 900      | ~150    | ~750   |
| Test       | 900      | ~140    | ~760   |
| **Total**  | **6,000**| **~1,040** | **~4,960** |

> **Performance Impact:** Removing outliers improves mAP50 by 5–10%.

---

## Dataset Format (YOLO)

All datasets use the YOLO annotation format:

```text
<class_id> <x_center> <y_center> <width> <height>
```

Where all values are normalized to `[0, 1]`:

| Field      | Range    | Description                          |
|------------|----------|--------------------------------------|
| class_id   | 0 to N-1 | Object class index                   |
| x_center   | 0.0–1.0  | BBox center X (relative to width)    |
| y_center   | 0.0–1.0  | BBox center Y (relative to height)   |
| width      | 0.0–1.0  | BBox width (relative to width)       |
| height     | 0.0–1.0  | BBox height (relative to height)     |

Example annotation file (`image_001.txt`):

```text
0 0.512 0.438 0.185 0.218
```

---

## `data.yaml` Configuration

Example for Shoulder & Arm dataset:

```yaml
path: /path/to/dataset
train: train/images
val: val/images
test: test/images

nc: 1
names: ['fracture']
```

Example for Hips & Pelvis (3 classes):

```yaml
nc: 3
names: ['fracture', 'no fracture', 'pelvic_fracture']
```

---

## Directory Structure After Preparation

```text
datasets/
├── README.md                           # This file
├── hips_pelvis_dataset_info.md         # Detailed dataset documentation
├── shoulder_arm_dataset_info.md        # Detailed dataset documentation
│
├── hips_pelvis/
│   ├── raw/                            # Original downloaded files (not tracked)
│   └── processed/                      # After sampling + filtering (not tracked)
│
└── shoulder_arm/
    ├── raw/                            # Original downloaded files (not tracked)
    └── processed/                      # After sampling + filtering (not tracked)
```

> ⚠️ **Important:** The `raw/` and `processed/` folders contain large image files and are excluded from Git via `.gitignore`. Only the documentation files are committed.

---

## TensorFlow Extended (TFX) Compatibility

The filtered dataset structure is compatible with TensorFlow data pipelines:

```python
import tensorflow as tf

def parse_yolo_line(line):
    # Parse YOLO format for TFX pipeline
    parts = tf.strings.split(line, ' ')
    class_id = tf.strings.to_number(parts[0], tf.int32)
    coords = tf.strings.to_number(parts[1:5], tf.float32)
    return class_id, coords
```

---

## References

- **YOLO Annotation Format:** [Ultralytics Docs](https://docs.ultralytics.com)
- **COCO Dataset Format:** [COCO Documentation](https://cocodataset.org/#format-data)
- **Original Dataset Sources:** See `hips_pelvis_dataset_info.md` and `shoulder_arm_dataset_info.md`





