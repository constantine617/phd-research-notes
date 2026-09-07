---
tags:
  - trustworthiness
  - reliability
---

# 模型可信性（Model Trustworthiness）

一个模型在测试集上表现很好，仍可能在实际使用中给出没有依据的回答。可信性关注的是：在明确的用途与条件下，我们有什么理由依赖模型，以及这些理由能否经受检验。它不是模型自称“有把握”，也不是使用者主观上愿不愿意相信它。

## 一个覆盖多个要求的概念

可信性是一个统摄性概念。不同人工智能（Artificial Intelligence，AI）框架、自然语言处理（Natural Language Processing，NLP）研究和应用领域，选取的维度、命名与层级并不完全一致。

NIST AI RMF 1.0 列出的特征包括有效与可靠、安全、信息安全与韧性、可问责与透明、可解释与可理解、隐私增强，以及对有害偏差的管理。该框架强调，这些特征需要结合使用情境与相互之间的权衡来判断（Tabassi, 2023）。这是一个机构提出的风险管理框架，不是所有研究都必须采用的唯一分类。

安全性（Safety）、公平性（Fairness）、隐私（Privacy）、透明性（Transparency）和可解释性（Interpretability）都属于更宽的讨论范围。本知识库当前集中在生成输出与证据之间的关系，但这个取舍不意味着其余维度不重要，也不意味着仅检查生成内容就完成了整个系统的可信性评估。

## 本知识库的工作范围

这里采用一个实用工作定义：**模型可信性关注模型在指定任务和使用条件下，能否以可检验的依据满足相关要求，并使其能力边界与失败风险可以被评估。** 这是内容组织上的约定，不是通用形式化定义，也不保证一次评估能覆盖所有未来情境。

当前主要区分以下问题：

| 关注点 | 首先要问的问题 |
|---|---|
| [事实性（Factuality）](factuality.md) | 输出的事实主张与可核查事实一致吗？ |
| [忠实性（Faithfulness）](faithfulness.md) | 输出是否被任务允许的来源支持？ |
| [鲁棒性（Robustness）](robustness.md) | 合理变化发生后，行为还能满足要求吗？ |
| 幻觉（Hallucination） | 是否生成了当前任务定义下的错误或无依据内容？ |
| 输出可靠性（Output Reliability） | 满足要求的行为能否稳定出现，潜在失败能否被识别？ |

这些不是五个互斥类别。一次错误回答可以同时违反事实性和忠实性；同一种错误也可能只在某些 prompt 写法下出现，进一步暴露鲁棒性问题。记录多个维度，有助于避免用一个总分掩盖不同问题。

## 为什么高分还不够

benchmark 的成绩只描述某个测试分布、任务定义和评分规则下的表现。Ribeiro et al. (2020) 的 CheckList 在多个 NLP 任务中揭示了传统准确率评估没有充分暴露的行为问题，例如否定、实体替换等语言现象的处理失败。该结果支持增加行为测试，不代表所有高分模型都会以同样方式失败。

生成任务还要检查评分究竟奖励什么。Maynez et al. (2020) 在抽象式摘要中发现，文本可以流畅、与主题相关，并获得较好的词面重合分数，却仍包含不受输入文档支持的内容。因此，参考答案相似度与证据支持关系需要分别判断。

例如，一个模型在熟悉领域经常答对，但遇到新的专业术语仍以确定口吻回答。这里的置信度（Confidence）表达、事实判断和领域适用性是三个待检验对象。这个例子是概念说明；仅凭自信语气，既不能确认模型正确，也不能断言其内部确实掌握了相关知识。

## 幻觉只是其中一类问题

幻觉研究帮助我们识别生成内容中的无依据补充、捏造或证据冲突。摘要文献尤其关注来源支持关系（Maynez et al., 2020）。但一个回答即使没有捏造事实，也可能泄露隐私、对不同群体产生不公平结果，或无法在条件变化后维持表现。降低幻觉率因而只是可信性改进的一部分。

同样，事实都正确的回答仍可能没有完成用户要求。判断时应明确评估单位：某个事实主张、整段回答、模型在一组任务上的行为，或包含检索与人工复核的完整系统。不同单位不能直接共享同一个“可信”标签。

## 与研究方向的关系

本知识库长期研究大语言模型（Large Language Model，LLM）与大型视觉语言模型（Large Vision-Language Model，LVLM）的可信性，并面向医疗应用扩展。当前主要技术路线之一是不确定性量化（Uncertainty Quantification，UQ）：研究哪些信号有助于区分较可靠与可能失败的输出。

这条路线需要与明确的任务标签、证据要求和评估条件配合。之后可以比较信号在不同 prompt、模型和领域下的表现，再研究计算成本与失效案例。UQ 提供的是可检验的判断信号，不能单独承担事实核查、风险处置或临床适用性验证。

长期范围仍保留幻觉缓解、模型训练、多模态表征（Multimodal Representation）与跨模态对齐（Cross-modal Alignment）。这些方向可以改善生成过程或证据使用方式，与输出可靠性的估计相互补充；具体研究问题仍需要通过基线比较与错误分析逐步收敛。

## 参考文献（References）

- Tabassi, E. (2023). *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. NIST AI 100-1. [DOI](https://doi.org/10.6028/NIST.AI.100-1)
- Ribeiro, M. T., Wu, T., Guestrin, C., Singh, S. (2020). *Beyond Accuracy: Behavioral Testing of NLP Models with CheckList*. ACL, 4902–4912. [Paper](https://aclanthology.org/2020.acl-main.442/)
- Maynez, J., Narayan, S., Bohnet, B., McDonald, R. (2020). *On Faithfulness and Factuality in Abstractive Summarization*. ACL, 1906–1919. [Paper](https://aclanthology.org/2020.acl-main.173/)
