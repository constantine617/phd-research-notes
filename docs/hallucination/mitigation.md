---
tags:
  - hallucination
  - mitigation
---

# 幻觉缓解（Hallucination Mitigation）

幻觉缓解通过改变数据、训练、生成或核查流程，减少任务定义下的内容问题，或限制有问题的回答被直接交付。它需要说明改进目标，而不是把任何形式的“更谨慎”都算成进步。

减少幻觉频率、提高事实性（Factuality）、提高忠实性（Faithfulness）、增加证据使用，以及支持拒答（Abstention），是相互关联但不同的目标。模型也可能通过少回答来减少错误，因此最终评价要同时考虑答了什么、答对多少和放弃了多少任务。

## 干预可以发生在哪个阶段

| 阶段 | 可以改变的对象 | 需要另行验证的结果 |
|---|---|---|
| 数据与知识准备 | 来源质量、时效性、标注与领域覆盖 | 数据变化是否减少目标错误 |
| 训练 | 示范、偏好、事实或证据相关目标 | 目标行为是否学到，其他能力是否受损 |
| inference 输入准备 | 检索、证据选择与上下文组织 | 相关证据是否可获得、是否进入输入 |
| 生成过程 | decoding、输出约束、拒答与重新生成 | 最终内容是否更可靠，任务完成度如何 |
| 生成后核查 | 事实检查、修订、过滤与人工复核 | 错误是否实际修复，是否新增问题 |

这是根据 Huang et al. (2025) 对数据、训练与 inference 方法的综述整理的工作视角。阶段可以交叉，例如检索器可以在训练时优化，也可以在 inference 时替换知识库；生成后核查还可能开启新一轮生成。

## 数据与知识

改进来源质量、修正明显错误、保留领域条件，以及更新过时知识，都可以成为干预方向。对于需要依据材料作答的训练样本，还要检查参考答案是否确实被输入支持，避免把无依据补充当成合格示范。Huang et al. (2025) 将这些问题放在数据相关缓解的范围内讨论。

这些措施需要通过对照验证。增加专业文本，不保证模型对该领域每个问题都可靠；增加新数据，也不等于覆盖全部低频知识。应针对[成因与失败表现](causes-failure-modes.md)中提出的假设，检查干预究竟改变了哪些错误类型。

## 训练阶段

监督 fine-tuning 可以提供任务示范；偏好训练可以鼓励有依据、保留限定或适当拒答的回答。事实性相关目标也可以把主张核查结果纳入学习，但前提是训练信号本身有足够质量，且与最终评价要求一致。

Ouyang et al. (2022) 将示范数据与人类偏好反馈用于指令对齐，在所测任务中报告了真实性及部分来源约束生成行为的改进。原论文同时指出模型仍会犯错。该证据支持特定训练流程的有效性，不能推出“任何偏好训练都能消除幻觉”。

多模态训练还可以针对图文关系、细粒度证据或回答依据施加约束。Huang et al. (2025) 将大型视觉语言模型（Large Vision-Language Model，LVLM）的幻觉作为相关扩展方向。这里保留跨模态对齐（Cross-modal Alignment）的研究接口，不预设哪种训练目标已经解决视觉或医疗生成的全部可靠性问题。

## 检索与外部证据

检索增强生成（Retrieval-Augmented Generation，RAG）在生成时引入可访问的外部知识。Lewis et al. (2020) 的原始 RAG 工作结合参数化生成模型与非参数化文档索引，通过检索相关文档支持知识密集任务。它为知识更新和来源检查提供了具体模型方案，不应泛称为“首次使用外部知识的生成方法”。

RAG 提供证据机会，**不保证事实性或忠实性**。至少要分别检查：

- 检索结果是否相关，关键证据是否遗漏。
- 文档是否可靠、过时，或与其他文档冲突。
- 模型是否正确理解并有效使用这些材料。
- 输出的每条重要主张是否真的被引用内容支持。

Huang et al. (2025) 讨论了检索与利用环节的限制。因此，应把检索失败、证据质量问题和生成不忠实分开分析。即使最后一句附上了链接，也不能仅凭“有引用”判断主张有依据；链接指向的材料必须支持具体内容。

## Decoding 与生成控制

更保守的 decoding、约束生成（Constrained Generation）、重新生成，以及依据不确定性（Uncertainty）决定是否拒答，都属于可研究的控制方式。Huang et al. (2025) 汇总了相关方法，但各方法的收益依赖任务与模型，不能预设降低 temperature 一定提高事实性。

