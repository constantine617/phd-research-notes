---
tags:
  - foundations
---

# 基础知识

本板块整理与博士研究主线直接相关的前置知识，帮助理解 LLM 的生成过程，以及后续研究能够读取和分析哪些模型信息。内容围绕 UQ、幻觉（Hallucination）与错误检测所需的基础展开。

当前首先整理[语言模型（Language Models）](language-models/index.md)。这一部分解释模型如何逐步生成 token、如何给 token 和序列赋予概率、decoding 与 sampling 如何决定实际输出，以及 hidden state 在生成过程中处于什么位置。

这些基础分别对应后续研究中的几个问题：概率分数衡量的是什么，多个回答之间为什么会存在差异，置信度校准（Confidence Calibration）需要校准什么，以及内部表征能否提供额外的可靠性信息。阅读时应始终区分模型提供的信息、从中构造的分数，以及最终希望判断的回答正确性。

另外两个基础入口是[概率与信息论（Probability and Information Theory）](probability-information-theory/index.md)和[视觉语言模型（Vision-Language Model，VLM）](vision-language-models/index.md)。三组基础共同解释生成、概率、内部表征与视觉证据的关系，供方法和评价页面引用。
