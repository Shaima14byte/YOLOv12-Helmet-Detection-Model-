# YOLOv12-Helmet-Detection-Model-
#  Helmet Detection Model — AI-Powered PPE Safety for Construction Sites
Project-M4U3 Shaima Husameldin Kamil
> Built with [Roboflow](https://roboflow.com) · YOLOv12 · Ultralytics
> 
> 📦 **Roboflow Model**: [Trained Model](https://app.roboflow.com/shaimas-workspace/helmet-detection-model-6wiet/models/helmet-detection-model-6wiet/2)
> 
> AI-powered helmet detection model for construction sites that identifies workers wearing safety helmets and counts helmets per image. Built using Roboflow and YOLOv8, the model supports automated safety monitoring, enabling fast visual inspection, improved compliance tracking, and scalable PPE analysis from site imagery.


---

## 🏗️ AECO Problem Statement

In the **Architecture, Engineering, Construction & Operations (AECO)** industry, ensuring Personal Protective Equipment (PPE) compliance — specifically helmet usage — is a critical safety requirement. Manual inspection is:

- ❌ Time-consuming and inconsistent
- ❌ Difficult to scale across large or multi-site projects
- ❌ Prone to human error and reporting gaps

This model addresses the challenge of **automated helmet compliance monitoring** by detecting and counting helmets in site imagery, enabling faster audits, real-time alerts, and data-driven safety reporting.

---

## ✅ Success Criteria

The model is considered successful when it:

| Criteria | Target |
|---|---|
| Detects helmets reliably across varied site conditions | ✅ |
| Produces accurate helmet count per image | ✅ |
| Handles occlusion, distance, and weather variation | ✅ |
| Minimizes false positives on non-helmet objects | ✅ |
| Achieves stable mAP@50 above 70% | ✅ ~77% achieved |

---

## 📊 Model Details

| Parameter | Value |
|---|---|
| **Model Variant** | YOLOv12 |
| **Epochs** | 309 |
| **Ultralytics Version** | `ultralytics >= 8.3.0` |
| **Image Size** | 640×640 |
| **Framework** | Roboflow + Ultralytics |
| **mAP@50** | ~77% |
| **mAP@50-95** | ~38% |

---

## 📈 Training Analytics

### Roboflow Training Graphs
![Roboflow Training Graphs](https://cdn.abacus.ai/images/a263195d-7266-4872-9177-7e0e0062da09.png)

> **Model Performance**: mAP stabilizes around **~77%** after epoch 50, with consistent convergence through epoch 309. Box Loss, Class Loss, and Object Loss all show healthy downward trends.

### Advanced Training Analytics
![Advanced Training Graphs](https://cdn.abacus.ai/images/4bccc9c9-1185-44c4-9a9c-987560d193d2.png)

| Metric | Observation |
|---|---|
| `train/box_loss` | Steady decline from ~2.0 → ~0.5 ✅ |
| `train/cls_loss` | Strong drop from ~4.0 → ~0.3 ✅ |
| `train/dfl_loss` | Gradual decrease from ~1.6 → ~0.8 ✅ |
| `metrics/precision(B)` | Stabilizes at ~0.85 ✅ |
| `metrics/recall(B)` | Stabilizes at ~0.75 ✅ |
| `metrics/mAP50(B)` | Reaches ~0.77 ✅ |
| `metrics/mAP50-95(B)` | Reaches ~0.38 ✅ |
| `val/box_loss` | Plateaus ~2.0 (expected for val) |
| `val/cls_loss` | Drops and stabilizes ~1.2 ✅ |

---

## 🗂️ Dataset

- 📦 **Roboflow Dataset**: [Helmet Detection Model v2](https://app.roboflow.com/shaimas-workspace/helmet-detection-model-6wiet/2)
- **Classes**: `helmet`
- **Augmentations applied**: Flip, brightness/contrast, crop, scale variation

---

## 🖼️ Inference Results

### ✅ Success Cases

These images show the model performing correctly — helmets are detected with high confidence and accurate bounding boxes.

| Success Case 1 | Success Case 2 | Success Case 3 |
|---|---|---|
| ![Success 1](https://cdn.abacus.ai/images/d9fc65d2-66b1-4637-82aa-597bc98fb2a4.png) | ![Success 2](https://cdn.abacus.ai/images/bd808885-2b95-4e90-a1f1-2b92c71cd4cc.png) | ![Success 3](https://cdn.abacus.ai/images/9aec1117-a433-4e15-8e79-9aad1fba71e6.png) |
| 2 helmets detected at 90% & 88% confidence | 1 helmet detected at 93% confidence | 4 objects detected across varied poses |

---

### ❌ Failure Cases — Analysis & Fixes

These images highlight where the model struggled and what was done to address each issue.

---

#### ❌ Fail Case 1 — Crowded Scene with Overlapping Helmets

![Fail Case 1](https://cdn.abacus.ai/images/a16187d1-a5e8-458f-85cc-6891d15b80a6.png)

**5 objects detected — but with inconsistent confidence (89%–95%) and overlapping bounding boxes**

| | Detail |
|---|---|
| **Why it failed** | Dense grouping of workers caused overlapping bounding boxes. The model struggled to separate individual helmets in a tightly packed scene, leading to merged or duplicate detections. |
| **Root cause** | Insufficient training examples of **crowded multi-person scenes** with overlapping helmets. |
| **How it was fixed** | Added more annotated images of dense groups, applied **mosaic augmentation**, and tuned **NMS (Non-Maximum Suppression) IoU threshold** to better separate overlapping boxes. |

---

#### ❌ Fail Case 2 — Small Objects at Distance with Low Confidence

![Fail Case 2](https://cdn.abacus.ai/images/38e93837-353b-4056-9920-8ea106e73de2.png)

**3 objects detected — but with low confidence (41%–80%) and missed detections for distant workers**

| | Detail |
|---|---|
| **Why it failed** | Workers in the background are far from the camera, making helmets appear very small (few pixels). The model lacked sufficient small-object training data. |
| **Root cause** | Underrepresentation of **long-range / wide-angle** shots in the training dataset. |
| **How it was fixed** | Expanded the dataset with more **long-distance site images**, applied **scale-down augmentation**, and slightly lowered the `conf` threshold for small-object inference scenarios. |

---

#### ❌ Fail Case 3 — Adverse Weather & Low Contrast (Snow/Overcast)

![Fail Case 3](https://cdn.abacus.ai/images/5f02c0de-3bb8-4ec0-9a48-f97856023727.png)

**8 objects detected — but with very low confidence on background workers (3%–59%) and noisy detections**

| | Detail |
|---|---|
| **Why it failed** | Snow and overcast lighting drastically reduce contrast and texture cues. Workers at varying depths created mixed scales, and background clutter (tools, lines) confused the detector. |
| **Root cause** | Lack of **adverse weather** training examples and insufficient multi-scale variation in the dataset. |
| **How it was fixed** | Added images from **snowy, foggy, and low-light** environments. Applied **brightness, contrast, and saturation augmentations** to simulate weather variation. Increased scale diversity in training batches. |

---

## 🚀 How to Reproduce

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/helmet-detection-model.git
cd helmet-detection-model
