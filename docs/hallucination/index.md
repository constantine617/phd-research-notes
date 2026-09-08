---
tags:
  - trustworthiness
  - hallucination
---

# 可信性与幻觉（Trustworthiness & Hallucination）

可信性是本知识库的长期研究主题。研究对象包括大语言模型（Large Language Model，LLM）和大型视觉语言模型（Large Vision-Language Model，LVLM），长期面向医疗应用中的可信生成与证据使用。它关心的不只是模型能答对多少题，还包括答案是否有依据、条件变化后行为是否稳定，以及失败能否被识别。美国国家标准与技术研究院（National Institute of Standards and Technology，NIST） 的风险管理框架把可信性放在具体使用情境下讨论，并强调多个要求之间的协调（Tabassi, 2023）。

## 先区分三个维度

- [事实性（Factuality）](factuality.md)：输出中的事实主张是否符合可核查的事实。
- [忠实性（Faithfulness）](faithfulness.md)：输出是否受到任务允许的输入与来源支持。
- [鲁棒性（Robustness）](robustness.md)：在合理的输入或评估条件变化下，模型能否维持符合要求的行为。

这三个维度相互关联，但不能互相替代。例如，来源只说某人去过伦敦，回答却说她出生于伦敦；即使后者碰巧真实，也不能据此认定回答忠实于来源。Maynez et al. (2020) 在摘要任务中明确讨论了这种区别。

## 本模块解决什么问题

[模型可信性](trustworthiness-overview.md) 先说明整体范围。幻觉是其中一类重要的失败表现，通常涉及错误、无依据或与证据不一致的生成内容；它的具体边界随任务和文献变化。格式错误、计算失误等不能不加区分地全部归入幻觉。

本模块分别讨论[幻觉定义](hallucination-definition.md)、[幻觉分类](hallucination-taxonomy.md)、[成因与失败表现](causes-failure-modes.md)、[检测](detection.md)和[缓解](mitigation.md)，并进一步界定[正确性（Correctness）与可靠性（Reliability）](reliability-correctness.md)。这些页面共同回答：判定的对象是什么、依据是什么，以及发现问题后可以采取什么行动。

不确定性量化（Uncertainty Quantification，UQ）是当前研究输出可靠性的主要技术路线之一。使用任何信号之前，都需要先定义它要识别的错误。长期研究空间还包括训练、缓解、跨模态对齐和医疗应用，因此本模块也为这些方向提供共同的概念基础。

## 参考文献（References）

- Tabassi, E. (2023). *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. NIST AI 100-1. [DOI](https://doi.org/10.6028/NIST.AI.100-1)
- Maynez, J., Narayan, S., Bohnet, B., McDonald, R. (2020). *On Faithfulness and Factuality in Abstractive Summarization*. ACL, 1906–1919. [Paper](https://aclanthology.org/2020.acl-main.173/)
