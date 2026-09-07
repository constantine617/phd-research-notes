---
tags:
  - uq
  - representation
---

# Hidden State 与不确定性

在大语言模型（Large Language Model，LLM）中，hidden state 可以作为不确定性量化（Uncertainty Quantification，UQ）的输入特征。问题不是为每个向量赋予一个固定“置信度含义”，而是明确选取哪个计算位置，再验证从该位置能否预测目标。表征本身的机制复用[Hidden Representations](../foundations/language-models/hidden-representations.md)。

## token、层与观察时点

使用以下记号表示指定位置的状态：

$$
\mathbf h_t^{(\ell)}\in\mathbb R^d
\tag{1}
\label{eq:hidden-state-uq}
$$

$t$ 是输入与回答拼接后的位置，$\ell$ 为模型层，$d$ 是特征维度。公式 $\eqref{eq:hidden-state-uq}$ 只定义特征对象，没有把它预设成概率或不确定性。

在常见因果 decoder 中，处理到某位置得到的 hidden state 用于预测下一 token。最后一个输入位置的特征尚未读取回答，可用于问题级估计；最后一个回答位置的特征已读取相应回答前缀，适合研究具体输出。若实现记录的是“用于生成最后 token 的状态”，它与“读取最后 token 后的状态”还相差一个位置。

因此，答案生成前的可回答性、生成后的正确性与对下一 token 的预测，应分别描述。不能用尚未看到答案的特征，却把训练目标不加说明地称为“当前任意回答的正确性”。

## 从状态到序列特征

最后位置选取（Last-token Pooling）使用某个预先确定的末端位置，计算简单但对结束标记和模板敏感。均值聚合（Mean Pooling）平均指定 token 区间，可以降低单位置波动，也可能稀释局部事实信息。特殊 token（Special Token）只有在模型结构和训练确实支持其聚合作用时才有依据，不能默认所有模型都存在统一的句子摘要 token。

对长度不同的回答，均值聚合应使用有效位置掩码，说明是否包括输入、回答、填充和结束 token。跨层拼接则增加特征维度，可能增加存储和过拟合风险。

统一写法可以表示为：

$$
\mathbf r=P\bigl(\{\mathbf h_t^{(\ell)}:t\in\mathcal I\}\bigr),\qquad u=g_\phi(\mathbf r)
\tag{2}
\label{eq:hidden-state-scoring}
$$

$\mathcal I$ 是选定位置集合，$P$ 为 pooling 操作，$\mathbf r$ 为所得特征，$g_\phi$ 为参数为 $\phi$ 的评分函数，$u$ 为输出分数。公式 $\eqref{eq:hidden-state-scoring}$ 是本页的通用组织记号，不是某篇论文提出的专有方法。若采用距离函数，$\phi$ 也可包含参照中心和尺度等估计量。

## Probe 的目标与输出

探针（Probe）可以预测正确性、证据支持或代理不确定性标签。Azaria and Mitchell (2023) 从内部激活预测陈述真假；Kossen et al. (2024) 则用语义熵高低标签训练线性分类器，并分析层和 token 位置差异。两者提供不同监督目标的例子，不能把输出都称为同一个“真实不确定性”。

对高低熵标签训练的 sigmoid 输出，最直接是分类器对“高熵”类别的分数，不是连续熵值，也不是已经校准的错误概率。如果要将它用于正确性预测，必须用独立正确性标签检验这种复用。

## 特征选择与验证边界

层、位置和 pooling 需要在训练或验证数据上选择。对每个候选层试验后只报告测试集最佳者，会使结果混入测试选择。训练集还应防止相同问题、改写模板或重复实体跨集合泄漏，避免 probe 记住浅层模式。

固定模型也不保证固定特征分布。更换 prompt、回答格式、tokenizer 或 checkpoint 可能改变坐标和标签关系。观察到跨任务预测能力，最多支持该验证范围内的迁移，不证明特征是模型生成错误的原因。

## 实际成本

保存所有层、所有 token 的状态会随层数、序列长度、特征维度和精度增长。只保留少量位置可以减少存储，但需要确认实现是否仍临时产生了整批状态。若生成框架没有保存最终所需位置，可能要额外前向计算；不能把 probe 很小当作全部开销很小。

当前研究可先选择具有明确时点的一小组特征，与概率分数在相同任务上比较，再检查其新增信息和迁移能力。这使“内部状态可预测什么”成为可检验的问题，而不是对模型自我认识的假定。

## 相关笔记（Related Notes）

- [表征方法总览](index.md)
- [置信度与不确定性](../uncertainty/foundations/confidence-vs-uncertainty.md)

## 参考文献（References）

- Azaria, A., Mitchell, T. (2023). *The Internal State of an LLM Knows When It’s Lying*. Findings of EMNLP, 967–976. [Paper](https://aclanthology.org/2023.findings-emnlp.68/)
- Kossen, J., Han, J., Razzak, M., Schut, L., Malik, S., Gal, Y. (2024). *Semantic Entropy Probes: Robust and Cheap Hallucination Detection in LLMs*. ICML Workshop on Foundation Models in the Wild；本页使用 arXiv v1. [作者版本](https://arxiv.org/abs/2406.15927v1)
