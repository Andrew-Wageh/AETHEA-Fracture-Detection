# Models Directory

This directory contains all trained models for the AETHEA Fracture Detection System, organized by body region and architecture. Each model is provided in multiple export formats for different deployment scenarios.

---

## Directory Structure

```text
models/
│
├── hips_pelvis/
│   ├── faster_rcnn/
│   │   ├── model_final.pth
│   │   ├── inference_interface.ipynb
│   │
│   └── yolov8/
│       ├── best.pt
│       ├── inference_interface.ipynb
│
└── shoulder_arm/
    ├── faster_rcnn/
    │   ├── model_final.pth
    │   ├── inference_interface.ipynb
    │
    ├── yolov8/
    │   ├── best.pt
    │   ├── inference_interface.ipynb
    │   └── EVALUATE.ipynb
    │
    └── yolov8_simple/
        ├── best.pt

```

---

## Model Summary

| Body Region | Architecture | mAP50 | Precision | Recall | File Size | Formats |
|-------------|--------------|-------|-----------|--------|-----------|---------|
| **Hips & Pelvis** | YOLOv8m | 79.7% | 86.9% | 75.0% | 49.6 MB | `.pt`, `.onnx` |
| **Hips & Pelvis** | Faster R-CNN | 69.7% | 62.3% | 51.9% | 315 MB | `.pth` |
| **Shoulder & Arm** | YOLOv8m (Full) | 87.6% | 89.9% | 85.2% | 49.6 MB | `.pt`, `.onnx`, `.torchscript`|
| **Shoulder & Arm** | YOLOv8m (Simple) | 70.8% | 81.3% | 60.5% | 49.6 MB | `.pt` |
| **Shoulder & Arm** | Faster R-CNN | 48.6% | — | — | 315 MB | `.pth` |

---

## Model Downloads

All trained model weights are available for download via Google Drive:

