# Shoulder & Arm Dataset

## Overview

| Property | Value |
|----------|-------|
| **Body Region** | Shoulder and Arm |
| **Total Images** | 27,480 |
| **Classes** | 1 (fracture) |
| **Annotation Format** | YOLO (normalized bounding boxes) |
| **Image Formats** | JPG, PNG |
| **Source** | Kaggle (Filtered) |
| **License** | Academic / Research |

---

## Original Dataset Source

- **Kaggle Dataset:** [Arm and Shoulder Fracture Dataset](https://www.kaggle.com/datasets/andrewwageh111/arm-and-shoulder-fracture-dataset)
- **Creator:** Andrew Wageh
- **Preprocessing:** Filtered from public bone fracture X-ray repositories

---

## Dataset Composition

| Body Part | Training | Validation | Test | Total |
|-----------|----------|------------|------|-------|
| Shoulder | 12,665 | 2,235 | 549 | 15,449 |
| Arm | 14,387 | 2,502 | 662 | 17,348 |
| **Total** | **27,052** | **4,737** | **1,211** | **27,480** |

---

## Dataset Split (Original)

| Split | Images | Percentage |
|-------|--------|------------|
| Training | 27,052 | 98.4% |
| Validation | 4,737 | 17.2% |
| Test | 1,211 | 4.4% |

> **Note:** The original split has overlapping percentages due to the way the two datasets (shoulder and arm) were merged. A sampling script was used to create a clean 70/15/15 split.

---

## Sampled Dataset (Used for Training)

Due to Kaggle's 12-hour session limit, a balanced subset was created:

| Split | Original | Sampled (70/15/15) | After Filtering (Outliers Removed) |
|-------|----------|---------------------|-------------------------------------|
| Training | 27,052 | 4,200 | ~3,450 |
| Validation | 4,737 | 900 | ~740 |
| Test | 1,211 | 900 | ~740 |
| **Total** | **27,480** | **6,000** | **~4,930** |

---

## Training Variants

This dataset supports three training configurations:

| Variant | Epochs | Dataset Size | mAP50 | Training Time | Use Case |
|---------|--------|--------------|-------|---------------|----------|
| **Full** | 86 | 27,480 | **87.6%** | ~12 hours | Production model |
| **Sampled** | 40 | 6,000 | 70.8% | ~3 hours | Testing/development |
| **Sampled + Filtered** | 50 | ~4,930 | 70.8% | ~4 hours | Clean data training |

---

## Annotation Format

Binary classification — all annotations are class 0 (fracture):

```text
0 <x_center> <y_center> <width> <height>
```

Example from the dataset:

```text
0 0.512 0.438 0.185 0.218
0 0.750 0.620 0.120 0.150
```

### Coordinate Explanation

All values are normalized to `[0, 1]`:

| Value | Formula | Example |
|-------|---------|---------|
| x_center | bbox_center_x / image_width | 0.512 |
| y_center | bbox_center_y / image_height | 0.438 |
| width | bbox_width / image_width | 0.185 |
| height | bbox_height / image_height | 0.218 |

---

## `data.yaml` for Training

```yaml
# shoulder_arm_data.yaml
# For YOLOv8 training

path: /path/to/dataset
train: train/images
val: val/images
test: test/images

nc: 1
names: ['fracture']
```

---

## Model Performance on This Dataset

### YOLOv8m (Full Dataset — 86 epochs)

| Metric | Score |
|--------|-------|
| mAP50 | 87.6% |
| mAP50-95 | 52.4% |
| Precision | 89.9% |
| Recall | 85.2% |
| F1 Score | 87.5% |
| Inference Speed | 0.03 sec/image |
| Training Time | ~12 hours |
| Best Epoch | 80 |

### Training Progress

| Epoch Range | mAP50 Trend |
|-------------|-------------|
| 0–20 | Rapid increase (0% → 75%) |
| 20–50 | Steady improvement (75% → 85%) |
| 50–80 | Plateau and peak (85% → 95.7% validation) |
| 80–86 | Early stopping triggered |

> **Peak validation mAP50: 95.73% at epoch 80**

### YOLOv8m (Sampled + Filtered — 50 epochs)

| Metric | Score |
|--------|-------|
| mAP50 | 70.8% |
| Precision | 81.3% |
| Recall | 60.5% |

---

## Outlier Detection Results

After running the filtering pipeline on the sampled dataset (6,000 images):

| Filter | Threshold | Count | Percentage | Example |
|--------|-----------|-------|------------|---------|
| Blurry | Laplacian < 50 | ~480 | 8.0% | Motion blur, poor focus |
| Over-zoomed | BBox area < 0.02 | ~260 | 4.3% | Fracture too small |
| Too tight | BBox area > 0.95 | ~180 | 3.0% | Bone fills entire image |
| Distorted | Aspect ratio > 6:1 | ~120 | 2.0% | Extreme cropping |
| **Total** | — | **~1,040** | **17.3%** | — |

### Sample Outlier Report

```json
{
  "train": [
    {
      "file": "blurry_xray_001.jpg",
      "issues": ["BLURRY (score=32.4)"],
      "blur": 32.4,
      "min_bbox_ratio": 0.15,
      "max_aspect": 2.5
    },
    {
      "file": "tiny_fracture_023.jpg",
      "issues": ["OVER-ZOOMED / tiny bbox (ratio=0.0156)"],
      "blur": 125.6,
      "min_bbox_ratio": 0.0156,
      "max_aspect": 1.8
    }
  ]
}
```

---

## Image Preprocessing

All images are preprocessed before training:

| Step | Method | Value |
|------|--------|-------|
| Resize | Letterbox | 640×640 |
| Normalization | Scale | 0–255 → 0–1 |
| Augmentation | Mosaic | 1.0 (probability) |
| Horizontal Flip | Random | 0.5 (probability) |
| HSV Augmentation | Hue/Sat/Val | ±0.015 / ±0.7 / ±0.4 |

---

## Usage in This Project

### Step 1: Download
```bash
kaggle datasets download andrewwageh111/arm-and-shoulder-fracture-dataset
```

### Step 2: Extract
```bash
unzip arm-and-shoulder-fracture-dataset.zip -d datasets/shoulder_arm/raw/
```

### Step 3: Sample (Create balanced subset)
```bash
python scripts/dataset_sampling.py --dataset shoulder_arm --target 6000
```

### Step 4: Filter Outliers
```bash
python scripts/outlier_filtering.py --dataset shoulder_arm
```

### Step 5: Train Full Model
```bash
jupyter notebook notebooks/shoulder_arm/yolov8_training.ipynb
```

### Step 6: Train Simple Model (for testing)
```bash
jupyter notebook notebooks/shoulder_arm/yolov8_simple_training.ipynb
```

---

## Folder Structure After Setup

```text
datasets/shoulder_arm/
├── raw/                          # Original downloaded files (not tracked)
│   ├── train/
│   ├── val/
│   └── test/
│
├── sampled/                      # After sampling (6,000 images) (not tracked)
│   ├── data.yaml
│   ├── train/
│   ├── val/
│   └── test/
│
├── filtered/                     # After outlier removal (~4,930 images) (not tracked)
│   ├── data.yaml
│   ├── train/
│   ├── val/
│   └── test/
│
└── reports/                      # Outlier reports
    └── outlier_report.json
```

---

## Training Checkpoints

The training notebook saves checkpoints every 5–10 epochs:

```text
/checkpoints/
├── epoch0.pt       # Initial weights
├── epoch10.pt      # 10 epochs
├── epoch20.pt      # 20 epochs
├── epoch30.pt      # 30 epochs
├── epoch40.pt      # 40 epochs
├── epoch50.pt      # 50 epochs
├── epoch60.pt      # 60 epochs
├── epoch70.pt      # 70 epochs
├── epoch80.pt      # 80 epochs (best model)
├── last.pt         # Final epoch (86)
└── best.pt         # Best model (epoch 80)
```

---

## Known Limitations

- **Binary classification only:** Does not distinguish fracture types (simple, comminuted, displaced)
- **No multi-view:** Single X-ray view only (AP, lateral not combined)
- **Dataset bias:** Primarily from public repositories, may not generalize to Egyptian population
- **Small test set:** Only 1,211 test images (4.4% of total)

---

## Future Improvements

| Priority | Improvement | Description |
|----------|-------------|-------------|
| 1 | Egyptian population data | Collect local X-rays for fine-tuning |
| 2 | Fracture type classification | Extend to 3+ classes |
| 3 | Multi-view fusion | Combine AP + lateral views |
| 4 | Continuous learning | Radiologist feedback loop |

---

## References

- **Original Kaggle Dataset:** [link](https://www.kaggle.com/datasets/andrewwageh111/arm-and-shoulder-fracture-dataset)
- **YOLOv8 Documentation:** [Ultralytics Docs](https://docs.ultralytics.com)
- **OpenCV Outlier Detection:** [OpenCV Documentation](https://docs.opencv.org)
