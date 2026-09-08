---
tags:
  - paper-note
  - llm
  - factuality
  - evaluation
  - evidence
---

# FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation

## 文献信息（Metadata）

- 作者：Sewon Min, Kalpesh Krishna, Xinxi Lyu, Mike Lewis, Wen-tau Yih, Pang Wei Koh, Mohit Iyyer, Luke Zettlemoyer, Hannaneh Hajishirzi。
- 年份：2023。
- 发表：EMNLP，12076–12100。
- 原始来源：[官方页面](https://aclanthology.org/2023.emnlp-main.741/)。
- 预印本：[arXiv:2305.14251](https://arxiv.org/abs/2305.14251)；方法与实验叙述仍按本页注明的正式来源。
- 作者提供的代码：[FActScore](https://github.com/shmsw25/FActScore)。


## 研究问题与方法

Min et al. (2023)研究长文本生成的事实精确度（Factual Precision）。一篇回答可能同时包含有支持和无支持的内容，将整篇标成正确或错误会丢失这些差异。

FActScore 将回答拆成原子事实，判断每条事实是否受到指定知识源支持，计算回答内部的支持比例，再汇总模型在作答样本上的平均比例。它关注已经说出的事实中有多少得到支持，不直接测量该说而未说的内容。

自动估计流程包括事实拆解、检索相关证据及支持判定。论文比较不同语言模型判定器、非参数 token 似然信号及组合方案。检索和判定器是评分管线的组成部分，不应与人工定义的目标指标混为一谈。

## 实验设置与主要发现

人工研究主要涉及 183 位人物的传记生成，以 Wikipedia 为支持来源，并比较 InstructGPT、ChatGPT 和 PerplexityAI。作者同时记录回答比例及事实数量，避免仅凭支持比例忽略拒答和篇幅差异。

作者报告，不同系统在事实精确度上有明显差异，较少见人物和回答后部事实更容易缺乏支持。这些是所研究数据中的现象，不能直接提升为所有生成任务的因果规律。

自动估计实验显示，检索帮助判定支持，适当组合可以接近模型级人工评分。原文摘要所述低于 2% 的估计误差，是对论文数值表述的转述，讨论的是聚合 FActScore 估计与人工评分的偏差，不能解释为每条事实分类的错误率小于 2%。

## 优势与局限

作者把评价单位与证据来源明确写入指标，使长文本不同事实的质量可以分别讨论。

本笔记的理解是，原子化本身含有判断：如何拆分、相关性如何限定、事实重要性是否相等，都会影响分母。评价也受固定知识源的覆盖、版本、检索遗漏和判定错误约束。无支持与已证伪不应自动等同。

模型级误差可能因个体误差相互抵消而较小，所以聚合估计准确不保证逐条事实都判得准。按非拒答样本汇总时，还必须查看作答率；只生成很少事实的系统可能获得较高精确度，但完整性仍不足。

## 与博士研究主线的关系

这篇论文为证据支持标签和长文本评价提供具体范例。将其用于不确定性量化（Uncertainty Quantification，UQ）或幻觉检测时，需要区分人工支持标签、自动评分器输出和模型自身置信度。

## 相关笔记（Related Notes）

- [事实性](../hallucination/factuality.md)
- [外部证据 UQ](../uncertainty/evidence-aware/index.md)
- [幻觉标签](../evaluation/ground-truth/hallucination-labels.md)

## 参考文献（References）

- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [原文](https://aclanthology.org/2023.emnlp-main.741/)