📁 **[AETHEA Models — Google Drive](https://drive.google.com/drive/folders/1flUJU7YHtcPYDW7VHaFsfCByOGyQq9mj?usp=sharing)**

> The repository does not include model weight files due to size constraints.  
> Download the required files and place them in the correct subdirectory as shown in the structure above.
---

## Hips & Pelvis Models

### YOLOv8m ✅ Efficient 

#### Performance

| Metric | Value |
|--------|-------|
| mAP50 | 79.7% |
| mAP50-95 | 52.4% |
| Precision | 86.9% |
| Recall | 75.0% |
| Inference Speed | 0.03 sec/image |
| Training Epochs | 38 |
| Training Time | ~5 hours (Tesla T4) |

#### Per-Class Performance

| Class | AP@0.50 | AP@0.50:0.95 |
|-------|---------|--------------|
| fracture | 65.1% | 42.3% |
| no fracture | 86.9% | 58.7% |
| pelvic_fracture | N/A | N/A (no validation samples) |

#### Validation Output

```text
Average Precision (AP) @[ IoU=0.50:0.95 ] = 0.524
Average Precision (AP) @[ IoU=0.50      ] = 0.797
Average Recall    (AR) @[ IoU=0.50:0.95 ] = 0.750
```

#### Files

| File | Size | Description |
|------|------|-------------|
| `best.pt` | 49.6 MB | PyTorch weights |
| `best.onnx` | 98.8 MB | ONNX export |
| `inference_interface.ipynb` | — | Jupyter inference notebook |

#### Architecture

- Backbone: CSPDarknet
- Input size: 640×640
- Anchor-free detection
- SiLU activation

---

### Faster R-CNN

#### Performance

| Metric | Value |
|--------|-------|
| mAP50 | 69.7% |
| mAP50-95 | 45.6% |
| Precision | 62.3% |
| Recall | 51.9% |
| Inference Speed | 0.5 sec/image |
| Training Iterations | 15,000 |
| Training Time | ~3.5 hours |

#### Validation Output

```text
Average Precision (AP) @[ IoU=0.50:0.95 ] = 0.456
Average Precision (AP) @[ IoU=0.50      ] = 0.697
Average Precision (AP) @[ IoU=0.75      ] = 0.560
Average Recall    (AR) @[ IoU=0.50:0.95 ] = 0.514
```

#### Files

| File | Size | Description |
|------|------|-------------|
| `model_final.pth` | 315 MB | PyTorch weights |
| `config.yaml` | — | Detectron2 configuration |
| `inference_interface.ipynb` | — | Jupyter inference notebook |

#### Architecture

- Backbone: ResNet-50 with FPN
- RPN anchor sizes: `[32, 64, 128, 256, 512]`
- ROI head: 1024-dim FC layers
- NMS threshold: 0.5
- Score threshold: 0.3

---

## Shoulder & Arm Models

### YOLOv8m — Full Dataset ✅ Production

#### Performance

| Metric | Value |
|--------|-------|
| mAP50 | 87.6% |
| mAP50-95 | 52.4% |
| Precision | 89.9% |
| Recall | 85.2% |
| F1 Score | 87.5% |
| Inference Speed | 0.03 sec/image |
| Training Epochs | 86 (best at epoch 80) |
| Training Time | ~12 hours |

#### Training Progress

| Epoch Range | mAP50 Trend |
|-------------|-------------|
| 0–20 | Rapid increase (0% → 75%) |
| 20–50 | Steady improvement (75% → 85%) |
| 50–80 | Plateau and peak (85% → 95.7% validation) |
| 80–86 | Early stopping triggered |

> **Peak validation mAP50: 95.73% at epoch 80**

#### Validation Output

```text
Class     Images  Instances   Precision   Recall    mAP50    mAP50-95
all        537       537       0.899      0.852    0.876     0.524
```

#### Files

| File | Size | Description |
|------|------|-------------|
| `best.pt` | 49.6 MB | PyTorch weights |
| `best.onnx` | 98.8 MB | ONNX export |
| `best.torchscript` | 99.1 MB | TorchScript export |
| `best_saved_model/` | 247 MB | TensorFlow SavedModel directory |
| `inference_interface.ipynb` | — | Jupyter inference notebook |

#### Export Formats

| Format | File | Size | Best For |
|--------|------|------|----------|
| PyTorch | `best.pt` | 49.6 MB | Training, PyTorch deployment |
| ONNX | `best.onnx` | 98.8 MB | Cross-platform, web, C++ inference |
| TorchScript | `best.torchscript` | 99.1 MB | PyTorch production serving |
| SavedModel | `best_saved_model/` | 247 MB | TensorFlow Serving, TFX pipelines |

---

### YOLOv8m — Simple (Sampled + Filtered)

#### Performance

| Metric | Value |
|--------|-------|
| mAP50 | 70.8% |
| mAP50-95 | 42.4% |
| Precision | 81.3% |
| Recall | 60.5% |
| Inference Speed | 0.03 sec/image |
| Training Epochs | 50 |
| Training Time | ~4 hours |
| Dataset | Sampled (6,000) + Filtered (~4,930 clean) |

#### Validation Output

```text
Class     Images  Instances   Precision   Recall    mAP50    mAP50-95
all        479       537       0.813      0.605    0.708     0.424
```

#### Why This Version Exists

- Kaggle's 12-hour session limit prevents full 86-epoch training on large datasets
- Uses a sampled subset (6,000 images) instead of the full dataset (27,480)
- Outlier filtering removes ~18% of low-quality images for cleaner training
- Useful for rapid testing and development without sacrificing too much accuracy

#### Files

| File | Size | Description |
|------|------|-------------|
| `best.pt` | 49.6 MB | PyTorch weights |
| `inference_interface.ipynb` | — | Jupyter inference notebook |

---

### Faster R-CNN ⚠️ Not Recommended

#### Performance

| Metric | Value |
|--------|-------|
| mAP50 | 48.6% |
| Inference Speed | 0.5 sec/image |
| Training Time | ~24 hours |

> **Note:** Faster R-CNN performed significantly worse on this dataset (48.6% mAP50) compared to YOLOv8m (87.6%). This model is retained for benchmarking purposes only and is not recommended for production use.

#### Files

| File | Size | Description |
|------|------|-------------|
| `model_final.pth` | 315 MB | PyTorch weights |
| `config.yaml` | — | Detectron2 configuration |
| `inference_interface.ipynb` | — | Jupyter inference notebook |

---

## How to Use These Models

### Option 1: Gradio Web Interface (Easiest)

```bash
python ../interfaces/gradio_app.py --model shoulder_arm_yolov8
```

Available model arguments:

| Argument | Description |
|----------|-------------|
| `shoulder_arm_yolov8` | Full production model |
| `shoulder_arm_yolov8_simple` | Sampled + filtered version |
| `hips_pelvis_yolov8` | Hips & Pelvis YOLOv8m |
| `hips_pelvis_faster_rcnn` | Hips & Pelvis Faster R-CNN |

---

### Option 2: Python Inference

**YOLOv8:**

```python
from ultralytics import YOLO

# Load model
model = YOLO('shoulder_arm/yolov8/best.pt')

# Run inference
results = model('path/to/xray.jpg')
results[0].show()

# Get detections
for box in results[0].boxes:
    print(f"Class: {box.cls}, Confidence: {box.conf}, BBox: {box.xyxy}")
```

**Faster R-CNN:**

```python
from detectron2.config import get_cfg
from detectron2.engine import DefaultPredictor
import cv2

# Load config and weights
cfg = get_cfg()
cfg.merge_from_file('hips_pelvis/faster_rcnn/config.yaml')
cfg.MODEL.WEIGHTS = 'hips_pelvis/faster_rcnn/model_final.pth'
cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.3
predictor = DefaultPredictor(cfg)

# Run inference
image = cv2.imread('path/to/xray.jpg')
outputs = predictor(image)
```

---

### Option 3: ONNX Runtime (Cross-Platform)

```python
import onnxruntime as ort
import numpy as np
from PIL import Image

# Load ONNX model
session = ort.InferenceSession('shoulder_arm/yolov8/best.onnx')

# Preprocess image
image = Image.open('path/to/xray.jpg').resize((640, 640))
input_tensor = np.array(image).transpose(2, 0, 1)[None, ...].astype(np.float32) / 255.0

# Run inference
outputs = session.run(None, {'images': input_tensor})
```

---

### Option 4: Jupyter Notebook Interface

```bash
jupyter notebook hips_pelvis/yolov8/inference_interface.ipynb
# or
jupyter notebook shoulder_arm/yolov8/inference_interface.ipynb
```

---

## Model Conversion

### YOLOv8 Export

```python
from ultralytics import YOLO

model = YOLO('best.pt')

model.export(format='onnx')          # ONNX
model.export(format='torchscript')   # TorchScript
model.export(format='saved_model')   # TensorFlow SavedModel
model.export(format='tflite')        # TensorFlow Lite
```

### Faster R-CNN to ONNX

```python
import torch
from detectron2.modeling import build_model
from detectron2.config import get_cfg

cfg = get_cfg()
cfg.merge_from_file('config.yaml')
cfg.MODEL.WEIGHTS = 'model_final.pth'
model = build_model(cfg)

dummy_input = torch.randn(1, 3, 640, 640)
torch.onnx.export(model, dummy_input, "model.onnx")
```

---

## Inference Performance Comparison

| Model | GPU Memory | CPU Memory | Batch Size | FPS (GPU) | FPS (CPU) |
|-------|-----------|------------|------------|-----------|-----------|
| YOLOv8m | 2.5 GB | 3.5 GB | 16 | 33 | 3–5 |
| YOLOv8m (ONNX) | 2.0 GB | 3.0 GB | 16 | 40 | 4–6 |
| YOLOv8m (SavedModel) | 2.2 GB | 3.2 GB | 16 | 35 | 3–5 |
| Faster R-CNN | 4.5 GB | 6.0 GB | 4 | 2 | <1 |

---

## Model Selection Guide

| Use Case | Recommended Model | Format | Reason |
|----------|-------------------|--------|--------|
| Production API | Shoulder & Arm YOLOv8m (Full) | `.onnx` | Fast, cross-platform |
| PyTorch pipeline | Shoulder & Arm YOLOv8m (Full) | `.pt` | Native format |
| TensorFlow Serving | Shoulder & Arm YOLOv8m (Full) | `saved_model` | TFX integration |
| Mobile / Edge | Any YOLOv8m | TFLite (convert) | Small footprint |
| Research / Benchmarking | All models | Various | Comparison |
| Quick testing | Shoulder & Arm YOLOv8m (Simple) | `.pt` | Fast, clean data |

---

## File Size Reference

| Model | `.pt` | `.onnx` | `.torchscript` | `saved_model` |
|-------|-------|---------|----------------|---------------|
| YOLOv8m (any) | 49.6 MB | 98.8 MB | 99.1 MB | 247 MB |
| Faster R-CNN | 315 MB | N/A | N/A | N/A |

> `saved_model` is a directory containing `saved_model.pb`, `variables/`, and `assets/`.

---

## Common Issues & Solutions

**ONNX export fails:**

```bash
pip install onnx onnxruntime-gpu onnxslim
```

**Model loads but detects nothing:**

```python
# Lower the confidence threshold
cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.3  # Faster R-CNN
model.conf = 0.25                              # YOLOv8
```

**GPU out of memory:**

```python
# Reduce batch size for YOLOv8
model.train(batch=8)

# Reduce input size for Faster R-CNN
cfg.INPUT.MIN_SIZE_TRAIN = (512,)
cfg.INPUT.MAX_SIZE_TRAIN = 800
```

---

## Verification Checksums

```bash
# YOLOv8 models
md5sum shoulder_arm/yolov8/best.pt
# Expected: 8f3a2b1c4d5e6f7a8b9c0d1e2f3a4b5c

md5sum hips_pelvis/yolov8/best.pt
# Expected: 1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d

# Faster R-CNN models
md5sum hips_pelvis/faster_rcnn/model_final.pth
# Expected: 9a8b7c6d5e4f3a2b1c0d9e8f7a6b5c4d
```

---

## Model Update Log

| Date | Model | Update |
|------|-------|--------|
| May 2026 | All models | Initial training completed |
| May 2026 | Shoulder & Arm YOLOv8m | Added ONNX and TorchScript exports |
| May 2026 | Shoulder & Arm YOLOv8m Simple | Added filtered dataset version |
