---
tags:
  - hallucination
  - detection
---

# 幻觉检测（Hallucination Detection）

幻觉检测将生成内容及可用信息转成关于潜在问题的判断。它可以指出哪条主张缺乏支持，也可以只输出一个风险分数。两种输出的含义不同，必须先固定[幻觉的操作性定义](hallucination-definition.md)，再比较检测方法。

本页建立方法空间，关注各类信号需要什么信息、能支持什么判断，以及在哪里可能失败。下面的类别可以组合，并不是互斥或穷尽的分类。

## 检测问题的输入与输出

输入首先包括 prompt、任务说明、给定来源与生成回答。根据模型访问条件，还可能获得 token 概率、多次生成结果、检索上下文、视觉证据或内部表征（Hidden Representations）。其中一部分信息在只有文本接口的 LLM 上可能不可得。

输出可以是二元标签、支持状态、幻觉分数，或置信度与不确定性分数。一个连续分数不会自动成为错误概率；还需要定义方向、单位、聚合方式及转为行动时使用的阈值。一个回答级（Response-level）分数也不等于已经定位了错误主张。

方法比较时应固定可访问的信息。只读取一次回答的检测器，与调用模型多次并检索外部资料的核查流程，具有不同的计算预算和证据条件，不能只按一个检测分数评价。

## 检测粒度

| 粒度 | 主要用途 | 需要保留的区别 |
|---|---|---|
| 回答级 | 决定整段回答是否需复核 | 一处错误与多处错误如何聚合 |
| 句子级（Sentence-level） | 标出问题句子 | 一句可能同时含有正确与错误信息 |
| 主张级（Claim-level） | 分别核查可验证陈述 | 拆分不能丢失否定、条件或指代 |
| token / 片段级（Span-level） | 定位可疑词语或文本范围 | 高风险位置不必等于最小事实错误单位 |

