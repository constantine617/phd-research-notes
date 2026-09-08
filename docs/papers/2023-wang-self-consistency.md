---
tags:
  - paper-note
  - llm
  - sampling
  - uq
---

# Self-Consistency Improves Chain of Thought Reasoning in Language Models

## 文献信息

- 作者：Xuezhi Wang, Jason Wei, Dale Schuurmans, Quoc Le, Ed H. Chi, Sharan Narang, Aakanksha Chowdhery, Denny Zhou。
- 年份：2023。
- 发表：ICLR。
- 原始来源：[官方页面](https://arxiv.org/abs/2203.11171v4)。
- 版本：[arXiv:2203.11171v4](https://arxiv.org/abs/2203.11171v4)，首版 2022，本文按 2023 版本阅读。


## 研究问题与核心方法

[Wang et al. (2023)](https://arxiv.org/abs/2203.11171v4 "文献引用")研究 LLM 的推理生成：同一问题可能有不同推理路径，单次贪心 decoding 容易选中错误路径。自一致性（Self-Consistency）用多条路径的最终答案投票替代只保留一条路径。

方法先提供少样本 CoTprompt，再进行多次随机 sampling。每条输出被解析为一个最终答案，对相同答案汇总投票并返回票数最多者。论文也比较带生成概率权重的变体；简单多数投票是重要基线。

这个流程不需要针对任务额外 fine-tuning，也不需要训练辅助验证器，但需要增加生成次数及答案解析。它主要改变答案选择，本身并不是为输出经过校准的正确概率而提出。

## 实验设置与结果

论文测试 UL2、LaMDA、PaLM 和当时的 Codex 模型，覆盖 GSM8K、SVAMP、AQuA 等算术推理，CommonsenseQA、StrategyQA、ARC 等常识问答，以及字母拼接、硬币翻转等符号任务。不同任务的最终答案可解析性并不相同。

主要比较使用相同 CoT 示例的单路径 decoding 与多路径聚合。作者在主要设置中每次生成 40 条路径并报告重复运行结果，同时考察样本数增加时的变化。原文报告多种任务和模型上答案准确率提高，收益受任务、模型及 sampling 设置影响。

论文的成功标准主要是最终答案正确。正确答案不能证明所生成推理链的每一步都正确，也不能据此把被多数选中的推理过程当作忠实解释。

## 优势与局限

作者展示了推理路径多样性能够改善答案选择，而且不必依赖额外监督训练。这使其成为多次生成方法的重要基础。

本笔记的理解是，投票比例可作为一致性信号，但它不是直接的知识证据。多个路径可能重复同一种错误，问题歧义和解析失败也会影响计票。把该比例用于 UQ 时，需要另行验证其错误排序、校准和跨任务稳定性。

计算成本随生成数量增加；不同路径的长度、停止条件和解析规则也应进入成本报告。只比较最终准确率而忽略这些条件，会低估方法的代价。

## 与博士研究主线的关系

这篇论文提供“多次生成可以产生哪些信号”的起点。研究中应区分改善生成答案与评价既定答案可靠性两个用途，并与语义聚类、低成本代理信号和选择性预测建立联系。

## 相关笔记

- [自一致性知识页](../uncertainty/sampling-based/self-consistency.md)
- [sampling 设置](../evaluation/experimental-protocols/sampling-settings.md)
- [语义不确定性论文](2023-kuhn-semantic-uncertainty.md)

## 参考文献

- Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E. H., Narang, S., Chowdhery, A., Zhou, D. (2023). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR. [原文](https://arxiv.org/abs/2203.11171v4)
