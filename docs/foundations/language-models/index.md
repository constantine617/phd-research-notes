---
tags:
  - foundations
  - llm
---

# 语言模型（Language Models）

语言模型（Language Model）对语言序列的概率进行建模。本组页面聚焦自回归的生成式大语言模型（Large Language Model，LLM）：给定已经出现的序列，模型预测下一个 token 的条件概率（Conditional Probability），再逐步构成输出。

## 模型在预测什么

文本先被表示为 token 序列。一个 token 可以对应一个词、词的一部分或其他文本片段，具体边界由所用 tokenizer 决定。模型使用词表（Vocabulary）中的 token 编号进行计算，本阶段只需要理解这种序列表示。

例如，给出一段尚未完成的文本后，模型为词表中的各个 token 分配不同概率，表示在当前上下文条件下，它们作为下一个 token 的可能性。模型先给出分布，实际生成哪个 token 则由后续的选择规则决定。

[Bengio et al. (2003)](https://www.jmlr.org/papers/v3/bengio03a.html) 用前文条件下的下一个词概率表示语言模型，并学习词的连续表示。这里借用其概率建模视角；现代 LLM 中的基本单位通常是所用 tokenizer 定义的 token。

## 从 prompt 到生成文本

在常见的生成式 LLM 中，可以按以下顺序理解一次生成步骤：

1. 将 prompt 和已经生成的内容组成当前上下文。
2. 模型处理这些 token，得到相应位置的 hidden state。
3. 输出层产生 logits，即词表中每个候选 token 的未归一化分数。
4. softmax 将 logits 转换为下一个 token 的概率分布。
5. decoding 规则决定实际输出；使用 sampling 时，会按指定分布随机选择 token。
6. 将选出的 token 加入上下文，再执行下一步。

这种使用已有输出继续预测后续输出的方式称为自回归生成（Autoregressive Generation）。[Radford et al. (2019)](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) 使用这一语言建模方式，从输入文本继续生成序列。

本组页面分别讨论上述过程的不同部分。概率分布描述模型为候选项赋予的概率；decoding 描述怎样使用这些数值；hidden state 则是产生输出分数之前的内部表示。读取模型信息时，需要明确读到的是哪一层的信息。

## 与当前研究的关系

不确定性量化（Uncertainty Quantification，UQ）需要先明确可用信号及其含义。这组基础页面对应以下研究入口：

- 基于概率的 UQ（Probability-based UQ）：需要理解 token 概率、序列概率及长度对分数的影响。
- 基于 sampling 的 UQ（Sampling-based UQ）：需要说明多个回答是怎样生成的，以及生成设置如何影响回答集合。
- 置信度校准（Confidence Calibration）：需要区分“模型赋予某个序列的概率”和“这个回答正确的概率”。
- 基于表征的 UQ（Representation-based UQ）：需要明确 hidden state 的层、位置及读取条件。

这些知识也服务于幻觉（Hallucination）与错误检测。具体研究仍需定义希望检测的错误，并检验所选信号是否与该错误有关。单独获得概率、多个回答或内部向量，还不足以完成这种判断。

## 页面导航

- [自回归生成（Autoregressive Generation）](autoregressive-generation.md)：解释序列怎样逐步生成，以及各步之间的依赖。
- [Token 与序列概率（Token and Sequence Probability）](token-sequence-probability.md)：定义模型概率、log probability 和长度相关分数。
- [解码与 Sampling（Decoding and Sampling）](decoding-sampling.md)：说明概率分布如何变成实际输出，以及常见生成设置的作用。
- [内部表征](hidden-representations.md)：解释内部表示、输出投影与模型访问条件。

## 参考文献（References）

- Bengio, Y., Ducharme, R., Vincent, P., Jauvin, C. (2003). *A Neural Probabilistic Language Model*. Journal of Machine Learning Research, 3, 1137–1155. [Paper](https://www.jmlr.org/papers/v3/bengio03a.html)
- Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., Sutskever, I. (2019). *Language Models are Unsupervised Multitask Learners*. OpenAI Technical Report. [Paper](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)
