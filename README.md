# ENUSP

## Generalizable Gesture Recognition Using Adversarial Regularization under Environment, User, and Position Variations

Dachuan Mei, Yongtao Ma, Bobo Wang, Chenglong Tian, and Lele Yin  
Tianjin University

ENUSP is a framework for millimeter-wave (mmWave) radar gesture recognition
across changes in environment, user, and position. It uses Dynamic Range-Angle
Image (DRAI) sequences to learn gesture representations under coupled domain
shifts.

## Method overview

ENUSP follows a **Purify-then-Align (PtA)** strategy with four components:

- **Local-global encoding.** A 3D-CNN captures local spatiotemporal patterns,
  while a lightweight Transformer models longer-range temporal dependencies.
- **Independent feature gating.** Separate gates weight the local and global
  feature streams before fusion.
- **Multi-output projection and cosine regularization.** A shared projection
  produces one classifier-facing output and three auxiliary outputs. A
  sample-wise absolute-cosine penalty reduces their directional overlap.
- **Axis-specific adversarial regularization.** Three gradient-reversal branches
  provide environment-, user-, and position-specific training signals. Adaptive
  linear balancing adjusts their relative contributions using EMA-smoothed
  domain losses.

The gesture classifier uses only the classifier-facing output at inference.
The auxiliary discriminators are used during training.

## Project updates

This page provides an overview of ENUSP. Additional project materials and code
access information will be added here.

## Contact

**Dachuan Mei**  
Email: [meidachuan@tju.edu.cn](mailto:meidachuan@tju.edu.cn)

## 中文简介

ENUSP 面向环境、用户和位置变化下的毫米波雷达手势识别，以动态距离-角度图序列为输入，
结合局部与全局时空编码、独立门控融合、共享多输出投影、逐样本余弦正则化和三路域对抗训练。
手势分类器在推理时仅使用面向分类的输出，辅助判别器用于训练阶段。

本页面先提供项目简介，后续将逐步补充相关材料和代码获取说明。
