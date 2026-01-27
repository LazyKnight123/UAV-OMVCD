
# UAV-OMVCD (BCD Subset)

**UAV-OMVCD**：无人机倾斜多视角全景变化检测数据集  
**本仓库发布内容**：UAV-OMVCD 的 **BCD（二分类变化检测）** 子集（对标 Levir-CD 组织格式），面向中国典型区域用地变化检测研究与模型开发。

- 图像格式：PNG  
- 分辨率：512 × 512  
- 数据划分：Train / Val / Test = 1735 / 225 / 221（固定划分，可复现实验）  
- 参考格式：Levir-CD（train/val/test + A/B/Label + list）

---

## 1. 数据集简介

UAV-OMVCD 基于多期次无人机倾斜全景影像构建，采集区域覆盖合肥、唐山、南京等典型城市场景。相较传统正射影像的垂直视角，本数据集采用**多角度倾斜观测**，更有效覆盖地物侧立面及部分隐蔽区域，从而提升复杂城市场景下变化区域的可辨识性，并降低季节性伪变化干扰。

为确保监督信号清晰且训练稳定，我们从人工标绘样本中筛选 **变化区域占比 > 1%** 的图像对进入本 BCD 子集，从而避免变化区域极端稀疏导致的训练不稳定与评估偏差。

---

## 2. 数据集获取

- 下载地址（镜像）：

```text
Baidu Netdisk:
https://pan.baidu.com/s/1BVIe4I40zSQPeJR630HZ6g?pwd=DATA
Code: DATA

Google Drive:
https://drive.google.com/file/d/1ynDMNmIa-XB6_oG9hFaf1vTpoWUi50AR/view?usp=drive_link
https://drive.google.com/file/d/1mjX2lSGRINDa4TV3Q58tJv_HeyqdV7hO/view?usp=drive_link
````

---

## 3. 数据格式与组织结构（Levir-CD Compatible）

数据集根目录包含 `train/ val/ test/` 三个子集，分别用于训练、验证与最终测试评估。每个子集均包含一致的子目录结构：

* `A/`：第一时相（image1）原始影像（PNG，512×512）
* `B/`：第二时相（image2）原始影像（PNG，512×512，与 A 按文件名一一对应）
* `Label/`：像素级变化掩膜（PNG，单通道二值）
* `img.txt`：记录该子集全部样本文件名列表（不含路径）

同时根目录还包含 `list/` 文件夹保存固定划分协议：

* `list/train.txt`
* `list/val.txt`
* `list/test.txt`

其中 Train/Val/Test 的样本数量分别为 **1735 / 225 / 221**，确保不同研究者在相同划分下进行可复现对比实验。

### 3.2 样本对应关系

每个样本由同名文件组成，例如 `sample_001.png`：

```text
train/A/sample_001.png
train/B/sample_001.png
train/Label/sample_001.png
```
> ![图片1.png](picture/%E5%9B%BE%E7%89%871.png)
> 第 1 列 = image1（A），第 2 列 = image2（B），第 3 列 = mask（Label）。

### 3.3 Label 编码约定

* `Label` 为单通道二值掩膜图，像素值集合为 `{0, 255}`
* **默认约定**：`0 = 未变化`，`255 = 变化`

> 注：若你在发布版本中采用相反编码，请在此处显式说明，并在代码示例中同步修改。

---

## 4. Benchmark：模型训练与精度评价（BCD）

为验证数据集的可用性与训练稳定性，我们在统一实验设置下选取 5 种代表性的变化检测模型进行标准化对比实验：

* **BIT**
* **ChangeMamba**
* **CDLamba**
* **SARASNet**
* **CDXLSTM**

### 4.1 实验设置（统一）

* 数据划分：Train / Val / Test = 1735 / 225 / 221（互不重叠）
* 训练轮次：120 epochs
* 模型选择：训练过程中以 **F1** 最优的 checkpoint 进行测试集推理
* 评价指标：OA、Precision、Recall、F1-score、IoU（像素级分割指标）

**软硬件环境：**

* GPU：NVIDIA RTX 4090 (24GB) × 1
* CPU：Intel Xeon Platinum 8352V
* RAM：120 GB
* Disk：2 TB
* OS：Ubuntu 20.04
* Python：3.8
* CUDA：11.8
* PyTorch：2.0.1 (cu118)

### 4.2 测试集结果（Table 3）
本数据集各模型上的训练结果
![图片2.png](picture/%E5%9B%BE%E7%89%872.png)
| Model       | OA         | Precision  | Recall     | F1-score   | IoU        |
| ----------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| BIT         | 0.9457     | 0.9071     | 0.8803     | 0.8930     | 0.8150     |
| ChangeMamba | 0.9429     | 0.9027     | 0.8729     | 0.8869     | 0.8060     |
| **CDLamba** | **0.9492** | **0.9107** | **0.8914** | **0.9007** | **0.8266** |
| SARASNet    | 0.9429     | 0.9050     | 0.8698     | 0.8861     | 0.8049     |
| CDXLSTM     | 0.9487     | 0.9139     | 0.8851     | 0.8987     | 0.8236     |

### 4.3 结果讨论（简述）

五个基准模型在测试集上均取得稳定且较高的性能（OA≈0.9429–0.9492，F1≈0.8861–0.9007，IoU≈0.8049–0.8266），表明本数据集能够提供清晰有效的监督信号，并支持稳定训练与泛化。最优模型 CDLamba 达到 OA=0.9492、F1=0.9007、IoU=0.8266，其余模型表现接近，说明数据集并未过度偏向某一特定网络结构或归纳偏置，可作为变化检测方法的通用评测资源。

从可视化定性结果看，模型能够较好恢复多种典型用地变化的主要空间格局与边界轮廓；误差主要集中在高分辨率倾斜影像中普遍存在的困难情形，例如细窄/碎片化结构、边界过渡带，以及受视角差异、光照阴影或纹理相似性影响产生的外观歧义区域。这些误差形态与二分类变化检测任务的典型难点一致，也进一步证明本数据集适合用于评估边界精度、降低误报并提升鲁棒性的算法研究。

---

## 5. 快速开始（数据读取示例）

> 以下为最简索引示例（可按你的框架改写）：

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

## 6. 引用（Citation）

如果本数据集对你的研究有帮助，请引用我们的工作（发布论文后建议补充正式 BibTeX）：

```bibtex
@dataset{UAV-OMVCD-BCD,
  title   = {UAV-OMVCD: UAV Oblique Multi-View Panoramic Change Detection Dataset (BCD Subset)},
  author  = {TODO},
  year    = {2026},
  doi     = {TODO},
  note    = {Train/Val/Test = 1735/225/221, 512x512 PNG, Levir-CD compatible format}
}
```

---

## 7. 许可与声明（License）

* 数据集许可：`TODO（建议明确：CC BY 4.0 / CC BY-NC 4.0 / Academic Use Only 等）`
* 仅限科研/教学用途（如适用，请补充具体限制条款）。

---

## 8. 联系方式（Contact）

* Maintainer：TODO
* Email：TODO
* Issue：欢迎在本仓库提交问题与建议

```

---

如果你把 **DOI 链接、许可类型、作者/单位** 这三项补全，我也可以顺手帮你把 README 里的 `TODO` 全部替换成最终版本，并给你一段更“论文风格”的 **数据集贡献点（Contributions）** 段落（更适合放在 GitHub 首页 + 论文数据集章节里）。
```
