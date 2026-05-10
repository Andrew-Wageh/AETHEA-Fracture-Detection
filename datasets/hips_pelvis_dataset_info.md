# Hips & Pelvis Dataset

## Overview

| Property | Value |
|----------|-------|
| **Body Region** | Hips and Pelvis |
| **Total Images** | 12,218 |
| **Classes** | 3 (fracture, no fracture, pelvic_fracture) |
| **Annotation Format** | YOLO (normalized bounding boxes) |
| **Image Formats** | JPG, PNG |
| **Source** | Kaggle |
| **License** | Academic / Research |

---

## Original Dataset Source

- **Kaggle Dataset:** [Hips and Pelvis Fraction Detection](https://www.kaggle.com/datasets/mohamedahmed12232/hips-and-pelvis-fraction-detection)
- **Creator:** Mohamed Ahmed
- **Collection:** Combined from multiple medical imaging repositories

---

## Class Distribution

| Class ID | Name | Training Instances | Validation Instances | Notes |
|----------|------|-------------------|---------------------|-------|
| 0 | fracture | 9,591 | 196 | Hip fractures |
| 1 | no fracture | 14,356 | 181 | Negative samples |
| 2 | pelvic_fracture | ~50 | 0 | Limited representation |

> **Important:** Class 2 (pelvic_fracture) has zero instances in the validation set, meaning the model cannot be properly validated on this class.

---

## Dataset Split (Original)

| Split | Images | Percentage |
|-------|--------|------------|
| Training | 11,840 | 96.9% |
| Validation | 184 | 1.5% |
| Test | 194 | 1.6% |
| **Total** | **12,218** | **100%** |

> **Note:** The original split is heavily imbalanced toward training (97%). A sampling script was used to create a more balanced split for training.

---

## Sampled Dataset (Used for Training)

To balance the dataset and fit within Kaggle constraints, a sampled version was created:

| Split | Original | Sampled (70/15/15) | After Filtering (Outliers Removed) |
|-------|----------|---------------------|-------------------------------------|
| Training | 11,840 | 2,800 | ~2,300 |
| Validation | 184 | 600 | ~490 |
| Test | 194 | 600 | ~490 |
| **Total** | **12,218** | **4,000** | **~3,280** |

---

## Annotation Format

Labels are stored as `.txt` files with the same name as the corresponding image:

```text
<class_id> <x_center> <y_center> <width> <height>
```

Example from the dataset:

```text
0 0.512 0.438 0.185 0.218
1 0.750 0.620 0.120 0.150
2 0.300 0.500 0.080 0.100
```

### Coordinate Explanation

All coordinates are normalized relative to image dimensions:

```text
x_center = bbox_center_x / image_width   (range: 0.0 - 1.0)
y_center = bbox_center_y / image_height  (range: 0.0 - 1.0)
width    = bbox_width / image_width      (range: 0.0 - 1.0)
height   = bbox_height / image_height   (range: 0.0 - 1.0)
```

---

## `data.yaml` for Training

```yaml
# hips_pelvis_data.yaml
# For YOLOv8 training

path: /path/to/dataset
train: train/images
val: val/images
test: test/images

nc: 3
names: ['fracture', 'no fracture', 'pelvic_fracture']
```

---

## Model Performance on This Dataset

### YOLOv8m Results

| Metric | Score |
|--------|-------|
| mAP50 | 79.7% |
| mAP50-95 | 52.4% |
| Precision | 86.9% |
| Recall | 75.0% |
| Inference Speed | 0.03 sec/image |
| Training Epochs | 38 |
| Training Time | ~5 hours |

### Faster R-CNN Results

| Metric | Score |
|--------|-------|
| mAP50 | 69.7% |
| mAP50-95 | 45.6% |
| Precision | 62.3% |
| Recall | 51.9% |
| Inference Speed | 0.5 sec/image |
| Training Iterations | 15,000 |
| Training Time | ~3.5 hours |

### Per-Class Performance (YOLOv8m)

| Class | AP@0.50 | AP@0.50:0.95 |
|-------|---------|--------------|
| fracture | 65.1% | 42.3% |
| no fracture | 86.9% | 58.7% |
| pelvic_fracture | N/A | N/A (no validation samples) |

---

## Outlier Detection Results

After running the filtering pipeline on the sampled dataset (4,000 images):

| Issue | Count | Percentage | Impact |
|-------|-------|------------|--------|
| Blurry images (Laplacian < 50) | ~350 | 8.8% | Removed |
| Over-zoomed (bbox area < 0.02) | ~180 | 4.5% | Removed |
| Too tight (bbox area > 0.95) | ~120 | 3.0% | Removed |
| Distorted (aspect ratio > 6:1) | ~80 | 2.0% | Removed |
| **Total removed** | **~730** | **18.3%** | — |

---

## Known Limitations

- **Class 2 scarcity:** Pelvic fracture class has very few samples and zero validation instances
- **Small validation set:** Original 184 validation images is too small for reliable evaluation
- **Class imbalance:** "No fracture" class has more instances than "fracture"
- **Anatomical variation:** X-rays from different machines/hospitals may vary

---

## Usage in This Project

### Step 1: Download
```bash
kaggle datasets download mohamedahmed12232/hips-and-pelvis-fraction-detection
```

### Step 2: Extract
```bash
unzip hips-and-pelvis-fraction-detection.zip -d datasets/hips_pelvis/raw/
```

### Step 3: Sample (Optional)
```bash
python scripts/dataset_sampling.py --dataset hips_pelvis --target 4000
```

### Step 4: Filter Outliers
```bash
python scripts/outlier_filtering.py --dataset hips_pelvis
```

### Step 5: Train
```bash
jupyter notebook notebooks/hips_pelvis/yolov8_training.ipynb
```

---

## Folder Structure After Setup

```text
datasets/hips_pelvis/
├── raw/                          # Original downloaded files (not tracked in Git)
│   ├── train/
│   ├── val/
│   └── test/
│
├── sampled/                      # After sampling (not tracked)
│   ├── data.yaml
│   ├── train/
│   ├── val/
│   └── test/
│
└── filtered/                     # After outlier removal (not tracked)
    ├── data.yaml
    ├── train/
    ├── val/
    └── test/
```

---

## References

- **Original Kaggle Dataset:** [link](https://www.kaggle.com/datasets/mohamedahmed12232/hips-and-pelvis-fraction-detection)
- **YOLOv8 Documentation:** [Ultralytics Docs](https://docs.ultralytics.com)
- **Medical Imaging Standards:** DICOM, NIfTI

---

## Citation

If using this dataset, please cite:

```bibtex
@dataset{hips_pelvis_fracture,
  author    = {Mohamed Ahmed},
  title     = {Hips and Pelvis Fracture Detection Dataset},
  year      = {2024},
  publisher = {Kaggle},
  url       = {https://www.kaggle.com/datasets/mohamedahmed12232/hips-and-pelvis-fraction-detection}
}
```

---

*Last Updated: May 2026*
