---
tags:
  - uq
  - sampling
---

# UQ：基于 sampling 的方法

这类方法给 LLM 相同的 prompt，多次生成回答，再比较一致与分歧。直觉是观察单次回答隐藏的其他可能输出；估计器真正测到什么，则由 sampling 分布、答案比较规则和聚合方式共同决定。

## 基本工作过程

先固定任务输入、目标模型和生成设置，再产生多条回答。之后可以抽取最终答案、比较表面相似度，或根据语义等价（Semantic Equivalence）进行分组，最后把频率或差异转成分数。

```text
固定输入和生成设置
→ 多次生成回答
→ 抽取答案或比较回答
→ 保留一致、分歧及无法判断的状态
→ 构造分数，并关联到明确的目标回答或输入
```

[自一致性（Self-Consistency）](self-consistency.md)原本通过多条推理路径聚合最终答案（[Wang et al., 2023](https://arxiv.org/abs/2203.11171v4 "文献引用")）。[语义熵（Semantic Entropy）](semantic-entropy.md)则将等价表达聚合为含义类别，再观察类别分布（[Kuhn et al., 2023](https://arxiv.org/abs/2302.09664v3 "文献引用")）。两者都需要多个输出，但原始目的和最后产生的量不同。

## 比较规则决定分歧来源

字符串不同可能只是同义改写。字符串相似也可能掩盖关键差别，如否定词、数量、单位或时间限定。短答案可以采用事先规定的规范化，长回答通常需要更复杂的语义比较。

整段一致还可能遮住局部错误。两个段落大部分内容相同，却在一个关键事实处不同，平均相似度可能仍很高。反过来，多个完全有效的例子可能互不等价。任务粒度应先决定，再选择比较器，而不是把比较器方便输出的结果当作最终目标。

## 样本数与估计波动

少量样本可能碰巧全部相同；增加样本能够观察更多模式，也会增加生成成本。给定稳定的 sampling 分布，经验频率随样本增多通常更稳定，但有限样本仍可能漏掉罕见答案。扩大样本量也无法修正系统性语义误分组。

在条件独立的重复抽样下，可以使用相应频率估计直觉；如果流程重用候选、通过 beam 搜索共享路径，或后续生成读取前面的回答，就不能直接套用独立样本假设。即使独立抽样成立，样本仍来自同一个有偏模型，多数意见不会因此成为独立事实证据。

这里还应区分回答分布本身的分散与估计器因样本不足产生的波动。某次估出的高熵可能不稳定；重复实验可以帮助识别这种波动，但不会改变该分布是否对应真实正确性。

## 温度与计算预算

Decoding 温度、候选截断和最大长度会改变观察到的多样性。低温度容易提高一致性，高温度可能放大低质量候选；没有一种设置能仅凭多样性大小被认定为更忠实地测量知识不足。[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 的消融研究也强调生成准确性与多样性之间的取舍。

成本除回答生成外，还包括答案解析、两两比较、语义核查和必要的聚合。固定样本预算与按输入自适应追加样本，可以形成不同系统，但自适应停止本身也会影响分数分布，详见[高效 UQ](../../efficient-uq/index.md)。

Sampling 多样性不自动等于[认知不确定性（Epistemic Uncertainty）](../foundations/epistemic-aleatoric.md)。[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 的工作说明含义变化可以帮助识别一类错误，同时保留持续同错这一盲区。当前研究需要比较不同分歧信号在具体任务中能发现什么，以及它们遗漏了哪些稳定错误。

## 参考文献

- Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E. H., Narang, S., Chowdhery, A., Zhou, D. (2023). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR. [作者会议版本](https://arxiv.org/abs/2203.11171v4)
- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
