---
tags:
  - uq
  - evidence
---

# 证据感知不确定性量化（Evidence-aware Uncertainty Quantification，UQ）

证据感知 UQ 把模型之外的可检查信息纳入估计，研究回答在什么证据条件下更可信。对于大语言模型（Large Language Model，LLM），外部证据（External Evidence）可以是来源文档、检索文本、知识库记录或核查结果。本页建立其与内部信号的关系，并作为通向多模态研究的概念接口。

## 内部信号与证据回答不同问题

模型概率、生成一致性和 hidden state 主要观察模型自身的状态与行为。证据可以提供事实内容、时间版本或任务允许的来源。模型可能对一个错误事实非常一致，外部文档却能指出矛盾；也可能内部概率较低，但来源明确支持一个罕见事实。

检索增强生成（Retrieval-Augmented Generation，RAG）将检索内容加入生成条件。Lewis et al. (2020) 的原始工作结合参数与非参数记忆，展示检索参与生成的一类方法。RAG 本身不等于 UQ：取得更多上下文可能改善回答，但是否形成有效的不确定性估计，还取决于如何构造和验证分数。

## 证据条件下估计哪个事件

证据条件置信度（Evidence-conditioned Confidence）需要明确被条件化的信息。对于回答 $y$、输入 $x$ 和证据 $e$，可以询问“主张是否被 $e$ 支持”，也可以询问“综合这些信息后，主张是否符合世界事实”。这两个事件不同；即使来源内部明确支持，来源本身也可能错误。

[忠实性（Faithfulness）](../../hallucination/faithfulness.md)关注任务允许的来源支持；[事实性（Factuality）](../../hallucination/factuality.md)关注可核查事实。一个系统应说明它预测哪种性质，并保留与另一种性质分离的评价。

Min et al. (2023) 的 FActScore 将长回答拆成原子事实，再评价指定知识来源是否支持。它是事实精确程度的评价框架，不是自动给出回答正确概率的校准器。本页借用其主张与来源对应思路，作为构造证据特征的例子。

## 哪些量可以作为证据信号

检索相关性反映文档与查询是否有关；证据定位或支持分数（Grounding Score）尝试建立具体主张与来源片段的联系；验证器（Verifier）则可以输出支持、矛盾或信息不足等判断。三类信号可以结合，但数值含义不可互换。

一个高检索分数可能只说明主题相近，并没有覆盖回答中的日期。一个蕴含判断也依赖核查器模型、上下文长度和来源处理方式。若最后需要概率表达，可以在明确标签上学习映射，并独立检查[置信度校准（Confidence Calibration）](../../calibration/index.md)。

较清楚的工作过程是先固定主张，再关联候选证据，检查来源质量和支持关系，最后结合内部信号给出分数或未决状态。如果证据被用来改写回答，应对改写后的主张重新建立对应，而不是沿用旧回答的高分。

## 证据质量如何改变解释

| 证据状态 | 不能直接作出的推断 | 更合适的记录方式 |
|---|---|---|
| 不完整 | 没检索到支持，所以主张必错 | 区分未覆盖与明确反驳 |
| 不相关 | 查询相似，所以主张被支持 | 关联到具体主张和片段 |
| 相互矛盾 | 来源多的一方必然正确 | 保留冲突，比较来源独立性和依据 |
| 已过期 | 历史记录仍适用于当前问题 | 标记时间、版本及适用范围 |
| 内容错误 | 来源语气确定，所以事实可靠 | 核查来源本身及其他独立依据 |

这张表是知识库的证据处理框架，不是某一论文的固定 taxonomy。证据数量也不能简单代表独立支持强度：多个页面可能复制同一条错误消息。若生成器与验证器来自同一个模型家族，它们还可能共享偏差。

缺少证据与有证据反驳，通常应保留不同状态。“信息不足”既不是支持，也不是矛盾；若实验需要把它映射成二元标签，应说明规则及其后果。否则所谓错误检测可能主要在检测知识库覆盖范围。

## 访问与成本

文本接口可以支持外部检索与核查，不必访问目标模型内部。但系统仍需获取、读取和处理来源，可能增加检索、重排、验证生成与文档存储开销。主张越多，核查越细，成本也可能更高，详见[高效 UQ](../../efficient-uq/index.md)。

评估时还要防止把答案键当作实际可取得的检索证据，或者用同一个核查器既生成目标标签又证明自己有效。证据依赖的估计器应接受独立来源或人工抽查，才能区分覆盖不足、模型错误与验证器错误。

## 与后续研究的接口

视觉证据（Visual Evidence）与多模态证据（Multimodal Evidence）可以来自图像区域、文本描述及跨模态对应。已有的[视觉定位（Visual Grounding）](../../foundations/vision-language-models/visual-grounding.md)提供基础概念，但定位分数不自动等于主张正确概率。

当前阶段只保留这一桥梁：先明确证据是什么、支持哪个主张，以及质量如何影响信号。完整多模态不确定性与医疗任务还需要各自的来源、标签和验证条件，不能由纯文本研究结果直接推出。

## 相关笔记（Related Notes）

- [幻觉检测](../../hallucination/detection.md)
- [表征方法](../../representation-uq/index.md)

## 参考文献（References）

- Lewis, P., Perez, E., Piktus, A., et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. Advances in Neural Information Processing Systems, 33, 9459–9474. [Paper](https://papers.nips.cc/paper_files/paper/2020/hash/6b493230205f780e1bc26945df7481e5-Abstract.html)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
