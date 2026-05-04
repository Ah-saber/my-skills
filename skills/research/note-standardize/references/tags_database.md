# 标签数据库

此文件存储已规范化的标签，用于推荐新标签时参考和保持一致性。

## 使用说明

当为新论文推荐标签时：
1. 先在此表中查找语义相似的已存在标签
2. 如果找到相似标签，优先使用
3. 如果是全新标签类别，添加到此表

### 标签选择规则

**❌ 避免使用过于泛化的标签**：
- `Method/Learning/Deep` - 太泛，几乎所有现代方法都是深度学习
- `Method/Architecture/CNN` - 太泛，不提供有效分类信息
- `Method/Architecture/Transformer` - 除非架构本身是核心贡献

**✅ 优先使用具体、有区分度的标签**：
- 方法特点：`Method/Optimization/Regularization`、`Method/Frequency-Domain`
- 任务类型：`CV/Low-Level/Infrared/NUC`、`Task/Image-Restoration`
- 技术特征：`Method/Train-Free`、`Method/Physics-Inspired`

---

## 任务标签 (Task Tags)

### Low-Level / Infrared

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Low-Level/Infrared/NUC` | 非均匀校正、固定模式噪声去除 | FPN, Destriping, Fixed Pattern Noise, Non-Uniformity Correction |
| `CV/Low-Level/Infrared/Enhancement` | 红外图像增强 | Enhancement, Improve, Boost |
| `CV/Low-Level/Infrared/Dehazing` | 红外去雾 | Dehazing, Haze Removal, Defog |
| `CV/Low-Level/Infrared/Super-Resolution` | 红外超分辨率 | SR, Super Resolution, Upscaling, Enlarge |
| `CV/Low-Level/Infrared/Fusion` | 红外图像融合 | Fusion, Merge, Combine, Integrate |
| `CV/Low-Level/Infrared/Denoising` | 红外去噪 | Denoising, Noise Removal, Noise Reduction |
| `CV/Low-Level/Infrared/Deconvolution` | 去卷积 | Deconvolution, Deblur, Blur Removal |
| `CV/Low-Level/Infrared/Deblurring` | 红外运动去模糊 | Motion Deblurring, Blur Removal, Thermal Video Deblur |
| `CV/Low-Level/Infrared/Temperature-Estimation` | 红外温度估计 | Temperature Calibration, Thermography, Temperature Regression |
| `CV/Low-Level/Infrared/Turbulence-Mitigation` | 大气湍流消除 | Turbulence Mitigation, Atmospheric Turbulence, Geometric Distortion Correction |

### Video

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Video/Restoration` | 视频复原 | Video Restoration, Sequence Reconstruction, Video Enhancement |

### Low-Level / Visible

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Low-Level/Restoration` | 低层图像恢复 | Image Restoration, Degradation-Agnostic, All-in-One, Unified Restoration |
| `CV/Low-Level/Enhancement` | 可见光图像增强 | Enhancement, Brighten |
| `CV/Low-Level/Denoising` | 可见光去噪 | Denoising, Noise Removal |
| `CV/Low-Level/Super-Resolution` | 可见光超分辨率 | SR, Upscaling |

### Generation

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Generation/Image-Generation` | 图像生成 | Image Synthesis, Generate Images |
| `CV/Generation/Image-Editing` | 图像编辑 | Edit, Manipulate, Modify |
| `CV/Generation/Restoration` | 图像恢复 | Restoration, Recover, Repair |
| `CV/Generation/Video-Generation` | 视频生成 | Video Synthesis |
| `CV/Generation/Image-to-Image` | 图像到图像转换 | I2I, Image Translation |

### 3DV

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/3DV/Novel-View-Synthesis` | 新视角合成 | NVS, Neural Radiance Field, NeRF, 3D Gaussian Splatting, Novel View |

### Detection & Segmentation

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Detection/Object-Detection` | 目标检测 | Detect Objects, Bounding Box |
| `CV/Segmentation/Semantic-Segmentation` | 语义分割 | Pixel-wise Classification |
| `CV/Segmentation/Instance-Segmentation` | 实例分割 | Instance-wise |

---

## 方法标签 (Method Tags)

### Generation Methods

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Method/Generation/Diffusion` | 扩散模型 | DDPM, DDIM, Diffusion Model, Denoising Diffusion |
| `Method/Generation/Flow` | 流模型 | Flow-based, Rectified Flow, Normalizing Flow |
| `Method/Generation/GAN` | 生成对抗网络 | Generative Adversarial Network |
| `Method/Generation/AR` | 自回归生成模型 | Autoregressive, AR, Token-by-Token, Next-Token Prediction |
| `Method/Generation/VAE` | 变分自编码器 | Variational Autoencoder |

### Learning Methods

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Method/Learning/Self-Supervised` | 自监督学习 | Self-supervised, Unsupervised |
| `Method/Learning/Deep` | 深度学习 | Deep Learning, CNN, Neural Network |
| `Method/Learning/Weakly-Supervised` | 弱监督学习 | Weak Supervision |
| `Method/Learning/Few-Shot` | 少样本学习 | Few-shot, One-shot |

