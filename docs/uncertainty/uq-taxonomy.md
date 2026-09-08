---
tags:
  - uq
---

# 不确定性量化（Uncertainty Quantification，UQ）分类

大语言模型（Large Language Model，LLM）的 UQ 没有唯一通用分类。一个方法可以同时使用多次生成、token 概率和语义比较；文件夹只能给它一个主要入口。**本页是知识库用于组织和比较方法的工作分类（Working Taxonomy），不是领域唯一标准。**

Geng et al. (2024) 按生成与分类任务进一步区分估计和校准方法；Fadeeva et al. (2023) 在 LM-Polygraph 中同时标记信息类别、访问方式和资源要求。本页吸收这些比较维度，将其整理为相互独立的轴。表中组合与命名属于知识库综合，不归因于某一篇论文。

## 六个分类轴

| 分类轴 | 需要记录的取值或问题 | 解释价值 |
|---|---|---|
| 信号来源（Signal Source） | 概率、回答分歧、语言自报、内部特征、外部核查 | 估计器究竟观察了什么 |
| 模型访问（Model Access） | 文本输出、所选 token 的 log-probability、完整 logits、hidden state、参数或梯度 | 当前部署环境能否实现 |
| 生成次数（Number of Generations） | 单序列、固定多样本、按输入自适应；是否包含额外评审生成 | 比较信息量和生成开销 |
| 估计粒度（Granularity） | token、序列、语义类别、主张、指定分布 | 防止把局部分数解释成整体性质 |
| 证据来源（Evidence Source） | 模型自身、独立模型、来源文档、检索知识、多模态观察 | 支持关系来自哪里，是否独立 |
| 输出类型（Output Type） | 未校准分数、概率估计、类别分布、排序、集合或决策 | 数值允许怎样解释和使用 |

单个数值同时占据多轴。例如语义熵（Semantic Entropy）是一个标量，但它由含义类别上的分布得到。不能因为输出只有一个数，就把估计对象改称“回答是否正确”。类别分布还可能帮助发现问题歧义，而不仅用于风险排序。

## 访问能力需要逐项写清楚

黑盒（Black-box）通常指只拿到输入输出；白盒（White-box）通常意味着能够观察模型内部。灰盒（Grey-box）的边界在文献中不统一：有些研究把 log-probability 接口视作灰盒，有些直接纳入白盒。因此，本知识库优先写实际能力，颜色标签只作辅助。

“返回概率”尤其含糊。只返回已生成 token 的 log-probability，可以构造这条序列的似然分数；完整 token 分布的熵则需要更充分的概率信息。接口返回前若干候选的概率，不代表剩余概率质量为零，也不保证允许对任意候选回答重新评分。

同样，拥有开放权重不等于已经低成本取得全部 hidden state。运行环境可能只暴露文本，或者保存内部特征需要额外显存。接口能力、实际启用的信息与训练时所需能力应分开记录。

## 代表性方法的交叉位置

| 方法或路线 | 主要信号与访问 | 生成与粒度 | 输出解释 |
|---|---|---|---|
| 长度归一化似然分数 | 所选 token 的 log-probability | 一条序列；序列层 | 常见程度或风险相关信号 |
| [自一致性（Self-Consistency）](sampling-based/self-consistency.md)的频率复用 | 多个回答文本及答案解析 | 多次生成；答案层 | 回答频率或聚合结果 |
| 语义熵的概率版本 | 多次生成、序列分数、含义判定器 | 语义类别层 | 所定义类别分布的熵估计 |
| 语义熵的频率版本 | 多个回答文本与含义判定器 | 语义类别层 | 经验类别频率的熵 |
| 正确性探针 | hidden state 与训练标签 | 部署时可单序列 | 对训练目标的预测分数 |
| 证据条件下的置信度 | 回答、来源及核查结果 | 主张或回答层 | 针对指定证据目标的估计 |

Wang et al. (2023) 的 Self-Consistency 原本用于推理答案聚合；本表中的频率用途是 UQ 视角的复用。Kuhn et al. (2023) 与 Farquhar et al. (2024) 的语义熵需要区分理论定义、概率加权近似和频率近似。它们的具体计算统一放在[语义熵页面](sampling-based/semantic-entropy.md)。

## 分类不能替代效用比较

“单次生成”只限定目标模型生成的序列数，并未限定辅助模型调用数。一个单序列核查器可能读取很长证据并多次输出推理，实际比几条短回答更慢。“无需正确性标签”也不等于没有学习过的组件：语义比较器可能事先接受过监督训练。

对于混合方法，应分别列出基础信号和后续映射。例如，用内部特征预测错误风险，再做[置信度校准（Confidence Calibration）](../calibration/index.md)，其第一步属于表征估计，第二步属于校准。校准是对数值解释的约束，不应与“概率”“sampling”并列成互斥的信息来源。

实际检索一篇新论文时，先填六个轴，再记录训练标签、部署条件与成本。若某项不清楚，保留未知，不凭方法名称补齐。这样可以在不复制页面的前提下发现方法之间的重叠，以及实验尚未控制的差异。

## 相关笔记（Related Notes）

- [高效 UQ](../efficient-uq/index.md)
- [表征方法](../representation-uq/index.md)

## 参考文献（References）

- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
- Fadeeva, E., Vashurin, R., Tsvigun, A., et al. (2023). *LM-Polygraph: Uncertainty Estimation for Language Models*. EMNLP: System Demonstrations, 446–461. [Paper](https://aclanthology.org/2023.emnlp-demo.41/)
- Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E. H., Narang, S., Chowdhery, A., Zhou, D. (2023). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR. [作者会议版本](https://arxiv.org/abs/2203.11171v4)
- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
