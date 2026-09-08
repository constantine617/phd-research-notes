---
tags:
  - uq
  - sampling
---

# 自一致性（Self-Consistency）

Self-Consistency 原始工作的目标是改进推理任务的 decoding 与答案聚合。[Wang et al. (2023)](https://arxiv.org/abs/2203.11171v4 "文献引用") 在CoTprompt 的基础上生成多条推理路径，再对最终答案聚合。论文于 2022 年发布预印本，正式发表于 ICLR 2023。**原方法不应被描述为最初就是 UQ 方法。**

## 原始方法如何工作

LLM 面对同一个推理问题，可以生成不同中间步骤。这些步骤可能通向同一个最终答案。原方法的直觉是，多个推理路径共同支持的答案值得优先选择；它通过 sampling 与答案聚合替代仅使用一次贪心 decoding 的结果。

实现时，需要先从每条生成中提取最终答案。答案抽取、单位换算、数值规范化和无法解析的处理，都是方法的一部分。如果把整个推理段落进行字符串匹配，统计的对象就不再是原工作关注的最终答案。

在原论文的比较中，简单多数投票是重要聚合方式，作者也研究了概率加权等变体。获得更好的最终答案属于生成质量目标；判断所选答案是否可能出错，则需要另一层验证。

## 从答案聚合到 UQ 信号

设 $K$ 次生成提取出的答案依次为 $a_1,\ldots,a_K$。某个规范化答案 $a$ 的经验频率为：

$$
\hat p(a)=\frac{1}{K}\sum_{k=1}^{K}\mathbf 1[a_k=a]
\tag{1}
\label{eq:self-consistency-frequency}
$$

$\mathbf 1[\cdot]$ 是指示函数，条件成立时取一，否则取零；$K$ 为纳入统计的生成次数。若有解析失败，必须说明它是否计入分母或作为独立状态，不能悄悄丢弃后只对成功解析者给出更高频率。

公式 $\eqref{eq:self-consistency-frequency}$ 描述当前模型和 sampling 设置下的答案出现比例。可以选择频率最大的答案作为输出，也可以用其频率或不同答案的分散程度构造 UQ 信号。若要评分的是事先生成的回答，应取那个回答所属答案的频率，而不能直接拿多数答案的高频率替它背书。

把 $\hat p(a)$ 写成概率符号，是因为它估计答案分布；它并未自动成为“答案 $a$ 正确”的概率。[Xiong et al. (2024)](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6733cf15e10e2cd1d59af033c3bb8507-Abstract-Conference.html "文献引用") 将多回答一致性与置信度聚合用于校准和失败预测，说明这类复用需要单独的任务定义与实验。

## 精确匹配（Exact Match，EM）与语义匹配

精确匹配适合输出可规范化且等价规则清楚的任务。例如可以统一数值格式，但只有在任务允许时才能将近似值视作同一个答案。一个尾数差异在概念问答中可能无关，在数值计算中却可能决定正确性。

语义等价适用于表达不同但意义相同的答案。不能用主题相近替代等价，也不能忽略否定、范围或单位。若使用模型判定等价，比较器自己的错误和成本应计入，相关概率聚合见[语义熵](semantic-entropy.md)。

## 一致为何仍可能错误

同一个模型可以把常见误解稳定地复述多次。即使每次生成在固定条件下独立抽样，共享的模型偏差仍然存在；它与抽样过程中的统计依赖是两个不同问题。若样本由共享搜索树、候选重排或前后互相参考产生，还需额外检查相关性。

一致也不能证明推理有效。多条错误路径可能碰巧得到正确最终答案，或多条表面不同路径重复同一错误步骤。若任务要求推导正确，就应另行评价推导，不能仅靠终值投票。

## 预算与使用边界

多次生成增加 token 数与等待时间，解析和语义判定还可能增加额外调用。比较改进时，应同时报告基础单次回答质量、聚合后的质量，以及 UQ 对其目标输出的识别能力。这样才能区分“生成了更好的答案”和“更知道何时会错”。

当前研究可以把答案频率作为透明的 sampling 基线，再分析重复错误、解析失败、多个有效答案与样本预算的影响。多数答案的稳定性是可观察信号，独立来源核查仍然承担不同作用。

## 相关笔记

- [Sampling 总览](index.md)
- [不确定性与正确性](../foundations/uncertainty-vs-correctness.md)

## 参考文献

- Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E. H., Narang, S., Chowdhery, A., Zhou, D. (2023). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR. [作者会议版本](https://arxiv.org/abs/2203.11171v4)
- Xiong, M., Hu, Z., Lu, X., Li, Y., Fu, J., He, J., Hooi, B. (2024). *Can LLMs Express Their Uncertainty? An Empirical Evaluation of Confidence Elicitation in LLMs*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6733cf15e10e2cd1d59af033c3bb8507-Abstract-Conference.html)
