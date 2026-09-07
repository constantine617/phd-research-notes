---
tags:
  - foundations
---

# 基础知识（Foundations）

本板块整理与博士研究主线直接相关的前置知识，帮助理解大语言模型（Large Language Model，LLM）的生成过程，以及后续研究能够读取和分析哪些模型信息。内容围绕不确定性量化（Uncertainty Quantification，UQ）、幻觉（Hallucination）与错误检测所需的基础展开。

当前首先整理[语言模型（Language Models）](language-models/index.md)。这一部分解释模型如何逐步生成 token、如何给 token 和序列赋予概率、decoding 与 sampling 如何决定实际输出，以及 hidden state 在生成过程中处于什么位置。

这些基础分别对应后续研究中的几个问题：概率分数衡量的是什么，多个回答之间为什么会存在差异，置信度校准（Confidence Calibration）需要校准什么，以及内部表征能否提供额外的可靠性信息。阅读时应始终区分模型提供的信息、从中构造的分数，以及最终希望判断的回答正确性。

后续将继续整理概率与信息论（Probability & Information Theory）和视觉语言模型（Vision-Language Models）。这两个子板块将在各自阶段建立。本阶段先把语言模型中的生成、概率、sampling 与内部表征之间的关系说明白，供后续方法页面反复引用。
