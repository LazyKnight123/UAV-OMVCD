下面给你一份**可直接用于 GitHub 仓库首页**的 `README.md`（面向你当前的 **BCD（二分类变化检测）版本**，格式对标 LEVIR-CD 风格，信息完整、可复现、便于他人直接跑通数据加载与基准实验）。你只需要把其中 `DOI`、`License`、`Citation` 里 `TODO` 的位置按你的实际信息替换即可。

````markdown
# UAV-OMVCD (BCD)

**UAV-OMVCD**：无人机倾斜多视角全景变化检测数据集（Binary Change Detection, BCD）。  
本仓库发布 UAV-OMVCD 的二分类变化检测版本，面向中国典型区域的用地变化检测研究与模型开发。

- **Task**: Binary Change Detection (BCD)
- **Image size**: 512 × 512 (PNG)
- **Splits**: train / val / test（固定划分，可复现实验）
- **Download**: Baidu Netdisk & Google Drive（见下文）
- **DOI**: TODO（开放科学数据平台 DOI 链接）

---

## 1. Dataset Overview

UAV-OMVCD 由研究团队对中国境内典型区域的无人机倾斜全景影像进行人工标注构建。数据集中每个样本由两期（或两视角）高分辨率影像对组成，并提供像素级二分类变化掩膜，用于监督训练与标准化评测。

该数据集组织格式参照 **LEVIR-CD** 的层次化结构，能够直接适配多种主流变化检测/语义分割框架的数据加载流程（如 BIT、ChangeMamba、CDLamba、SARASNet、CDXLSTM 等）。

---

## 2. Data Format & Directory Structure

数据集根目录包含 `train/`, `val/`, `test/` 三个子集，每个子集目录下包含一致的三类文件夹：

- `A/`：前时相/视角影像（PNG，512×512）
- `B/`：后时相/视角影像（PNG，512×512，与 A 按文件名一一对应）
- `Label/`：二分类变化掩膜（PNG，单通道灰度图，与 A/B 同名）

同时：
- 每个子集中提供 `img.txt`：该子集全部样本文件名列表（不含路径），便于快速索引与批量读取；
- 根目录提供 `list/` 文件夹保存划分信息：`train.txt`, `val.txt`, `test.txt` 分别给出三子集的文件名清单，用于**可复现划分协议**。

目录示意如下：

