# S<sup>2</sup>DRNet: Spatial-Spectral Differential Rectification with Coupling Suppression for Clustered Infrared Small Target Detection

<div align="center">

**Peng Wang<sup>1</sup>, Ziling Lu<sup>1</sup>, Hankiz Yilahun<sup>1</sup>, Jihong Zhu<sup>2</sup>, and Askar Hamdulla<sup>1</sup>**

<sup>1</sup>School of Computer Science and Technology, Xinjiang University
<sup>2</sup>Department of Precision Instrument, Tsinghua University

[![Paper](https://img.shields.io/badge/Paper-PDF-red)](assets/paper.pdf)
[![Dataset](https://img.shields.io/badge/Dataset-Coming%20Soon-blue)](#datasets)
[![Code](https://img.shields.io/badge/Code-Coming%20Soon-black)](#how-to-use)
[![Project Page](https://img.shields.io/badge/Project-Page-green)](index.html)

</div>

---

## News

* **The project page is available.**
* **Code, trained models, and dataset links will be released after the paper/public repository is ready.**

If this repository is helpful to your research, please consider giving it a star.⭐⭐⭐

---

## Overview

Clustered infrared small target detection (CIRSTD) aims to distinguish densely adjacent small targets under low signal-to-noise ratios, weak textures, and complex background interference. Compared with conventional sparse infrared small target detection, clustered scenarios are more prone to response coupling, target adhesion, missed detection, and background false alarms.

To address these challenges, we propose **S<sup>2</sup>DRNet**, a **Spatial-Spectral Differential Rectification Network** for clustered infrared small target detection. S<sup>2</sup>DRNet improves clustered-target discrimination from four complementary perspectives:

1. **Spatial aggregation** for suppressing neighboring-target response diffusion.
2. **Spectral decoupling** for separating target details, background structures, and noise disturbances.
3. **Hybrid-domain feature rectification** for balancing spatial semantic features and frequency-domain structural priors.
4. **Boundary-gap mining supervision** for strengthening target-separation regions during optimization.

<p align="center">
  <img src="imgs/introduction.jpg" alt="Motivation and overview of S2DRNet" width="350"/>
</p>

<div align="center">
  <b>Motivation and overview.</b> Clustered targets suffer from target adhesion, missed detection, and false alarms. S<sup>2</sup>DRNet addresses these issues through spatial aggregation, spectral decoupling, and cross-domain rectification.
</div>

---

## Motivation

CIRSTD should not be regarded as a simple extension of sparse IRSTD. In clustered scenes, the detector must perform fine-grained target-background discrimination under stronger inter-target coupling and more severe background interference.

The key challenges are summarized as follows:

* **Target-response coupling.** Dense and adjacent infrared small targets tend to generate mutually diffused responses, which causes target adhesion and weakens individual target separation.
* **Spectral interference aliasing.** Target details, background edges, and high-frequency noise are easily mixed in complex infrared scenes, producing missed detections and false alarms.
* **Cross-domain feature imbalance.** Directly fusing spatial semantic features and frequency-domain structural priors can introduce redundant responses and unbalanced feature contributions.
* **Insufficient boundary supervision.** Narrow gap regions between adjacent targets occupy very few pixels, so conventional segmentation losses provide limited supervision for target-separation boundaries.

---

## Overall Architecture

<p align="center">
  <img src="imgs/TGRS-model.jpg" alt="Overall architecture of S2DRNet" width="900"/>
</p>

<div align="center">
  <b>Overall architecture of S<sup>2</sup>DRNet.</b> The framework performs parallel spatial-spectral feature extraction and hybrid-domain feature rectification for clustered infrared small target detection.
</div>


---

## Method

### MDAM: Multi-granularity Dynamic Aggregation Module

MDAM enhances clustered-target spatial representation through two complementary dynamic aggregation strategies:

* **Square dynamic aggregation** for local response enhancement.
* **Cross-strip dynamic aggregation** for directional context modeling and adjacent-target coupling suppression.

### SDRM: Spectral Differential Representation Module

SDRM performs wavelet-based structure-detail decomposition and multi-directional differential refinement. It decomposes features into low-frequency structural components and high-frequency details, then refines target details while suppressing edge-noise interference.

<p align="center">
  <img src="imgs/MDAM+SDRM.jpg" alt="Detailed structures of MDAM and SDRM" width="900"/>
</p>

<div align="center">
  <b>Spatial and spectral representation.</b> MDAM models local and cross-strip neighborhoods, while SDRM performs spectral decomposition and differential refinement.
</div>

### HDFR: Hybrid-Domain Feature Rectification Module

<p align="center">
  <img src="imgs/TGRS-HDFR1.jpg" alt="Detailed structure of HDFR" width="900"/>
</p>

<div align="center">
  <b>Hybrid-domain feature rectification.</b> HDFR adaptively rectifies spatial semantic features and frequency-domain priors through axial-channel adaptive modulation and branch-wise competitive weighting.
</div>

### CBML: Coupling-suppressed Boundary Mining Loss

CBML strengthens the optimization of target-separation regions by emphasizing boundary and narrow-gap pixels between adjacent targets. This supervision helps reduce target adhesion, boundary ambiguity, and response coupling in clustered infrared scenes.

---

## Datasets

The experiments are conducted on three public infrared small target detection datasets:

* **DenseSIRST** [[Original dataset](https://github.com/YimianDai/sirst)] 
* **SIRST3** [[Original dataset](https://github.com/XinyiYing/LESPS)]
* **IRSTD-1k** [[Original dataset](https://drive.google.com/file/d/1JoGDGF96v4CncKZprDnoIor0k1opaLZa/view)]


Please organize the datasets according to the dataloader settings in this repository. A typical directory structure can be arranged as follows:

```text
datasets/
├── DenseSIRST/
│   ├── images/
│   ├── masks/
│   └── splits/
├── SIRST3/
│   ├── images/
│   ├── masks/
│   └── splits/
└── IRSTD-1K/
    ├── images/
    ├── masks/
    └── splits/
```



---

## Environment

Create a conda environment:

```bash
conda create -n S2DRNet python=3.11
conda activate S2DRNet
```

Install the required packages:

```bash
pip install torch==2.3.0 torchvision==0.18.0 torchaudio==2.3.0 --index-url https://download.pytorch.org/whl/cu121
pip install scikit-image
pip install PyYAML
pip install tensorboardX
pip install pytest
pip install einops
pip install timm
pip install scikit-learn
pip install opencv-python
pip install ml-collections
pip install thop
pip install tensorboard
pip install matplotlib
pip install albumentations
pip install torchsummary
pip install fvcore
pip install PyWavelets
```

If your CUDA version is fixed, install the PyTorch version that matches your local CUDA toolkit. For example:

```bash
pip install torch==1.13.1+cu116 torchvision==0.14.1+cu116 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu116
```

---

## How to Use

### 1. Data Preparation

Download the required datasets and place them under the `dataset/` directory. Then check the dataset path and split settings in the corresponding configuration or training file.

### 2. Training

```bash
python train.py
python train.py   --model S2DRNet   --dataset DenseSIRST   --dataset_type mask   --cuda_devices 0   --epochs 800   --lr 1e-3   --seed 42   --edge_weight 3   --hard_ratio 0.5
```

For training on a specific dataset, modify the dataset name, data path, and training configuration in the training script or config file.

### 3. Testing

```bash
python test.py   --model S2DRNet   --dataset DenseSIRST   --dataset_type mask  
```

Before testing, please set the path of the trained model weights in the testing script or config file.

---

## Results and Trained Models


### Performance-Efficiency Analysis

<p align="center">
  <img src="imgs/pf.png" alt="Performance-efficiency comparison" width="350"/>
  <img src="imgs/radar.png" alt="Radar evaluation" width="206"/>
</p>

<div align="center">
  <b>Performance-efficiency comparison.</b> S<sup>2</sup>DRNet maintains a favorable balance between detection accuracy and deployment efficiency.
</div>

S<sup>2</sup>DRNet contains **0.85M parameters** and **6.09G FLOPs**.

### ROC Curves

<p align="center">
  <img src="imgs/roc.jpg" alt="ROC curves" width="900"/>
</p>

<div align="center">
  <b>ROC comparison.</b> The proposed method maintains stable detection capability under strict false-alarm constraints.
</div>

### Visual Results

<p align="center">
  <img src="imgs/vision_1.jpg" alt="Visual comparison" width="900"/>
</p>

<div align="center">
  <b>Visual comparison.</b> S<sup>2</sup>DRNet separates adjacent targets more clearly and suppresses background interference more effectively.
</div>

### Response Distribution Analysis

<p align="center">
  <img src="imgs/3dmesh.jpg" alt="3D response distributions" width="900"/>
</p>

<div align="center">
  <b>Three-dimensional response distributions.</b> Sharper and cleaner response peaks indicate more accurate target localization and stronger background suppression.
</div>

---


## Contact

For questions about S<sup>2</sup>DRNet, please open an issue in this repository or contact the authors by email after the official repository is released.
