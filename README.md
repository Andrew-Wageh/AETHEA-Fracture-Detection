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
| Faster R-CNN | 48.6% | — | — | ~0.5 sec/image |

**Key Finding:** YOLOv8m significantly outperforms Faster R-CNN in both accuracy and speed, making it the recommended model for deployment.

---

## 📁 Repository Structure