[Manakul et al. (2023)](https://aclanthology.org/2023.emnlp-main.557/ "文献引用") 的 SelfCheckGPT 主要评价句子并形成段落级判断。[Min et al. (2023)](https://aclanthology.org/2023.emnlp-main.741/ "文献引用") 的 FActScore 则将长回答拆成原子事实（Atomic Fact），再评估指定知识来源支持的比例。后者说明了细粒度核查的价值，但该比例本身不能衡量任务是否完整完成。

## 概率、不确定性与内部信号

概率信号可以来自模型对输出的分配，UQ 也可以利用多次生成的变化。它们把“可能失败”转成可比较的连续信号，但概率集中只能说明模型偏好某种输出，不能单独提供外部事实证据。

[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 在语义层面研究生成变化，用于识别其定义的 confabulations，即具有随机性且错误的一类生成。这里保留该英文原词以避免与全部幻觉混同。原论文明确区分持续犯同一种错误等情况；不能把其检测范围扩写成所有幻觉都能识别。

内部信息还可以用于训练估计器。[Kadavath et al. (2022)](https://arxiv.org/abs/2207.05221 "文献引用") 研究了回答自评，以及带附加预测头的“能否回答正确”估计。问题级能力估计与某条已生成回答的正确性不是同一个目标；内部表征的可预测性也不自动证明已经找到错误的因果机制。本阶段只保留这些信号入口。

## 多次生成的一致性

一致性检测（Consistency-based Detection）比较重复生成是否表达相容事实。SelfCheckGPT 用同一模型的 sampling 结果检查原回答，在不依赖外部数据库的条件下形成检测信号（[Manakul et al., 2023](https://aclanthology.org/2023.emnlp-main.557/ "文献引用")）。

比较可以关注语义一致、同一主张能否被其他回答支持，或不同推理路径是否得到相同结论。有效实现仍要区别同义改写与真实冲突。多次答案相同可能源于共同误解；多条推理链也可能共享错误前提，因此一致性只能提供信号，不能作为事实真值的替代。

## 基于证据的核查与蕴含判断

证据核查把输出主张与给定文档、外部知识或检索结果对照。FActScore 的自动估计器结合检索与语言模型核查，是这一路线的具体例子；其适用范围和估计误差需要与人类标注比较（[Min et al., 2023](https://aclanthology.org/2023.emnlp-main.741/ "文献引用")）。

事实核查常包含寻找证据、选择相关片段和判断关系三个问题。[Thorne et al. (2018)](https://aclanthology.org/N18-1074/ "文献引用") 的 FEVER 区分支持、反驳与证据不足，并要求支持或反驳判断附带证据。没有找到证据，与找到了反证，是不同结果。

NLI 可以检查证据是否蕴含或反驳主张，但这是给定前提下的模型判断。检索错了、来源过时了，或推断模型理解错了，都可能导致错误核查结果。FEVER 的错误分析也将检索失败与推断判断失败分开。

视觉证据核查同样要明确对象、属性或关系的参照。仅确认文本之间一致，不能替代图像检查；视觉核查还需要适合任务的标注或专业判断。这是证据路线向多模态任务扩展时的要求，不是某个文本验证器已经具备的能力。

## 模型自检（Self-verification）、外部验证器与人工评价

模型自检可以让同一模型再次检查，也可以调用另一个验证器或评判模型（Judge）。更换角色名称不意味着判断者不会犯错；相同模型可能重复原有错误，不同模型也可能共享知识偏差。

[Huang et al. (2024)](https://proceedings.iclr.cc/paper_files/paper/2024/hash/8b4add8b0aa8749d80a34ca5d941c355-Abstract-Conference.html "文献引用") 在没有外部反馈的推理自纠错设置中观察到改进困难，部分情况还会退步。这是特定任务和反馈条件下的证据，不能被写成“任何自检都无效”，也不能与带独立证据的核查混为一谈。

人工评价能够依据明确指南核查证据与复杂语义，适合建立高质量标签和分析边界案例，但成本高，也会发生分歧。FEVER 报告了标注一致性；FActScore 同时指出逐条验证长回答的时间成本（[Thorne et al., 2018](https://aclanthology.org/N18-1074/ "文献引用"); [Min et al., 2023](https://aclanthology.org/2023.emnlp-main.741/ "文献引用")）。任务指南、标注者一致性和分歧裁决，都是标签质量的一部分。

## 检测结果怎样进入系统

检测回答的是“哪里或多大程度可能有问题”；[幻觉缓解（Hallucination Mitigation）](mitigation.md)则改变生成过程或系统处置。识别到错误，并不表示已经修复了它。是否触发拒答或重新生成，由应用策略决定；还需考虑覆盖率（Coverage），即系统实际回答的输入比例。

```text
任务定义、来源与生成回答
→ 检测标签、分数及可用证据
→ 依据风险、覆盖率与成本制定处置
→ 交付、拒答、重新生成、改用其他模型或人工复核
→ 对实际交付的最终回答重新评价
```

例如，检测器触发重新生成后，新回答可能仍错，也可能新增不同错误。应分别测量检测质量与最终系统质量，而不能仅凭触发了核查就宣布缓解成功。

与 UQ 的连接也在这里：先定义参考真值、[错误类型](hallucination-taxonomy.md)和检测单位，再评价信号能否排序风险、如何解释以及需要多少成本。置信度、正确性和可靠性（Reliability）的进一步区别，见[正确性与可靠性](reliability-correctness.md)。

## 参考文献

- Manakul, P., Liusie, A., Gales, M. J. F. (2023). *SelfCheckGPT: Zero-Resource Black-Box Hallucination Detection for Generative Large Language Models*. EMNLP, 9004–9017. [Paper](https://aclanthology.org/2023.emnlp-main.557/)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
- Kadavath, S., Conerly, T., Askell, A., et al. (2022). *Language Models (Mostly) Know What They Know*. arXiv:2207.05221. [Paper](https://arxiv.org/abs/2207.05221)
- Thorne, J., Vlachos, A., Christodoulopoulos, C., Mittal, A. (2018). *FEVER: a Large-scale Dataset for Fact Extraction and VERification*. NAACL-HLT, 809–819. [Paper](https://aclanthology.org/N18-1074/)
- Huang, J., Chen, X., Mishra, S., Zheng, H. S., Yu, A. W., Song, X., Zhou, D. (2024). *Large Language Models Cannot Self-Correct Reasoning Yet*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/8b4add8b0aa8749d80a34ca5d941c355-Abstract-Conference.html)
