---
tags:
  - paper-note
  - llm
  - hallucination
  - sampling
---

# SelfCheckGPT: Zero-Resource Black-Box Hallucination Detection for Generative Large Language Models

## 文献信息（Metadata）

- 作者：Potsawee Manakul, Adian Liusie, Mark J. F. Gales。
- 年份：2023。
- 发表：EMNLP，9004–9017。
- 原始来源：[官方页面](https://aclanthology.org/2023.emnlp-main.557/)。
- 预印本：[arXiv:2303.08896](https://arxiv.org/abs/2303.08896)；方法与实验叙述仍按本页注明的正式来源。
- 作者提供的代码：[SelfCheckGPT](https://github.com/potsawee/selfcheckgpt)。


## 研究问题与方法

Manakul et al. (2023)研究无法访问内部概率的大语言模型（Large Language Model，LLM）输出：能否通过同一模型的额外回答，识别原始文章中不可靠的句子。

SelfCheckGPT 固定一份待检查文本，再针对相同输入生成多个额外样本。它逐句检查原始文本是否得到其他样本支持。原文的 zero-resource 指不依赖外部事实知识库；方法仍需要额外模型调用，部分变体还依赖预训练的语义或问答组件。

论文比较 BERTScore 相似度、问答一致性、n-gram 统计、自然语言推断（Natural Language Inference，NLI）和 prompt 判断等变体。它们的分数构造不同：NLI 变体对蕴含与矛盾概率重新归一化；prompt 变体把是否支持的判断转为分数；n-gram 分数不一定与其他变体处于同一区间。

## 实验设置与发现

原文的主要实验使用 WikiBio 人物传记，由 GPT-3 text-davinci-003 生成待检查文本和额外样本。标注数据包含 238 篇文本、1,908 个句子；主要 sampling 设置使用 20 个额外回答。这些数目描述原文实验规模，并不代表方法适用于所有领域所需的固定预算。

人工标签区分准确、轻微不准确与严重不准确；二分类会合并不同不准确等级。句子级评价和文章级标签聚合回答不同问题。原文表中的精确率–召回率面积还采用不同正类定义，不能跨列直接比较数值。

作者报告，prompt 和 NLI 变体在主要实验中比若干其他变体更有效，prompt 变体在所报告的主要指标上表现较强。这是该模型、传记数据和标注规则下的经验结果。

## 优势与局限

该工作把一致性检测落实到长文本句子粒度，且适用于仅能取得生成文本的访问条件。

本笔记的理解是，“其他生成也提到这个事实”与“这个事实有外部证据支持”不同。模型稳定重复错误时可能相互支持；额外样本没有覆盖某句，也不直接证明该句错误。人物频率、文本长度及生成多样性会影响分数。

因此，输出是用于检测的自一致性信号，不是外部事实验证结果。额外生成和语义判断的成本必须合计，也应检验减少样本后性能如何变化。

## 与博士研究主线的关系

这篇论文适合作为黑盒幻觉检测基线，并与 FActScore 的外部证据路线形成对照。研究不确定性量化（Uncertainty Quantification，UQ）时，应明确检测原始答案还是重新选择答案，以及一致性分数对应句子还是整篇文章。

## 相关笔记（Related Notes）

- [幻觉检测](../hallucination/detection.md)
- [FActScore 论文](2023-min-factscore.md)
- [效率指标](../evaluation/efficiency-metrics.md)

## 参考文献（References）

- Manakul, P., Liusie, A., Gales, M. J. F. (2023). *SelfCheckGPT: Zero-Resource Black-Box Hallucination Detection for Generative Large Language Models*. EMNLP, 9004–9017. [原文](https://aclanthology.org/2023.emnlp-main.557/)
