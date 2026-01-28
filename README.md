# UAV-OMVCD (BCD Subset)

**UAV-OMVCD**: UAV Oblique Multi-View Panoramic Change Detection Dataset
**Content released in this repository**: the **BCD (Binary Change Detection)** subset of UAV-OMVCD (organized in a **LEVIR-CD-compatible** format), designed for land-use change detection research and model development in typical regions across China.

* Image format: PNG
* Resolution: 512 × 512
* Data split: Train / Val / Test = 1735 / 225 / 221 (fixed split for reproducible experiments)
* Overall change proportion: 14.3%
* Reference organization: LEVIR-CD (train/val/test + A/B/Label + list)

---

## 1. Dataset Overview

UAV-OMVCD is constructed from multi-temporal UAV oblique panoramic imagery, with acquisition regions covering representative urban scenes such as Hefei, Tangshan, and Nanjing. Compared with conventional orthophotos (nadir/vertical view), this dataset adopts **multi-angle oblique observations**, which better capture building facades and partially occluded areas. As a result, it improves the discriminability of changed regions in complex urban environments and reduces pseudo-changes caused by seasonal variations.

---

## 2. Dataset Access

* Download:

```text
Please request access via email: 1025071815@njupt.com
```

---

## 3. Data Format and Organization (LEVIR-CD Compatible)

The dataset root directory contains three subsets: `train/ val/ test/`, used for training, validation, and final testing, respectively. Each subset follows the same directory structure:

* `A/`: first-temporal (image1) raw images (PNG, 512×512)
* `B/`: second-temporal (image2) raw images (PNG, 512×512, paired one-to-one with `A` by filename)
* `Label/`: pixel-wise change masks (PNG, single-channel binary)
* `img.txt`: list of all sample filenames in this split (without paths)

In addition, the root directory includes a `list/` folder that stores the fixed split protocol:

* `list/train.txt`
* `list/val.txt`
* `list/test.txt`

The number of samples in Train/Val/Test is **1735 / 225 / 221**, ensuring that different researchers can conduct reproducible benchmarking under an identical split.

### 3.2 Sample Pairing

Each sample consists of files sharing the same filename, e.g., `sample_001.png`:

```text
train/A/sample_001.png
train/B/sample_001.png
train/Label/sample_001.png
```

> ![Figure 1.png](picture/%E5%9B%BE%E7%89%871.png)
> Column 1 = image1 (A), Column 2 = image2 (B), Column 3 = mask (Label).

### 3.3 Label Encoding Convention

* `Label` is a single-channel binary mask with pixel values in `{0, 255}`
* **Default convention**: `0 = unchanged`, `255 = changed`

---

## 4. Benchmark: Model Training and Evaluation (BCD)

To validate dataset usability and training stability, we benchmarked five representative change detection models under a unified experimental setting:

* **BIT**
* **ChangeMamba**
* **CDLamba**
* **SARASNet**
* **CDXLSTM**

### 4.1 Unified Experimental Settings

* Data split: Train / Val / Test = 1735 / 225 / 221 (non-overlapping)
* Training epochs: 120
* Model selection: during training, the checkpoint with the best **F1** score is used for test-set inference
* Metrics: OA, Precision, Recall, F1-score, IoU (pixel-wise segmentation metrics)

**Hardware/Software Environment:**

* GPU: NVIDIA RTX 4090 (24GB) × 1
* CPU: Intel Xeon Platinum 8352V
* RAM: 120 GB
* Disk: 2 TB
* OS: Ubuntu 20.04
* Python: 3.8
* CUDA: 11.8
* PyTorch: 2.0.1 (cu118)

### 4.2 Test Results (Table 3)

Benchmark results of all models on this dataset:
![Figure 2.png](picture/%E5%9B%BE%E7%89%872.png)

| Model       | OA         | Precision  | Recall     | F1-score   | IoU        |
| ----------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| BIT         | 0.9457     | 0.9071     | 0.8803     | 0.8930     | 0.8150     |
| ChangeMamba | 0.9429     | 0.9027     | 0.8729     | 0.8869     | 0.8060     |
| **CDLamba** | **0.9492** | **0.9107** | **0.8914** | **0.9007** | **0.8266** |
| SARASNet    | 0.9429     | 0.9050     | 0.8698     | 0.8861     | 0.8049     |
| CDXLSTM     | 0.9487     | 0.9139     | 0.8851     | 0.8987     | 0.8236     |

### 4.3 Discussion

All five benchmark models achieve stable and strong performance on the test set (OA ≈ 0.9429–0.9492, F1 ≈ 0.8861–0.9007, IoU ≈ 0.8049–0.8266), indicating that this dataset provides clear supervision signals and supports stable training and generalization. The best-performing model, CDLamba, reaches OA = 0.9492, F1 = 0.9007, and IoU = 0.8266. The remaining models perform closely, suggesting that the dataset is not overly biased toward a specific architecture or inductive bias, and can serve as a general benchmarking resource for change detection methods.

From qualitative visualizations, models can effectively recover the main spatial patterns and boundary outlines of typical land-use changes. Errors mainly occur in challenging scenarios commonly seen in high-resolution oblique imagery, such as thin or fragmented structures, boundary transition regions, and appearance ambiguity caused by viewpoint variations, illumination/shadows, or texture similarity. These error patterns align with common difficulties in binary change detection tasks, further demonstrating that this dataset is suitable for evaluating boundary accuracy, reducing false alarms, and improving robustness.

---

## 5. Quick Start (Data Loading Example)

```python
import os
from PIL import Image

root = "UAV-OMVCD_BCD"
split = "train"
name = "sample_001.png"

img1 = Image.open(os.path.join(root, split, "A", name)).convert("RGB")
img2 = Image.open(os.path.join(root, split, "B", name)).convert("RGB")
mask = Image.open(os.path.join(root, split, "Label", name))  # single-channel {0,255}
```

---

## 6. Citation

If this dataset is helpful for your research, please cite our work (you may add the official BibTeX after the paper is published):

```bibtex
@dataset{UAV-OMVCD-BCD,
  title   = {UAV-OMVCD: UAV Oblique Multi-View Panoramic Change Detection Dataset (BCD Subset)},
  author  = {TODO},
  year    = {2026},
  doi     = {TODO},
  note    = {Train/Val/Test = 1735/225/221, 512x512 PNG, LEVIR-CD compatible format}
}
```

---

## 7. License and Disclaimer

* Dataset license: `TODO (Academic Use Only)`
* For research/teaching purposes only.

---

## 8. Contact

* Maintainer: Runtian Wang
* Email: [1025071815@njupt.com](mailto:1025071815@njupt.com)
* Issues and suggestions are welcome.

```
