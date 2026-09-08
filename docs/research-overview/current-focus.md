---
tags:
  - research-overview
  - uq
  - calibration
  - efficiency
  - representation
  - evidence
---

# 当前研究重点（Current Focus）

当前主要研究不确定性量化（Uncertainty Quantification，UQ）如何支持幻觉（Hallucination）与错误检测。初期以大语言模型（Large Language Model，LLM）为方法研究起点，后续可以扩展到大型视觉语言模型（Large Vision-Language Model，LVLM）及医疗应用。现阶段重点是形成清楚、可检验的问题。

## 当前核心问题

当前关注的不确定性估计（Uncertainty Estimation），是从生成输出、概率信息、内部表征或证据中构造信号，并检验这些信号是否有助于判断输出的可靠性。核心问题是：在什么条件下，较高的不确定性确实对应较高的错误风险？

[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0) 利用语义熵（Semantic Entropy）检测随生成变化而出现的部分编造性错误，其研究对象是幻觉中的一个子集。这个结果为当前方向提供了技术背景，但可识别的错误范围仍需在具体任务中检验。

## 主要技术兴趣

### 不确定性信号

比较 token 或序列概率、多次 sampling 后的回答一致性、语义层面的差异，以及模型自报的置信度（Confidence）。重点分析不同信号对哪些错误有用，哪些情况下会失效，并检查回答正确性与证据支持情况。

### 置信度校准（Confidence Calibration）

[Guo et al. (2017)](https://proceedings.mlr.press/v70/guo17a.html) 将校准表述为置信度与正确概率之间的匹配问题。例如，在数量足够且条件可比的一组预测中，置信度接近 80% 的预测，其正确比例也应接近 80%。

当前计划将这一目标用于定义清楚的回答正确性判断，同时考察错误检测与校准。先比较已有方法的行为，再判断是否需要研究新的校准方式。

### 高效 UQ（Efficient UQ）

关注单次生成、减少 sampling 等路线能否保留有用信号。比较时记录 inference 时间、模型调用次数与生成 token 数量，并计入额外核查或特征处理的成本，分析效果与代价之间的关系。

### 基于表征的 UQ（Representation-based UQ）

探索 hidden state 是否包含输出概率之外的可靠性信息。关注这些信息能否帮助识别错误，以及它们对模型访问条件和实验设置有什么要求。表征信号的具体构造方式仍待收敛。

### 外部与多模态证据

探索外部证据（External Evidence）与多模态证据（Multimodal Evidence）能否补充模型自身的信号。后续关注视觉定位（Visual Grounding）等方向，研究回答与图像之间的关系能否帮助识别缺乏依据的内容，并检查证据的质量与使用成本。

## 当前研究方式

初期计划优先选择能够读取 token 概率和 hidden state 的开放权重纯文本 LLM，在尽量一致的数据与评估设置下比较代表性方法。任务选择需要有可用的正确性判断依据；针对幻觉的实验还需要明确输入支持关系及相应标签。

方法比较将同时考察检测、校准和计算成本。错误分析重点检查高置信度错误、低置信度正确，以及不同信号判断不一致的回答，以确定值得进一步研究的局限。

## 下一阶段目标

下一阶段通过基线比较（Baseline Comparison）、错误分析（Error Analysis）和初步实验（Preliminary Experiments），进一步缩小研究问题。目标是找到一个有实验依据的具体问题，形成可能的技术思路及后续实验安排。

通用 LLM 设置可以先支持这一过程。多模态与医疗任务将在方法问题更清楚后逐步考虑，具体扩展取决于实验观察。长期研究边界见[研究范围（Research Scope）](research-scope.md)，待检验的问题见[研究问题（Research Questions）](research-questions.md)。

## 参考文献（References）

- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
