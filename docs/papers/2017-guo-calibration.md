---
tags:
  - paper-note
  - calibration
  - evaluation
---

# On Calibration of Modern Neural Networks

## 文献信息

- 作者：Chuan Guo, Geoff Pleiss, Yu Sun, Kilian Q. Weinberger。
- 年份：2017。
- 发表：ICML，PMLR 70，1321–1330。
- 原始来源：[官方页面](https://proceedings.mlr.press/v70/guo17a.html)。
- 预印本：[arXiv:1706.04599](https://arxiv.org/abs/1706.04599)；方法与实验叙述仍按本页注明的正式来源。


## 研究问题与方法

[Guo et al. (2017)](https://proceedings.mlr.press/v70/guo17a.html "文献引用")研究分类神经网络的置信度校准（Confidence Calibration）：softmax 最大概率与预测正确频率是否一致。论文比较不同模型的校准表现，并检验能否在训练完成后，用一组独立验证数据修正概率输出。

核心方法是温度缩放（Temperature Scaling）。作者冻结分类器，只在验证集上拟合一个正温度参数，以最小化负对数似然（Negative Log-Likelihood，NLL）。它统一缩放每个样本的 logits，再计算 softmax；不会改变该样本的最大类别及分类准确率。

具体流程是：保存验证集 logits 和标签，拟合温度，冻结温度，再评价独立测试集。对照方法包括分箱、保序回归（Isotonic Regression）、贝叶斯分箱以及向量和矩阵缩放。标量温度的优势在于参数少，但也限制了它可以修正的误差形态。

## 实验设置与主要发现

图像实验涉及 CIFAR、ImageNet、Birds、Cars、SVHN 等数据及不同深度的分类网络；文本实验涉及 20 News、Reuters、SST，以及对应文本分类模型。论文以可靠性图、期望校准误差（Expected Calibration Error，ECE）等评价概率与正确率的差异。具体数据和模型组合见原文实验表，不能把所有数据集理解为使用相同架构。

作者报告，若干较新的深层网络比早期模型更不校准；温度缩放在多种设置下明显改善校准，且常接近或优于更复杂的校准器。但它并非每组实验的最优方法，原本较好校准的 Reuters 设置也未因此获得改善。高维矩阵缩放在部分设置中难以拟合或容易过拟合。

论文讨论容量、批归一化和权重衰减与校准的关系。这些实验支持相关现象及具体消融结论，作者没有完成对“现代网络为什么失校准”的一般因果识别。

## 优势与局限

作者的贡献是把分类准确率和概率可靠性明确分开，并给出成本低、容易复现的校准基线。

本笔记的理解是，部署该方法前要先明确验证集代表什么分布。论文主要处于训练、验证与测试分布相近的分类设置，不能据此保证分布变化后的校准。保持类别 argmax 也不意味着多分类样本之间的置信度排序一定不变。

温度缩放无法修正事实内容，也没有解决开放生成中“哪个事件的概率应被校准”。将它用于 LLM 时，必须另行定义答案正确事件、概率输入与监督标签。

## 与博士研究主线的关系

这篇论文适合作为概率校准实验的基础基线和报告规范来源。对新的 UQ 信号，应分别检查错误排序和校准；只证明 ECE 降低，不能推出幻觉检测能力提高。通用公式与校准条件见下列知识页。

## 相关笔记

- [校准方法](../calibration/calibration-methods.md)
- [后处理校准](../calibration/post-hoc-calibration.md)
- [ECE](../evaluation/calibration-metrics/expected-calibration-error.md)

## 参考文献

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [原文](https://proceedings.mlr.press/v70/guo17a.html)
