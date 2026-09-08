---
tags:
  - uq
  - llm
---

# LLM 中的不确定性

开放生成允许模型给出长度不同、措辞不同且都有效的回答，因此不能把固定类别分类中的直觉原样搬过来。UQ 需要先说明随机变化发生在哪个空间，以及我们关心哪些变化。[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 将语言表达与含义的区别作为这一问题的核心。

## 开放的输出空间

给定同一 prompt，模型可以回答一个名字、完整句子或附带解释的段落。可生成字符串数量很大；设置最大长度只是限制计算范围，并没有自动建立“所有可能答案”的任务类别。如果回答被截断，观察到的对象还是未完成前缀，不能直接按完整回答解释。

多个有效答案还分两种情况。第一种是语义等价（Semantic Equivalence），如单位规范允许时，“半小时”与“30 分钟”表达相同答案。第二种是答案确实不同但都满足要求，如“举出一种质数”允许多个数。把第一种差异消除通常有助于聚焦含义；第二种差异是否算不确定，需要由任务目的决定。

因此，语义分组不是先验的正确性判定。两个答案可以同样正确却不等价，也可以同样错误而完全等价。问题上下文、数量单位、时间范围和任务规则共同决定哪些差异应被保留。

## 自回归过程中的局部条件

自回归生成（Autoregressive Generation）以已有前缀为条件产生下一 token，基础机制见[自回归生成页面](../../foundations/language-models/autoregressive-generation.md)。某个实体名一旦被选中，后续语法和惯常解释可能都很容易延续，于是模型可以把一个错误前提续写成流畅、局部高概率的段落。

反过来，一个罕见但正确的人名、专业术语或等价改写，可能得到较低的模型概率。这种概率面向模型语言分布中的输出选择；任务正确性面向事实、推导或来源支持，两者需要通过数据建立关系。[Azaria and Mitchell (2023)](https://aclanthology.org/2023.findings-emnlp.68/ "文献引用") 在其研究中也指出，句子概率会受到长度和词频影响。

不能仅查看整段平均值就断定关键事实稳定。一个错误数字可能只占很少的 token，而周围大量常见文本会稀释它的局部分数。若改用最不确定位置，罕见但正确的符号又可能主导结果。聚合规则应与想识别的错误类型相匹配。

## 模型分布与世界事实

记固定模型的序列分布为 $p_\theta(y\mid x)$，其中 $\theta$ 是模型参数，$x$ 是输入，$y$ 是回答。它描述模型在相应规则下分配给字符串的概率；“回答符合任务要求”则是另一个事件。除非构造并验证了针对该事件的估计器，否则不能把两者直接相等。

实际 sampling 还可能使用经温度或截断调整后的分布。此时观察到的回答多样性同时受模型、prompt 与 decoding 控制。通过降低温度让输出更一致，无法独立证明模型获得了更多事实知识。相关机制复用[Decoding 与 Sampling](../../foundations/language-models/decoding-sampling.md)。

[Geng et al. (2024)](https://aclanthology.org/2024.naacl-long.366/ "文献引用") 将这些特性与语言自报置信度（Verbal Confidence）等能力一起讨论。LLM 的额外表达能力增加了可用信号，也增加了估计对象被混淆的机会：一段“我不确定”的文字、该段文字的概率和回答实际出错率仍需分别研究。

## 为具体任务确定目标

短事实问答可以关注答案含义分布；证据摘要可以关注某条主张是否得到来源支持；推理任务可以分别关注最终答案与推导是否有效。长回答则需要说明局部错误是否导致整体不合格，而不能把“多数句子正确”自动变成“回答正确”。

在模型保持低分歧却持续犯错时，需要检查系统性错误或证据不足；在多个正确答案导致高分歧时，需要检查任务本身的开放程度。这样的分析比把所有变化都命名为“模型不知道”更有用，也为选择 UQ 信号提供了依据。

## 相关笔记

- [认知与数据不确定性](epistemic-aleatoric.md)
- [不确定性与正确性](uncertainty-vs-correctness.md)

## 参考文献

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Azaria, A., Mitchell, T. (2023). *The Internal State of an LLM Knows When It’s Lying*. Findings of EMNLP, 967–976. [Paper](https://aclanthology.org/2023.findings-emnlp.68/)
- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
