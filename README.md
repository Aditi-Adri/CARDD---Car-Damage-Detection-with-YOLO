# CARDD---Car-Damage-Detection-with-YOLO
## 📂 Dataset
**CarDD (Car Damage Detection)** — YOLO-format annotations.
🔗 **Download:** [CarDD with YOLO Annotations (Kaggle)](https://www.kaggle.com/datasets/gabrielfcarvalho/cardd-with-yolo-annotations-images-labels)

- **4,000 images** · **8,740 annotated damage boxes** · **6 classes**
- Trained at **1024 × 1024** resolution

| Split      | Images |
|------------|-------:|
| Train      | 2,816  |
| Validation | 810    |
| Test       | 374    |
| **Total**  | **4,000** |

**Class distribution (boxes across all splits):**

| Class           | Boxes |
|-----------------|------:|
| scratch         | 3,595 |
| dent            | 2,543 |
| crack           | 898   |
| lamp_broken     | 704   |
| glass_shatter   | 681   |
| tire_flat       | 319   |
| **Total**       | **8,740** |

> ⚠️ **Challenge:** strong class imbalance (scratch is ~11× tire_flat) and many small,
> low-contrast damage regions — which motivated testing a high-resolution P2 detection head.

data.yaml
nc: 6
names:
  0: dent
  1: scratch
  2: crack
  3: glass_shatter
  4: lamp_broken
  5: tire_flat

Models :
Two detectors trained with an identical data pipeline and schedule (Ultralytics framework) for a fair head-to-head comparison.

| Setting        | YOLO11-L     | YOLOv8-L-P2  |
|----------------|--------------|--------------|
| Extra P2 head  | ❌ no        | ✅ yes       |
| Image size     | 1024         | 1024         |
| Max epochs     | 150          | 150          |
| Early-stopped  | epoch 106    | epoch 46     |
| Patience       | 30           | 30           |
| Optimizer      | auto         | auto         |
| Initial LR     | 0.01         | 0.01         |
Pipeline: EDA & class-balance analysis → image enhancement + augmentation → training (early stopping, patience 30) → evaluation on the held-out test split → head-to-head comparison.

Results (Test Split) — Overall

| Model        | mAP@50 | mAP@50-95 | mAP@75 | Precision | Recall |
|--------------|:------:|:---------:|:------:|:---------:|:------:|
| YOLO11-L     | 0.725  | 0.557     | 0.589  | 0.795     | 0.668  |
| YOLOv8-L-P2  | 0.721  | 0.541     | 0.571  | 0.748     | 0.701  |
Per-class AP@50-95

| Class          | YOLO11-L | YOLOv8-L-P2 |
|----------------|:--------:|:-----------:|
| glass_shatter  | 0.860    | 0.860       |
| tire_flat      | 0.859    | 0.839       |
| lamp_broken    | 0.716    | 0.689       |
| dent           | 0.344    | 0.323       |
| scratch        | 0.309    | 0.281       |
| crack          | 0.253    | 0.256       |




Takeaways ->
YOLO11-L is the best overall model — higher mAP@50-95 (0.557 vs 0.541) and precision (0.795).
It wins 5 of 6 classes on AP@50-95; YOLOv8-L-P2 only edges it on crack (+0.003).
The extra P2 head did not overturn YOLO11-L on small damage, and YOLOv8-L-P2 plateaued much sooner (early stop @ epoch 46).
Hardest classes: thin cracks and scratches (low AP) — the focus of future work.
