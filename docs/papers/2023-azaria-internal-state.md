---
tags:
  - paper-note
  - llm
  - representation
  - uq
---

# The Internal State of an LLM Knows When It’s Lying

## 文献信息

- 作者：Amos Azaria, Tom Mitchell。
- 年份：2023。
- 发表：Findings of EMNLP，967–976。
- 原始来源：[官方页面](https://aclanthology.org/2023.findings-emnlp.68/)。
- 预印本：[arXiv:2304.13734](https://arxiv.org/abs/2304.13734)；方法与实验叙述仍按本页注明的正式来源。


## 研究问题与方法

[Azaria and Mitchell (2023)](https://aclanthology.org/2023.findings-emnlp.68/ "文献引用")研究 LLM 的内部表征：一句陈述输入模型后，hidden state 是否含有可用于判别陈述真假的信息。

论文提出基于语言模型激活的陈述准确性预测（Statement Accuracy Prediction, based on Language Model Activations，SAPLMA）。作者冻结语言模型，在抽取的层表征上训练监督分类器。分类器是具有三个隐藏层的多层感知机（Multilayer Perceptron，MLP），不是线性探针。

流程包括构建真假陈述与标签、提取不同层激活、训练分类器，以及在未参与训练的主题上评价。探针必须能够访问模型内部表征，也依赖用于训练它的真假标签。

## 实验设置与结果

论文主要使用 OPT-6.7B 和 Llama 2-7B。构造数据覆盖城市、发明、元素、动物、公司和科学事实等六个主题，真假例子的获得包括属性替换与人工整理。作者用留出一个主题的方式检查迁移，并比较表征分类器、文本分类和 prompt 基线。

作者报告，在这些设置中，内部激活上的分类器能有效识别真假，较佳层的位置随模型变化。论文还单独评价模型生成的陈述，并以人工事实核查建立标签；这比模板化陈述更接近生成场景，但性能和阈值行为有所变化。

默认阈值与验证集调节后的阈值应分开报告。不能把调整阈值后的生成陈述结果直接当作固定阈值跨分布迁移的证据。

## 优势与局限

作者展示了输出文本概率之外的可预测信息，并用跨主题实验减少只在单一主题内拟合的疑虑。

本笔记的理解是，标题中的“知道”与“说谎”不能按人的主观知识或欺骗意图解释。探针可分类，只能说明相应表示在给定标签和数据下具有预测信息；它没有证明一个独立、可因果操纵的真值变量。

人工替换产生的假句可能带有统计捷径。跨主题测试也不能排除所有构造特征，更不能替代真实开放生成和领域迁移。模型、层、输入位置与训练标签变化后，需要重新检验。

## 与博士研究主线的关系

这篇论文是表征 UQ 的重要证据，但其训练目标是陈述真假。它可以与预测高语义熵的探针对照，以区分“监督正确性信号”和“对另一种不确定性信号的代理”。

## 相关笔记

- [hidden state 不确定性](../representation-uq/hidden-state-uncertainty.md)
- [语义熵探针](2024-kossen-semantic-entropy-probes.md)
- [正确性定义](../evaluation/ground-truth/correctness-definition.md)

## 参考文献

- Azaria, A., Mitchell, T. (2023). *The Internal State of an LLM Knows When It’s Lying*. Findings of EMNLP, 967–976. [原文](https://aclanthology.org/2023.findings-emnlp.68/)
