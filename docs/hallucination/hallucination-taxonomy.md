---
tags:
  - hallucination
  - taxonomy
---

# 幻觉分类

不同任务、综述和模型类型使用不同的幻觉分类。分类可能按事实参照、证据来源、错误内容或生成任务组织；这些分类维度回答的问题不同，不能拼成一句“幻觉一共分为几类”。

本页先说明文献分类的出处，再给出适合本知识库的按多个分类维度分别记录的方式。使用前应先确定[幻觉的定义](hallucination-definition.md)。分类帮助描述已识别的问题，本身不能证明一个输出有错，也不能证明错误的成因。

## 分类维度一：生成内容与什么不一致？

[事实性（Factuality）](factuality.md)关注与可核查事实的关系；[忠实性（Faithfulness）](faithfulness.md)关注与指定来源的关系。一次输出可以同时违反二者，也可能只违反一个维度。

[Huang et al. (2025)](https://doi.org/10.1145/3703155 "文献引用") 的 LLM 综述区分事实性幻觉与忠实性幻觉。前者包含事实矛盾和捏造，后者包括指令、上下文和逻辑不一致。这里应保留两个限制：该分类是综述作者提出的组织方式；其中“忠实性”的范围比本知识库的来源支持视角更宽。

例如，正确回答了一个问题却没有执行“请翻译这个问题”的指令，在该综述中可以归为指令不一致。本知识库默认先将这种情况记录为指令执行错误；只有同时产生符合本工作定义的内容问题时，才增加相应幻觉标签。二者是范围选择不同，不能通过改名消除。

### Intrinsic 与 extrinsic 的任务来源

内在幻觉（Intrinsic Hallucination）与外在幻觉（Extrinsic Hallucination）是来源约束生成（Constrained Generation）文献中的常见区分。[Maynez et al. (2020)](https://aclanthology.org/2020.acl-main.173/ "文献引用") 是这里直接核查的一项早期摘要研究；本页不作“该论文最早创造术语”的历史断言。

在其 §2.1 中，intrinsic 侧重利用文档已有内容却错误地综合或表达，extrinsic 侧重加入文档没有引入的信息。原文首先把不受来源支持的片段识别出来，再讨论其类型。**这里的内外针对内容与来源的关系，不是模型内部信号与外部检索的区别。**

[Ji et al. (2023)](https://doi.org/10.1145/3571730 "文献引用") 的综述用更简洁的判定关系概括：intrinsic 为与来源冲突，extrinsic 为既不能由来源支持、也不能由来源反驳。这个概括与 Maynez 对“错误综合来源内容”的原始表述不完全相同，尤其是“误表述”未必总能找到明确反证。因此，实验必须注明采用哪一版本。

若来源明确说 Alice 出生于巴黎，输出说她出生于伦敦，属于来源冲突的例子；若来源只说她访问了巴黎，输出补充其出生地，则通常是来源不足以判断。第二条输出可能被外部事实证实，但这不会自动让它受到原来源支持。

## 分类维度二：依据什么证据核查（Evidence-based Verification）？

世界或领域知识、给定上下文、检索证据与视觉证据，可以作为不同的核查参照。[Ji et al. (2023)](https://doi.org/10.1145/3571730 "文献引用") 对自然语言生成任务的比较说明，来源在摘要、问答、对话等任务中并不相同；[Rohrbach et al. (2018)](https://aclanthology.org/D18-1437/ "文献引用") 的图像描述工作则直接以图中对象为参照。

这几个证据类别允许交叉。检索到的领域文档既是外部知识载体，也成为本次生成的输入上下文。应记录它在实验中的角色，而不是按存储位置强行互斥分类。

如果两个来源发生冲突，可以分别记录每个来源对同一主张的支持或反驳，并保留来源时间和权威性。不能因为某个检索结果支持输出，就把“来源有分歧”改写成“事实已经证实”。

## 分类维度三：生成内容具体错在哪里？

[Pagnoni et al. (2021)](https://aclanthology.org/2021.naacl-main.383/ "文献引用") 的 FRANK 在抽象式摘要中从语义框架、篇章关系和内容可验证性组织错误。其直接支持的类型包括谓词或关系、实体及属性、时间地点等情境、指代、篇章连接以及来源外信息。它评估的是摘要中的事实一致性，不能直接当成所有 LLM 任务的完整分类。

| 内容观察角度 | 可能的错误表现 | 来源与边界 |
|---|---|---|
| 实体（Entity）与属性（Attribute） | 将人物替换为另一人，或把正确属性分配给错误对象 | FRANK 的实体错误含参与者及其属性，也涵盖角色互换 |
| 关系（Relation）与事件（Event） | 把“参加”改成“获胜”，把事件谓词表达错 | FRANK 的谓词与语义框架错误 |
| 时间、地点与事件连接 | 改错发生时间，颠倒先后，添加错误因果连接 | FRANK 的情境与篇章连接错误 |
| 视觉对象存在性 | 描述图中没有的对象 | [Rohrbach et al. (2018)](https://aclanthology.org/D18-1437/ "文献引用") 的物体幻觉，范围不等于全部视觉错误 |
| 引文（Citation）与参考文献 | 捏造不存在的文献，或写错真实文献的元数据 | [Walters and Wilder (2023)](https://www.nature.com/articles/s41598-023-41032-5 "文献引用") 区分虚构条目与真实条目的实质错误 |

数值形式不是自动独立于这些类别。例如，真实论文的年份、卷号或页码写错，是文献元数据错误；[Walters and Wilder (2023)](https://www.nature.com/articles/s41598-023-41032-5 "文献引用") 直接核查了这些字段。若在其他任务中加入“数值错误”标签，需要说明数值扮演的是数量、日期、度量还是推理结果，不能把所有数字不匹配都视为同一种幻觉。

FRANK 还收录了严重语法问题，以表示语句已难以理解和核查。这是其标注体系的一部分，不意味着本知识库把所有语法错误都纳入幻觉。类似地，查不到某条引用只是检索结果；声称引用被捏造，需要更充分的身份核验，不能把一次未命中当成不存在的证明。

## 分类维度四：属于什么任务，涉及哪些模态？

问答主要检查回答的事实、证据和问题匹配；摘要还要考虑来源压缩与信息选择；对话要区分对话历史、外部资料和允许的创造性回应。长篇生成往往需要拆分主张，避免一个整体标签掩盖局部问题。[Ji et al. (2023)](https://doi.org/10.1145/3571730 "文献引用") 提供了这些任务差异的综述背景。

多模态生成可能同时依赖图像与文本。若图像支持对象存在，而文本错误地给出其身份，不能用一个“图文不一致”标签掩盖究竟哪个来源被采用、哪个主张失败。[Rohrbach et al. (2018)](https://aclanthology.org/D18-1437/ "文献引用") 的对象存在性检查提供一个明确子任务，但不覆盖全部跨模态推断。

## 本知识库的工作分类

下面是基于上述文献建立的记录框架，**不是新的通用分类标准**。每个样本可以在多个轴上有标签；同一输出中的不同主张也可以有不同标签。

| 记录轴 | 建议记录内容 |
|---|---|
| 判定关系 | 事实冲突、来源冲突、来源未支持；允许“无法判定” |
| 证据参照 | 世界或领域资料、给定上下文、检索文档、图像；允许多来源 |
| 内容类型 | 实体、关系或事件、属性、时空、引用等；按任务增补并说明规则 |
| 任务与模态 | 问答、摘要、对话、长篇生成；文本、图像或组合 |
| 标注粒度 | 整体回答、句子、主张、片段，以及局部到整体的聚合方法 |

例如，来源记载某机构在 2021 年建立，回答写成 2020 年，可以同时记录“来源冲突—给定文档—时间属性—摘要—主张级（Claim-level）”。如果权威外部记录也确认 2021 年，还可增加事实冲突标签；若来源本身错误，就需要另行处理外部事实状态。

这样记录后，才能比较不同检测信号擅长识别哪类错误。类别之间的共现，不等于它们有共同成因。解释为什么发生这些现象，应转到[幻觉成因与失败表现](causes-failure-modes.md)，并使用独立的机制证据。

## 参考文献

- Maynez, J., Narayan, S., Bohnet, B., McDonald, R. (2020). *On Faithfulness and Factuality in Abstractive Summarization*. ACL, 1906–1919. [Paper](https://aclanthology.org/2020.acl-main.173/)
- Ji, Z., Lee, N., Frieske, R., Yu, T., Su, D., Xu, Y., Ishii, E., Bang, Y. J., Madotto, A., Fung, P. (2023). *Survey of Hallucination in Natural Language Generation*. ACM Computing Surveys. [DOI](https://doi.org/10.1145/3571730) · [作者预印本 v5](https://arxiv.org/abs/2202.03629v5)
- Huang, L., Yu, W., Ma, W., Zhong, W., Feng, Z., Wang, H., Chen, Q., Peng, W., Feng, X., Qin, B., Liu, T. (2025). *A Survey on Hallucination in Large Language Models: Principles, Taxonomy, Challenges, and Open Questions*. ACM Transactions on Information Systems. [DOI](https://doi.org/10.1145/3703155) · [作者预印本 v2](https://arxiv.org/abs/2311.05232v2)
- Pagnoni, A., Balachandran, V., Tsvetkov, Y. (2021). *Understanding Factuality in Abstractive Summarization with FRANK: A Benchmark for Factuality Metrics*. NAACL-HLT, 4812–4829. [Paper](https://aclanthology.org/2021.naacl-main.383/)
- Rohrbach, A., Hendricks, L. A., Burns, K., Darrell, T., Saenko, K. (2018). *Object Hallucination in Image Captioning*. EMNLP, 4035–4045. [Paper](https://aclanthology.org/D18-1437/)
- Walters, W. H., Wilder, E. I. (2023). *Fabrication and errors in the bibliographic citations generated by ChatGPT*. Scientific Reports, 13, 14045. [Paper](https://www.nature.com/articles/s41598-023-41032-5)
