---
tags:
  - paper-note
  - uq
  - llm
  - sampling
---

# Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation

## 文献信息

- 作者：Lorenz Kuhn, Yarin Gal, Sebastian Farquhar。
- 年份：2023。
- 发表：ICLR。
- 原始来源：[官方页面](https://arxiv.org/abs/2302.09664v3)。
- 版本：arXiv:2302.09664v3。
- 作者提供的代码：[semantic_uncertainty](https://github.com/lorenzkuhn/semantic_uncertainty)。


## 研究问题

[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用")指出，自然语言可以用不同词序和措辞表达相同答案。直接在字符串层面计算熵，会把表达多样性和答案含义的不确定性混在一起。论文提出语义不确定性（Semantic Uncertainty），将关注单位转为答案的意义。

## 方法与流程

模型针对同一问题生成多个答案。作者使用自然语言推断（Natural Language Inference，NLI）模型，在问题语境下检验答案之间的双向蕴含，并将等价答案归入同一语义类。原始实现用类代表进行聚类，并非对所有答案自动得到严格的数学等价关系。

随后汇总属于同一类的序列概率，并据此计算语义熵（Semantic Entropy）。这样，“意思相同但写法不同”的概率质量可以合并。论文同时讨论序列长度归一化，具体估计与 convention 见知识页；这里不将有限 sampling、聚类及概率估计笼统称为无偏估计。

方法需要多次生成、序列概率及额外的语义判断。原文采用 DeBERTa-large MNLI 模型；其语义判断仍可能受到否定、实体、上下文和答案长度影响。

## 实验与主要结果

作者用不同规模的固定预训练 OPT 模型，在 CoQA 开卷问答和 TriviaQA 闭卷问答设置中评价错误识别。主要正确性标签依据与参考答案的 Rouge-L 重合阈值，并检查其他匹配规则的敏感性。因此，这里的错误检测受答案评价规则约束。

比较对象包括序列熵、长度归一化熵、词汇相似性和论文设置下的 p(True) 基线。作者报告，语义聚合在研究的较大模型设置中改善正确与错误答案的区分；长度归一化的收益依赖任务。p(True) 的模型和示例预算与其原始工作并不完全一致，不能把本论文比较当作所有实现的最终排序。

## 优势与局限

该工作的价值在于明确“对什么随机变量计算熵”。它给开放生成的 UQ 增加了语义层面的不变性。

本笔记的理解是，语义聚类、有限样本覆盖和正确性标签构成三个不同误差来源。所读 v3 的 §6 的 Performance evaluation 段（第 7 页）将正确答案与较高不确定性的方向写反；实际使用时应明确让错误作为正类，或相应翻转分数方向，不能照抄该句。

固定模型的答案分歧不等于贝叶斯参数后验分歧，也不构成认知与数据不确定性的唯一分解。稳定重复的错误仍可能具有低语义熵。

## 与博士研究主线的关系

这篇论文是语义层面 UQ 的方法起点，适合与后续 Nature 工作和低成本探针共同阅读。实验设计需分别控制答案生成、等价判定、正确性标注和成本。

## 相关笔记

- [语义熵知识页](../uncertainty/sampling-based/semantic-entropy.md)
- [语义等价评价](../evaluation/ground-truth/semantic-equivalence.md)
- [Nature 后续工作](2024-farquhar-semantic-entropy.md)

## 参考文献

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [原文](https://arxiv.org/abs/2302.09664v3)