### Architecture

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Method/Architecture/Attention` | 注意力机制 | Attention, Self-Attention, Cross-Attention |
| `Method/Architecture/Transformer` | Transformer | ViT, Vision Transformer |
| `Method/Architecture/CNN` | 卷积神经网络 | ConvNet, Convolutional |
| `Method/Architecture/UNet` | U-Net 架构 | U-Net, Encoder-Decoder |

### Optimization

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Method/Optimization/Regularization` | 正则化优化 | L1, L2, Sparse, ADMM, IRLS |
| `Method/Optimization/Gradient` | 梯度方法 | Gradient Descent, SGD, Adam |
| `Method/Optimization/Adversarial` | 对抗训练 | Adversarial Training |

### Other Methods

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Method/Frequency-Domain` | 频域方法 | Fourier, DCT, Wavelet, Spectral |
| `Method/Physics-Inspired` | 物理启发 | Physics-based, Physics-informed |
| `Method/Filter` | 滤波方法 | Guided Filter, Bilateral Filter |
| `Method/Train-Free` | 无需训练 | Train-free, Training-free, Zero-shot |

---

## 数学/理论标签 (Math/Theory Tags)

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `Math/Sampling/Posterior-Sampling` | 后验采样 | Posterior Sampling, MCMC |
| `Math/Sampling/Langevin` | Langevin 采样 | Langevin Dynamics |
| `Math/Information/Entropy` | 熵相关 | Entropy, KL Divergence |
| `Math/Probability/Bayesian` | 贝叶斯方法 | Bayesian, Prior, Posterior |

---

## 发表场所标签 (Venue Tags)

| 标签 | 说明 |
|------|------|
| `Venue/CVPR` | CVPR 会议 |
| `Venue/ICCV` | ICCV 会议 |
| `Venue/ECCV` | ECCV 会议 |
| `Venue/NeurIPS` | NeurIPS 会议 |
| `Venue/ICML` | ICML 会议 |
| `Venue/AAAI` | AAAI 会议 |
| `Venue/IJCAI` | IJCAI 会议 |
| `Venue/TPAMI` | TPAMI 期刊 |
| `Venue/TIP` | TIP 期刊 |
| `Venue/TGRS` | IEEE TGRS 期刊 |

---

## 新标签记录区

当发现全新类型的标签时，添加到这里：

| 标签 | 说明 | 相似关键词 |
|------|------|-----------|
| `CV/Low-Level/low-light` | 低光照图像处理 | Low-light, Dark Image, Night Scene |
| `CV/Low-Level/Visable` | 可见光图像处理（通用） | Visible Light, RGB Image |
| `Method/Learning/Transfer` | 迁移学习/微调 | Transfer Learning, Fine-tuning, Adaptation |
| `Method/Learning/Fine-Tuning` | 微调策略 | Fine-tuning, Joint Optimization, Unified Training |
| `Method/Optimization/Sampling` | 采样优化 | Sampling, Inference Acceleration, Feature Caching |
| `Method/Architecture/Adapter` | 适配器架构 | Adapter, LoRA, Parameter-Efficient |
| `CV/Domain/Remote-Sensing` | 遥感图像处理领域 | Remote Sensing, Satellite Image, Aerial Image |
| `CV/Low-Level/Restoration/All-in-One` | 一体化图像恢复 | Universal Restoration, All-in-One, Multi-Task Restoration, Task-Decoupled |
| `Method/Learning/Multi-Modal` | 多模态方法 | Vision-Language, CLIP, Multi-Modal, VLM |
| `Math/PDE` | 偏微分方程 | PDE, Partial Differential Equation, Reaction-Diffusion, Differential Operator |
| `CV/Survey` | 计算机视觉综述论文 | Survey, Review, 综述 |
| `CV/Low-Level/Blur-Estimation` | 模糊核/PSF估计 | PSF Estimation, Blur Kernel Estimation, Point Spread Function, MTF, SFR |
| `Method/Learning/Reinforcement-Learning` | 强化学习 | RL, Reinforcement Learning, Policy Gradient, Actor-Critic, MDP |
| `Method/Learning/Distillation` | 蒸馏学习（通用） | Distillation, Knowledge Distillation, Model Compression, EMA Teacher |
| `Method/Architecture/NAS` | 神经架构搜索 | NAS, Neural Architecture Search, Architecture Search, Proxy Block |
| `Method/Optimization/Bayesian` | 贝叶斯优化 | Bayesian Optimization, GP, Gaussian Process, EHVI, Hyperparameter Search, Acquisition Function |
| `Method/Architecture/MoE` | 稀疏混合专家 | MoE, Mixture-of-Experts, Sparse Expert, Router, Top-K Routing |
| `Method/Learning/Post-Training` | 后训练方法 | Post-Training, Post-training Fine-tuning, Repurposing, One-step Conversion |
