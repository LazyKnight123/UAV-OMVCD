# UAV-OMVCD

**UAV-OMVCD (Unmanned Aerial Vehicle–Oblique Multi-View Panoramic Change Detection Dataset)**
无人机倾斜多视角全景语义变化检测数据集，用于 **语义变化检测（Semantic Change Detection, SCD）** 与 **变化检测（Change Detection, CD）** 等任务。

## 1. 简介

为建立一个**样本数量充足、类别覆盖合理、像素级标注规范**的语义变化检测（SCD）基准，我们构建了 **UAV-OMVCD**。数据采集于合肥、唐山、南京等典型区域的多期次无人机倾斜全景影像，重点覆盖与城市场景密切相关的关键地物变化（如硬化地表、工程机械、建筑物、温室大棚、堆土等），系统记录其在不同时间节点下的**结构增减、形态演变、缺陷演化**等变化特征。

与传统正射影像的垂直视角不同，UAV-OMVCD 采用**多角度倾斜观测**，可覆盖地物侧立面与部分隐蔽区域，从而提升复杂城市场景下变化识别的有效性，并降低季节性伪变化带来的干扰。

## 2. 数据构建与标注说明

* **切片大小**：每张图像为 `512 × 512`
* **筛选策略**：从人工标绘的多分类变化样本中筛选 **变化区域占比 > 1%** 的图像对，以保证训练样本包含足够变化信息，降低极端稀疏变化导致的训练不稳定。
* **标注粒度**：像素级精确标注
* **标注流程**：由遥感图像处理领域专家团队完成，并经过严格质检与复核流程，以确保标签准确性。

## 3. 类别体系（10类）

本数据集关注 10 个主要土地覆盖类别（类别 ID 为 `0–9`）：

| ID | 类别名称                             |
| -: | -------------------------------- |
|  0 | 背景（Background）                   |
|  1 | 建筑（Building）                     |
|  2 | 水域（Water）                        |
|  3 | 硬化地表（Paved/Impervious Surface）   |
|  4 | 绿地（Greenland/Vegetation）         |
|  5 | 推堆土（Earthwork/Soil Pile）         |
|  6 | 耕地（Farmland）                     |
|  7 | 构筑物（Structures / Infrastructure） |
|  8 | 养殖（Aquaculture）                  |
|  9 | 其他（Other）                        |

**类别解释补充：**

* **硬化地表**：不透水地面与人工铺装区域
* **绿地**：公园、草坪等植被覆盖区域
* **推堆土**：施工场地与土方工程区域
* **构筑物**：桥梁、道路、码头等基础设施
* **养殖**：水产养殖池塘等区域

> 10 个语义类别在两期（T1/T2）的组合可形成 **100 种变化类别组合（包含无变化）**。本版本通过阈值筛选策略使数据更符合“变化发生时的真实分布”，同时保证任务的可训练性。

## 4. 数据组织结构

`UAV-OMVCD` 采用层次化目录结构，划分为 `train / val / test`，比例为 **7 : 2 : 1**。每个 split 下包含五类文件夹，分别对应 SCD 所需的输入与监督：

* `T1/`：时相 T1 原始影像
* `T2/`：时相 T2 原始影像
* `GT_T1/`：T1 语义标签（单通道，像素值 0–9）
* `GT_T2/`：T2 语义标签（单通道，像素值 0–9）
* `GT_CD/`：变化标签（单通道二值掩膜，0=未变化，非0=变化）

目录示意：

```text
UAV-OMVCD/
├── train/
│   ├── T1/
│   ├── T2/
│   ├── GT_T1/
│   ├── GT_T2/
│   └── GT_CD/
├── val/
│   ├── T1/
│   ├── T2/
│   ├── GT_T1/
│   ├── GT_T2/
│   └── GT_CD/
├── test/
│   ├── T1/
│   ├── T2/
│   ├── GT_T1/
│   ├── GT_T2/
│   └── GT_CD/
├── train.txt
├── val.txt
└── test.txt
```

### 样本命名规则

同一样本的五个文件**同名**，分别位于对应目录。例如 `sample_001.png`：

```text
train/T1/sample_001.png
train/T2/sample_001.png
train/GT_T1/sample_001.png
train/GT_T2/sample_001.png
train/GT_CD/sample_001.png
```

同时，根目录提供：

* `train.txt` / `val.txt` / `test.txt`：列出 split 中所有样本文件名（不含路径），便于程序化读取与复现实验划分。

## 5. 任务定义（推荐用法）

* **CD（变化检测）**：输入 `(T1, T2)`，监督 `GT_CD`（二值）
* **T1 语义分割**：输入 `T1`，监督 `GT_T1`
* **T2 语义分割**：输入 `T2`，监督 `GT_T2`
* **SCD（语义变化检测）**：联合利用 `GT_T1`、`GT_T2` 与 `GT_CD`，评估“哪里变化 + 变成什么/从什么变来”的语义变化能力

## 6. 数据集获取

下载地址如下（建议在论文/项目中标注数据集版本号 `UAV-OMVCD`）：

```text
Baidu Netdisk:
https://pan.baidu.com/s/1BVIe4I40zSQPeJR630HZ6g?pwd=DATA
Code: DATA

Google Drive:
https://drive.google.com/file/d/1ynDMNmIa-XB6_oG9hFaf1vTpoWUi50AR/view?usp=drive_link
https://drive.google.com/file/d/1mjX2lSGRINDa4TV3Q58tJv_HeyqdV7hO/view?usp=drive_link
```

## 7. 引用（Citation）

如果该数据集对你的研究有帮助，请引用我们的工作（BibTeX 可在论文确定后替换）：

```bibtex
@dataset{UAV-OMVCD,
  title   = {UAV-OMVCD: Unmanned Aerial Vehicle–Oblique Multi-View Panoramic Semantic Change Detection Dataset},
  author  = {TODO},
  year    = {2026},
  note    = {Version: SCD_512_Single_1Percent}
}
```

## 8. 许可与声明（License）


## 9. 联系方式（Contact）

* 维护者：Runtian Wang
* Email：1025071815@njupt.edu.cn
* Issues：欢迎在本仓库提交问题与建议