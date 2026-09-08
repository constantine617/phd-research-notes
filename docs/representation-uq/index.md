---
tags:
  - uq
  - representation
---

# 不确定性量化（Uncertainty Quantification，UQ）：基于表征的方法

这类方法从大语言模型（Large Language Model，LLM）的内部特征构造分数，研究输出概率之外是否还有与可靠性相关的信息。Hidden state 的基础机制见[内部表征](../foundations/language-models/hidden-representations.md)；本页关注如何选择特征、建立评分函数，以及这些分数能支持什么结论。

## 动机与基本流程

输出概率是内部计算经过输出层后的结果。它可能混合词频、语法和任务内容；内部特征则提供不同观察位置。Azaria and Mitchell (2023) 在特定真假陈述数据上训练分类器，发现 hidden layer 激活可以帮助预测陈述真假。这支持内部特征具有可预测信息，不等于证明模型拥有可直接读取的通用真值变量。

一个典型流程先固定模型、输入和回答，再提取指定层与 token 位置的特征，建立评分函数，最后在独立数据上验证目标预测。若需要训练，特征选择与标签准备都是方法的一部分；若使用距离，也需说明参照分布来自哪里。

## 用于组织方法的工作分组

表征 UQ 没有完全统一的分类。本页采用以下工作分组（Working Grouping），它根据评分方式组织知识，不把每一组都称为独立且标准化的方法。

| 分组 | 主要做法 | 分数最直接反映什么 |
|---|---|---|
| 探针（Probe）或分类器 | 从特征学习目标标签 | 训练目标的可预测性 |
| 距离方法（Distance-based Method） | 与参照样本、中心或分布比较 | 特征偏离程度 |
| 中心或原型（Centroid / Prototype） | 为数据或类别建立代表 | 与所选代表的接近程度 |
| 几何方法（Geometry-based Method） | 观察方向、子空间、局部密度或结构 | 定义的表征几何关系 |
| 逐层分析（Layer-wise Analysis） | 在不同层重复同一评分过程 | 信息在不同观察位置的可提取程度 |

Fadeeva et al. (2023) 汇总了利用潜在表征和密度估计的 UQ 实现，为距离与密度路线提供了实例。这里的中心、原型和几何分组是方法设计层面的组织方式，不代表它们已经在所有生成任务中得到验证。

## 监督目标决定学到了什么

正确性标签可以来自答案规则或人工核验；证据支持标签则来自文档与主张关系。二者不可交换。模型也可能学习数据中的题型、长度、词频或格式线索，如果数据划分没有隔离这些模式，就容易高估迁移能力。

Kossen et al. (2024) 的 Semantic Entropy Probes 使用多次生成计算的语义熵产生高低标签，再训练线性 logistic regression 探针。其直接目标是高语义熵类别，而非精确连续熵或世界事实。无需人工正确性标签仍然包含代理标签构造、模型调用和有监督拟合。

分类器输出一个零到一的数，并不证明该数已经通过[置信度校准（Confidence Calibration）](../calibration/index.md)。即使对训练目标校准，也需要另行验证它与最终错误或幻觉标签的关系。

## 距离与几何为什么需要参照

离训练样本远可能意味着分布外（Out-of-distribution，OOD），但罕见且正确的知识也可能远；熟悉的错误模板可能很近。距离是相对于某种表示和参照的偏离，不能仅凭远近判断真实性。

原型的含义取决于如何建立：所有训练回答的中心代表常见特征；正确回答的中心引入了正确性监督；按题型分别建立的中心则引入任务分组。高维空间中的尺度、归一化和协方差估计都可能改变距离，不能把 embedding 空间的“接近”直接等同于语义等价。

几何方向若可以预测标签，说明在指定设置下存在统计关联。要进一步声称它参与生成错误的因果机制，需要干预和排除其他解释。论文标题中的“知道”或“说谎”也不应被直接翻译为关于模型主观状态的结论。

## 访问、层选择与迁移

通常需要白盒（White-box）能力，即实际取得指定 hidden state。仅有文本或已选 token 概率的接口不足以执行内部探针；如果另一个开放模型替代提取特征，那是辅助模型路线，信号归属也随之改变。

层与 pooling 选择会影响结果。某层在一个数据集上最优，不代表换模型、题型或 prompt 后仍最优。跨模型甚至可能连维度和坐标都不同；直接复用探针权重通常缺乏依据，至少需要对齐或重新拟合并验证。

成本不仅是小型分类器的推理。全层特征保存、长序列搬运、参照集合检索及训练标签生成都可能占用显存与时间。当前研究关注能否从少量层和位置获得可迁移的信号，并通过错误分析区分内容信息、数据捷径和新的偏差。

## 相关笔记（Related Notes）

- [hidden state 与不确定性](hidden-state-uncertainty.md)
- [高效 UQ](../efficient-uq/index.md)

## 参考文献（References）

- Azaria, A., Mitchell, T. (2023). *The Internal State of an LLM Knows When It’s Lying*. Findings of EMNLP, 967–976. [Paper](https://aclanthology.org/2023.findings-emnlp.68/)
- Fadeeva, E., Vashurin, R., Tsvigun, A., et al. (2023). *LM-Polygraph: Uncertainty Estimation for Language Models*. EMNLP: System Demonstrations, 446–461. [Paper](https://aclanthology.org/2023.emnlp-demo.41/)
- Kossen, J., Han, J., Razzak, M., Schut, L., Malik, S., Gal, Y. (2024). *Semantic Entropy Probes: Robust and Cheap Hallucination Detection in LLMs*. ICML Workshop on Foundation Models in the Wild；本页使用 arXiv v1. [作者版本](https://arxiv.org/abs/2406.15927v1)