约束也只对被编码的要求起作用。限制输出为合法结构，主要解决结构要求；限制到一个候选集合，仍可能选中错误候选。如果目标是事实或证据支持，约束必须与这些目标相关，并另行检查其有效性。

拒答可以减少直接交付错误答案的机会，但“总是拒答”不是一个完成任务的系统。Geifman and El-Yaniv (2017) 在选择性分类（Selective Classification）中联合研究错误风险与覆盖率（Coverage），为这种权衡提供了清晰参照。将思路用于开放生成时，仍需重新定义合格回答、部分回答和拒答；原论文的分类保证不能直接移植过来。

## 生成后验证与修订

生成后可以进行事实核查、蕴含检查、自检、外部核查器验证或人工复核。发现问题之后，还需要决定删除、修订、补充证据还是不交付。仅添加“可能不准确”的提示，并没有修复原来的错误主张。

Dhuliawala et al. (2024) 的 Chain-of-Verification 先生成草稿，再形成核查问题、独立回答这些问题并修订最终输出。其独立核查步骤旨在减少对原草稿错误的重复依赖；论文报告了所测事实生成任务中的改善，同时明确仍会产生错误，并增加计算成本。

自检是否有效还与反馈条件有关。Huang et al. (2024) 在没有外部反馈的推理自纠错设置中发现了局限。这与前述事实核查流程不是相同实验，不能据此得出“自检总是有效”或“自检完全无效”。应分别检查任务、核查证据、模型版本与对照预算。

## 如何判断缓解是否成功

至少分别报告内容质量、实用性（Utility）与成本。实用性可能涉及是否回答核心问题、保留必要信息和满足用户要求；成本包括延迟、模型调用、生成 token、检索与人工时间。表达流畅度、输出多样性、覆盖率及拒答率也可能变化。

一项方法若删除所有难以核查的细节，事实性分数可能提高，但信息量和任务完成度可能下降。相反，多次生成与核查也可能改善质量，却不适合对延迟敏感的场景。这些是需要测量的权衡，不是每种方法都会出现同样幅度的副作用。

最后，[幻觉检测（Hallucination Detection）](detection.md)质量与缓解质量是两个问题。检测器是否正确发现风险，处置策略是否选择适当动作，以及最终回答是否更符合要求，应分别评价。比较生成前后的同一组问题时，还应保留被拒答样本，并复核修订后新增的主张，避免仅在剩余容易样本上宣布改进。

## 参考文献（References）

- Huang, L., Yu, W., Ma, W., Zhong, W., Feng, Z., Wang, H., Chen, Q., Peng, W., Feng, X., Qin, B., Liu, T. (2025). *A Survey on Hallucination in Large Language Models: Principles, Taxonomy, Challenges, and Open Questions*. ACM Transactions on Information Systems. [DOI](https://doi.org/10.1145/3703155) · [作者预印本 v2](https://arxiv.org/abs/2311.05232v2)
- Ouyang, L., Wu, J., Jiang, X., et al. (2022). *Training language models to follow instructions with human feedback*. Advances in Neural Information Processing Systems, 35, 27730–27744. [Paper](https://papers.nips.cc/paper_files/paper/2022/hash/b1efde53be364a73914f58805a001731-Abstract-Conference.html)
- Lewis, P., Perez, E., Piktus, A., et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. Advances in Neural Information Processing Systems, 33, 9459–9474. [Paper](https://papers.nips.cc/paper_files/paper/2020/hash/6b493230205f780e1bc26945df7481e5-Abstract.html)
- Geifman, Y., El-Yaniv, R. (2017). *Selective Classification for Deep Neural Networks*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html)
- Dhuliawala, S., Komeili, M., Xu, J., Raileanu, R., Li, X., Celikyilmaz, A., Weston, J. (2024). *Chain-of-Verification Reduces Hallucination in Large Language Models*. Findings of ACL, 3563–3578. [Paper](https://aclanthology.org/2024.findings-acl.212/)
- Huang, J., Chen, X., Mishra, S., Zheng, H. S., Yu, A. W., Song, X., Zhou, D. (2024). *Large Language Models Cannot Self-Correct Reasoning Yet*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/8b4add8b0aa8749d80a34ca5d941c355-Abstract-Conference.html)
