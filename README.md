# Beyond Segmentation Accuracy: Reliable Fetal Head Biometry from Ultrasound Images

## Overview

This repository implements a geometry-aware deep learning pipeline for automated fetal head biometry from 2D ultrasound images. Unlike conventional approaches that prioritize segmentation overlap metrics such as Dice and IoU, this work focuses on clinically meaningful measurement accuracy of head circumference (HC).

The framework separates segmentation from biometric computation and demonstrates that reliable fetal head measurements depend more on geometry-aware post-processing and evaluation design than on segmentation accuracy alone.

---

## Key Idea

Most fetal ultrasound studies evaluate performance primarily using segmentation metrics. However, in clinical practice, fetal head circumference is obtained by fitting an ellipse to the head boundary. Small contour inconsistencies can significantly affect the final measurement, even when Dice scores are high.

This repository implements:

1. Transformer-based fetal head segmentation
2. Geometry-aware mask refinement
3. Ellipse fitting with anatomical constraints
4. Head circumference computation in physical units
5. Biometry-centric evaluation (MAE, RMSE)

---

## Pipeline

```
Ultrasound Image
        ↓
Swin Transformer Segmentation
        ↓
Largest Component Selection
        ↓
Morphological Refinement
        ↓
Safe Ellipse Fitting
        ↓
Head Circumference Computation
        ↓
Biometry Evaluation (MAE / RMSE)
```

---

## Model Architecture

The segmentation stage uses a Swin Transformer-based encoder–decoder architecture. The model is trained exclusively for boundary delineation using segmentation loss functions (Dice or BCE-Dice). No direct regression of head circumference is performed.

The architectural choice is implementation-driven rather than contribution-driven. The core contribution lies in the geometry-aware measurement framework and evaluation strategy.

---

## Geometry-Aware Measurement

After segmentation:

* Only the largest connected component is retained.
* Morphological operations remove spurious artifacts.
* Ellipse fitting is applied to the cleaned contour.
* Anatomical plausibility checks are enforced.
* Head circumference is computed using the standard elliptical approximation.
* Pixel values are converted to millimeters using image-specific spacing.

Invalid or anatomically implausible masks are excluded from measurement.

---

## Evaluation Metrics

### Segmentation Metrics (Secondary)

* Dice coefficient
* Intersection over Union (IoU)

### Biometry Metrics (Primary)

* Head Circumference Mean Absolute Error (MAE)
* Head Circumference Root Mean Square Error (RMSE)
* Valid measurement rate

---

## Experimental Results

Validation results on a public fetal head ultrasound dataset:

* Dice: **0.9669**
* IoU: **0.9378**
* Valid samples: **200**
* HC MAE: **4.97 pixels**
* HC RMSE: **6.91 pixels**

Using dataset-provided pixel spacing, the average head circumference error is approximately **2 mm**, which lies within clinically acceptable limits.

These findings support the central claim that reliable fetal head biometry does not depend solely on maximizing segmentation accuracy.

---

## Repository Structure

```
├── Experiment-1(ResNet).ipynb   # ResNet-based segmentation experiment
├── Experiment-2(Swin).ipynb     # Swin Transformer segmentation experiment
├── Experiment-3(Final).ipynb    # Final model with complete pipeline
├── Models/                      # Saved model weights
│   └── resnet_unet.pth
└── README.md
```

---

## Installation

```bash
pip install torch torchvision timm albumentations opencv-python pandas numpy
```

GPU acceleration is recommended but not mandatory.

---

## How to Run

1. Place the dataset in the `training_set/` directory.
2. Ensure the CSV file contains:

   * filename
   * pixel size (mm)
   * head circumference (mm)
3. Open and run `Experiment-3(Final).ipynb`.

The notebook performs:

* Model training
* Mask prediction
* Ellipse fitting
* Head circumference evaluation

---

## Reproducibility Notes

* Fixed train/validation split (80/20).
* No direct regression of biometric values.
* Measurement is derived exclusively through geometric fitting.
* Invalid ellipse cases are filtered using anatomical constraints.

---

## Contribution

This work introduces a biometry-centric evaluation perspective for fetal ultrasound analysis. Instead of pursuing incremental improvements in segmentation overlap, it emphasizes clinically reliable measurement as the primary performance indicator.

---

## Future Work

* Cross-dataset validation
* Uncertainty-aware measurement estimation
* Extension to additional fetal biometric parameters
* 3D ultrasound evaluation

---

## License

This project is intended for research and academic use.

---
