---
tags:
  - uq
---

# UQ 总览

在 LLM 中，UQ 首先要确定“对什么不确定”。同一个回答可以词语选择稳定、事实依据不足，也可以措辞多样而含义完全一致。估计器输出的数值只有与目标、观察条件及用途一起说明，才有可解释的含义。[Geng et al. (2024)](https://aclanthology.org/2024.naacl-long.366/ "文献引用") 将生成任务中的置信度估计与校准分别讨论，这一区分也是本页的出发点。

## 先固定目标与条件

设输入为 $x$，已生成回答为 $y$。研究者可以估计这个具体回答是否满足任务要求，也可以估计再次运行模型会产生多少种不同答案。前者面向已交付输出，后者面向生成分布。即使都使用多次 sampling，二者也不相同：其他样本可能一致支持某答案，却与当前回答不同。

[Kadavath et al. (2022)](https://arxiv.org/abs/2207.05221 "文献引用") 区分了对具体回答的自我评价与问题层面的可回答性估计。后者依赖模型将如何作答；如果更换 decoding 策略、允许检索或增加推理预算，所问的事件已经改变。研究记录应同时保存任务、回答生成规则、分数取得时点和允许使用的信息。

把“正确”当作目标时，要复用[任务正确性的定义](../hallucination/reliability-correctness.md)。把“来源支持”当作目标时，则要说明允许引用哪些来源、是否要求完整覆盖。可靠性是更宽的系统要求，单一分数通常只观察其中一部分。

## 粒度决定解释范围

| 粒度 | 常见对象 | 不能直接外推的结论 |
|---|---|---|
| token 层 | 给定前缀后的下一 token 选择 | 一个位置稳定，不等于完整回答正确 |
| 序列层 | 某段回答的概率或特征聚合 | 长度或文风差异可能主导分数 |
| 语义层 | 若干表达共同代表的答案含义 | 含义集中也可能是共同误解 |
| 主张层 | 回答中的单个可核查陈述 | 多个局部分数不能随意合成“全部正确概率” |
| 分布层 | 指定输入下的预测分布，或输入群体上的分数分布 | 条件预测分布与总体漂移是两类观察 |

语义等价（Semantic Equivalence）使表达与含义之间出现多对一关系。[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 据此将概率质量聚合到含义类别。主张层面还涉及拆分规则：一个包含两个事实的句子，不能仅因句子边界清晰就视为不可再分的单一事件。

## 信号从哪里来

概率路线使用 token 概率、序列 log-probability 或熵，获取成本取决于接口是否已经返回所需信息。Sampling 路线观察回答的一致与分歧，需要说明比较规则和样本数量。语言自报路线让模型输出把握程度，它本身仍是一次语言生成。

内部表征路线从 hidden state 提取特征，再通过探针（Probe）或距离函数构造分数。证据路线引入来源文本、检索结果或验证器（Verifier）输出，补充仅靠模型自身难以获得的信息。这些路线可以组合；组合是否有效，需要验证新增信号是否带来独立信息，而不能只比较更复杂的方法名称。

## 分数如何进入决策

不确定性可以用于错误检测、[幻觉检测](../hallucination/detection.md)、拒答（Abstention）、模型路由（Routing）与人工复核（Human Review）。例如，一个低成本分数可先筛选疑难回答，再决定是否追加检索。这样的设计同时改变了成本与最终交付的回答群体。

选择性预测（Selective Prediction）研究只对部分输入作答时的风险与覆盖率关系（[Geifman and El-Yaniv, 2017](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html "文献引用")）。将其用于 LLM 时，需要重新定义拒答、部分作答和回答合格标准。分类场景的保证不能直接迁移到任意开放生成任务。

如果需要向用户呈现“八成把握”，还需[置信度校准（Confidence Calibration）](../calibration/index.md)。善于排序的分数可能没有概率含义；校准后的群体频率匹配也不保证每条回答。校准器只能在已经说明的目标和数据条件下被评价。

## 当前研究中的比较单位

本知识库把一次完整的 UQ 方案视为“目标、信息、估计器、决策用途”的组合。比较时先保持前两项可比，再讨论信号质量和成本。例如，同样预测主张是否被文档支持，概率分数与证据核查可以形成有意义的比较；一个预测语言多样性、另一个预测世界事实的实验，则需要分别解释。

当前 PhD 以 UQ 为主要技术路线，关注检测、校准、效率和表征。长期可信性研究还包含生成质量、鲁棒性及证据使用等要求；本阶段的方法知识为这些问题提供工具，而非替代其定义。

## 相关笔记

- [UQ 分类](uq-taxonomy.md)
- [置信度与不确定性](foundations/confidence-vs-uncertainty.md)

## 参考文献

- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
- Kadavath, S., Conerly, T., Askell, A., et al. (2022). *Language Models (Mostly) Know What They Know*. arXiv:2207.05221. [Paper](https://arxiv.org/abs/2207.05221)
- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Geifman, Y., El-Yaniv, R. (2017). *Selective Classification for Deep Neural Networks*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html)
