# 🦴 AETHEA Fracture Detection System

## Deep Learning for Automated X-Ray Fracture Analysis

[![Python](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.10-red.svg)](https://pytorch.org/)
[![YOLOv8](https://img.shields.io/badge/YOLOv8-8.4.48-green.svg)](https://github.com/ultralytics/ultralytics)
[![Detectron2](https://img.shields.io/badge/Detectron2-Faster%20R--CNN-orange.svg)](https://github.com/facebookresearch/detectron2)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 📌 Project Information

| Field | Details |
|-------|---------|
| **Project Title** | AETHEA Fracture Detection System |
| **Course** | SET 491 - Deep Learning |
| **Institution** | Egyptian Chinese University |
| **Semester** | Spring 2026 |
| **Instructor** | Dr. Hossam Hawash |
| **Students** | Andrew Wageh (192100099), Omar Atwa (192100141) |

---

## 📋 Project Overview

This project implements two deep learning architectures for fracture detection in X-ray images across two anatomical regions:

**Body Regions Covered:**
- Hips & Pelvis
- Shoulder & Arm

**Architectures Implemented:**
- **Faster R-CNN** (Two-stage detector using Detectron2)
- **YOLOv8m** (One-stage detector using Ultralytics)

**Clinical Application:** Assists radiologists by providing automated fracture detection with bounding box localization and confidence scores.

---

## 📊 Model Performance Results

### Hips & Pelvis Dataset (12,218 X-ray images)

| Model | mAP50 | Precision | Recall | Inference Speed |
|-------|-------|-----------|--------|-----------------|
| YOLOv8m | **79.7%** | 86.9% | 75.0% | ~0.03 sec/image |
| Faster R-CNN | 69.7% | 62.3% | 51.9% | ~0.5 sec/image |

### Shoulder & Arm Dataset (27,480 X-ray images)

| Model | mAP50 | Precision | Recall | Inference Speed |
|-------|-------|-----------|--------|-----------------|
| YOLOv8m | **87.6%** | 89.9% | 85.2% | ~0.03 sec/image |
| Faster R-CNN | 48.6% | 43.68 | 41.76 | ~0.5 sec/image |

**Key Finding:** YOLOv8m significantly outperforms Faster R-CNN in both accuracy and speed, making it the recommended model for deployment.

---

## 📁 Repository Structure
```text
AETHEA-Fracture-Detection/
│
├── docs/              # Project documentation and reports
├── datasets/          # Dataset info (data not included)
├── models/            # Trained model weights and configs
├── notebooks/         # Training and evaluation notebooks
├── interfaces/        # Inference interfaces (Gradio + Jupyter)
├── requirements.txt   # Python dependencies
├── LICENSE            # MIT License
└── README.md          # This file
```

---

## 🔧 Dataset Preparation

- **Sampling:** Balanced subset (6,000 images) with 70/15/15 split.
- **Outlier Filtering:** Removes blurry, zoomed, or distorted images (~18% removed → +5–10% performance).

---

## 🚀 Training Instructions

- **YOLOv8 (Recommended):**
  - Shoulder & Arm: 86 epochs, ~12h (Tesla T4 GPU)
  - Hips & Pelvis: 38 epochs, ~5h
- **Faster R-CNN (Detectron2):**
  - ResNet-50-FPN backbone, 15k iterations, ~3.5h

---

## 💻 Inference Instructions

- **Option 1:** Gradio Web Interface → `python interfaces/gradio_app.py`
- **Option 2:** Jupyter Notebook → `interfaces/shoulder_arm_yolov8_interface.ipynb`

Features:
- Upload X-ray (JPG/PNG/JPEG)
- Confidence threshold slider
- Annotated bounding boxes + confidence scores
- Medical recommendation output

---

## 📤 Model Export Formats

Supports **ONNX**, **TorchScript**, and **TensorFlow SavedModel** for deployment.

---

## 📊 Evaluation Metrics

- mAP50, mAP50-95
- Precision, Recall
- Per-class AP

---

## 📦 Dependencies

```bash
pip install -r requirements.txt
```
---

## 📦 Dependencies

Core: `torch`, `ultralytics`, `detectron2`, `opencv-python`, `gradio`, `ipywidgets`

---

## 🎯 Key Results Summary

| Body Region   | Best Model | mAP50 | Precision | Recall | Inference Time |
|---------------|------------|-------|-----------|--------|----------------|
| Shoulder & Arm | YOLOv8m | 87.6% | 89.9% | 85.2% | 0.03 sec |
| Hips & Pelvis | YOLOv8m | 79.7% | 86.9% | 75.0% | 0.03 sec |

---

## ⚠️ Medical Disclaimer

**FOR RESEARCH AND EDUCATIONAL PURPOSES ONLY**  
This system is an AI-assisted screening tool. All detections must be verified by a qualified medical professional. Do not use as the sole basis for diagnosis or treatment decisions.

---

## 📄 License

MIT License – see the LICENSE file.

---

## 📧 Contact

| Name        | ID        | Email |
|-------------|-----------|-------------------------------|
| Andrew Wageh | 192100099 | 192100099@ecu.edu.eg |
| Omar Atwa    | 192100141 | 192100141@ecu.edu.eg |

Egyptian Chinese University – Faculty of Software Engineering  
Spring 2026

