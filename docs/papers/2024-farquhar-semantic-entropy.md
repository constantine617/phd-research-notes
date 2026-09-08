---
tags:
  - paper-note
  - uq
  - llm
  - hallucination
---

# Detecting hallucinations in large language models using semantic entropy

## 文献信息（Metadata）

- 作者：Sebastian Farquhar, Jannik Kossen, Lorenz Kuhn, Yarin Gal。
- 年份：2024。
- 发表：Nature，630，625–630。
- 原始来源：[官方页面](https://doi.org/10.1038/s41586-024-07421-0)。
- 作者提供的代码：[semantic_uncertainty](https://github.com/jlko/semantic_uncertainty)、[long_hallucinations](https://github.com/jlko/long_hallucinations)。


## 研究问题与方法

Farquhar et al. (2024)研究大语言模型（Large Language Model，LLM）的一类幻觉：模型在多次生成中给出语义上不稳定的错误内容，文中称为 confabulation。论文并未声称用语义熵（Semantic Entropy）覆盖所有错误或全部幻觉成因。

基本流程是针对问题生成多个答案，判断答案之间的语义关系，将表达同一含义的答案聚类，再计算语义类分布的不确定性。论文也使用基于样本频数的离散估计，使无法获取 token 概率的模型可以采用该思路。

句子级问答和长文本事实检测采用不同管线。后者先从传记抽取事实，为事实构造多个问题，再多次回答这些问题，把原始事实纳入一致性检验，最后聚合各问题的语义熵。它不是对整篇文章一次计算熵就自动得到每个错误位置。

## 实验条件与发现

句子问答实验覆盖 Falcon、Llama 2、Mistral 系列的特定指令模型，以及 SQuAD、TriviaQA、Natural Questions、BioASQ、SVAMP 等任务的论文设置。原本可能带检索或阅读上下文的数据被用于闭卷适配时，必须以论文输入条件为准。

正确性判断也有区分：句子回答使用参考答案语义判断，短答案设置采用 token F1 阈值。语义判断器在不同实验中使用 GPT 系列或 DeBERTa，不能视为同一聚类器。GPT-4 传记实验覆盖的对象和事实数量较小，主要说明方法可以扩展到事实粒度。

作者报告，语义熵在研究任务上能够识别部分错误，并改善拒绝高不确定性回答后的保留集准确率。原文的准确率–覆盖率面积与风险–覆盖率面积方向相反；比较结果时必须核实指标全名与方向。

## 优势与局限

作者将语义不变性扩展到更多模型、任务和无 token 概率的访问条件，且明确讨论其检测范围。

本笔记的理解是，长文本流程的主要挑战不仅是熵估计。事实抽取、问题是否真正针对原始事实、参考答案判断和拒答处理都会改变检测对象。较低语义熵可能来自稳定的系统性错误，较高语义熵也可能来自合理的多答案性。

语义判断和额外生成的成本需要单独计入。频数版去除了概率访问要求，仍保留多次 sampling 和判断调用，并未变成单次生成方法。

## 与博士研究主线的关系

这篇论文连接幻觉定义、语义不确定性量化（Uncertainty Quantification，UQ）和选择性预测。后续效率研究可以近似其中的昂贵信号，但必须分别报告代理信号拟合质量和真实错误检测质量。

## 相关笔记（Related Notes）

- [语义熵知识页](../uncertainty/sampling-based/semantic-entropy.md)
- [幻觉标签](../evaluation/ground-truth/hallucination-labels.md)
- [语义熵探针](2024-kossen-semantic-entropy-probes.md)

## 参考文献（References）

- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [原文](https://doi.org/10.1038/s41586-024-07421-0)
