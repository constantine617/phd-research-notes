---
tags:
  - paper-note
  - llm
  - uq
  - representation
  - efficiency
---

# Semantic Entropy Probes: Robust and Cheap Hallucination Detection in LLM

## 文献信息

- 作者：Jannik Kossen, Jiatong Han, Muhammed Razzak, Lisa Schut, Shreshth Malik, Yarin Gal。
- 年份：2024。
- 发表：ICML 2024 Workshop on Foundation Models in the Wild；本文使用 arXiv v1。
- 原始来源：[官方页面](https://arxiv.org/abs/2406.15927v1)。
- 版本：arXiv:2406.15927v1，2024-06-22；workshop 论文，不是 ICML 主会录用论文。


## 研究问题与方法

[Kossen et al. (2024)](https://arxiv.org/abs/2406.15927v1 "文献引用")希望减少语义熵在 inference 时的多次生成成本。语义熵探针（Semantic Entropy Probe，SEP）从 LLM 的 hidden state 预测高、低语义熵类别。

离线阶段对训练问题生成多个答案，聚类后计算频数语义熵，再将其二值化。分割阈值按两组内部的平方偏差确定。作者在贪心回答相关的 hidden state 上训练带正则化的逻辑回归探针。

因此，训练目标既不是连续语义熵回归，也不是人工标注的正确／错误标签。探针输出首先表示其训练任务中的高熵类别倾向，不能自动解释为回答错误概率。

## 工作流与实验设置

作者比较生成前最后一个输入 token 的表征与回答结束前最后一个响应 token 的表征。前者可以在回答前打分，后者需要完成一次回答。两种条件的可用信息与成本不同。

实验覆盖 TriviaQA、SQuAD、BioASQ、Natural Questions，以及 Llama、Mistral、Phi 等特定模型。短答案与所谓 long answer 使用不同生成指令；后者主要是简短完整句，不等于长文档生成。离线标签使用额外 sampling 和语义判断，在线才以单次 hidden state 近似。

对照包括直接用正确性标签训练的 accuracy probe，以及成本较高的语义熵。作者报告，直接正确性探针在部分同分布设置更强，语义熵探针 在所研究的任务迁移设置中更有优势；语义熵探针 与完整语义熵仍存在性能差距。

## 优势与局限

论文明确展示了把昂贵代理监督转移到离线阶段的可行性，并同时考察回答前后的特征位置。

作者也指出 BioASQ 中 yes/no 问题可能产生任务类型捷径。本笔记的理解是，跨数据集迁移的改善不等于没有捷径，更不等于学到了普适幻觉判别器。训练样本的语义聚类错误和 sampling 偏差会传给探针。

成本应分为离线标签构造、探针训练和在线提取／预测。“低成本”不能省略离线模型调用，也不能忽略 hidden state 访问限制。本文使用的 v1 在第 4 页 §3 的离散概率说明中将分母写成语义类数，存在归一化笔误，计算应以样本频数除以总生成数为准。

## 与博士研究主线的关系

该论文连接高效 UQ 和表征信号。设计类似实验时应分别评价代理任务拟合、真实错误检测、跨任务迁移及摊销成本，避免只凭探针训练准确率判断可靠性。

## 相关笔记

- [高效 UQ](../efficient-uq/index.md)
- [表征 UQ](../representation-uq/index.md)
- [语义熵原方法](2023-kuhn-semantic-uncertainty.md)

## 参考文献

- Kossen, J., Han, J., Razzak, M., Schut, L., Malik, S., Gal, Y. (2024). *Semantic Entropy Probes: Robust and Cheap Hallucination Detection in LLMs*. ICML Workshop on Foundation Models in the Wild；本页使用 arXiv v1. [原文](https://arxiv.org/abs/2406.15927v1)