```text
UAV-OMVCD_BCD/
├── train/
│   ├── A/
│   ├── B/
│   ├── Label/
│   └── img.txt
├── val/
│   ├── A/
│   ├── B/
│   ├── Label/
│   └── img.txt
├── test/
│   ├── A/
│   ├── B/
│   ├── Label/
│   └── img.txt
└── list/
    ├── train.txt
    ├── val.txt
    └── test.txt
````

### 2.1 Sample Pairing

对任一文件名 `xxx.png`，对应三者为：

```text
split/A/xxx.png
split/B/xxx.png
split/Label/xxx.png
```

### 2.2 Label Definition (Binary Mask)

`Label` 为单通道灰度掩膜，像素值含义为：

* `0`：未变化（No-change）
* `255`：变化（Change）

> 注：如你在训练代码中使用 `0/1`，可在读取时将 `255` 映射为 `1`。

---

## 3. Data Splits & Statistics

本版本采用固定划分，确保训练、验证与测试相互独立：

* **train**: 1735 pairs
* **val**: 225 pairs
* **test**: 221 pairs

数据集总计：**2181** 组影像对（A/B/Label 为一个样本单元）。

---

## 4. Benchmark Experiments (120 epochs)

为验证数据集可用性与难度水平，我们在统一软硬件环境与训练协议下，选取 5 个代表性的二分类变化检测模型进行对比实验：

* **BIT**
* **ChangeMamba**
* **CDLamba**
* **SARASNet**
* **CDXLSTM**

### 4.1 Experimental Setup

* **GPU**: NVIDIA RTX 4090 (24GB)
* **CPU**: Intel Xeon Platinum 8352V
* **RAM**: 120GB
* **Storage**: 2TB
* **OS**: Ubuntu 20.04
* **Python**: 3.8
* **CUDA**: 11.8
* **PyTorch**: 2.0.1 (cu118)

Training protocol:

* Train **120 epochs**
* Select the checkpoint with the best **F1-score** on validation during evaluation, and report results on test set.

Metrics:

* OA, Precision, Recall, F1-score, IoU

### 4.2 Test Results

| Model       |         OA |  Precision |     Recall |   F1-score |        IoU |
| ----------- | ---------: | ---------: | ---------: | ---------: | ---------: |
| BIT         |     0.9457 |     0.9071 |     0.8803 |     0.8930 |     0.8150 |
| ChangeMamba |     0.9429 |     0.9027 |     0.8729 |     0.8869 |     0.8060 |
| **CDLamba** | **0.9492** | **0.9107** | **0.8914** | **0.9007** | **0.8266** |
| SARASNet    |     0.9429 |     0.9050 |     0.8698 |     0.8861 |     0.8049 |
| CDXLSTM     |     0.9487 |     0.9139 |     0.8851 |     0.8987 |     0.8236 |

整体上，各模型在测试集上表现稳定且接近（OA≈0.9429–0.9492；F1≈0.8861–0.9007；IoU≈0.8049–0.8266），说明 UAV-OMVCD_BCD 提供了清晰有效的监督信号，并能支持稳定训练与泛化。误差主要集中在高分辨率倾斜影像常见困难情形：细窄/碎片化结构、边界过渡带，以及视角差异、光照/阴影变化、纹理相似导致的外观歧义区域。

---

## 5. Download

我们提供两种下载方式（建议使用固定划分的 `list/` 文件以确保可复现实验）：

**Baidu Netdisk**
[https://pan.baidu.com/s/1BVIe4I40zSQPeJR630HZ6g?pwd=DATA](https://pan.baidu.com/s/1BVIe4I40zSQPeJR630HZ6g?pwd=DATA)
Code: DATA

**Google Drive**
[https://drive.google.com/file/d/1ynDMNmIa-XB6_oG9hFaf1vTpoWUi50AR/view?usp=drive_link](https://drive.google.com/file/d/1ynDMNmIa-XB6_oG9hFaf1vTpoWUi50AR/view?usp=drive_link)
[https://drive.google.com/file/d/1mjX2lSGRINDa4TV3Q58tJv_HeyqdV7hO/view?usp=drive_link](https://drive.google.com/file/d/1mjX2lSGRINDa4TV3Q58tJv_HeyqdV7hO/view?usp=drive_link)

**DOI (Open Science Platform)**
TODO: 在此处添加 DOI 链接（推荐同时给出版本号/发布日期）

---

## 6. Citation

如果本数据集对你的研究有帮助，请引用（将作者、年份、DOI 按实际情况替换）：

```bibtex
@dataset{UAV-OMVCD_BCD,
  title   = {UAV-OMVCD: Oblique Multi-View Panoramic UAV Dataset for Binary Change Detection},
  author  = {TODO},
  year    = {2026},
  doi     = {TODO},
  note    = {Binary Change Detection (512x512), fixed train/val/test split}
}
```

---

## 7. License

* Dataset License: **TODO**（建议：CC BY 4.0 / CC BY-NC 4.0 / Academic Use Only）
* 如包含第三方底图/敏感区域，请在此处补充合规声明与使用限制。

---

## 8. Contact

* Maintainer: TODO
* Email: TODO
* Issues: 欢迎在本仓库提交问题与建议

```

---

如果你希望 README 更“顶会风格”，我建议再补两块（我也可以直接帮你写好）：

1) **Quick Start (Data Loader)**：给一段最小可用的 Python 读取示例（读取 A/B/Label，处理 0/255→0/1）  
2) **Qualitative Visualization**：放一张 `image1 | image2 | mask` 的示例拼图（你论文里“如图所示”的那张），让别人一眼知道数据是什么样子

你把 **DOI 链接**、**License 想用哪种**、以及是否要加 **示例图文件名/路径**（比如 `assets/preview.png`）告诉我，我就把 README 进一步“最终定稿版”给你。
```
