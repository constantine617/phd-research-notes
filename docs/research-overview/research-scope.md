---
tags:
  - research-overview
  - trustworthiness
  - hallucination
  - multimodal
  - medical
---

# 研究范围（Research Scope）

本博士研究的整体对象是可信的大语言模型（Large Language Model，LLM）与大型视觉语言模型（Large Vision-Language Model，LVLM）。长期重点是理解、评估和改善 LVLM 输出的可信性（Trustworthiness），并在医疗应用中考察相关方法。通用 LLM 研究可以提供方法起点，帮助分析语言生成中的可靠性问题，再进一步处理图像与文本共同参与时的问题。

## 可信性关注哪些问题

研究范围围绕输出内容及其依据展开，主要包括以下几个相互关联的方面：

- 事实性（Factuality）：输出是否符合可核查的事实与领域知识。
- 忠实性（Faithfulness）：输出中的陈述能否由给定文本、图像及任务允许的依据支持。
- 鲁棒性（Robustness）：当输入受到不改变任务含义的细微扰动时，模型能否维持合理表现，避免产生不应出现的错误。
- 模型可靠性（Model Reliability）：模型在给定条件和时间范围内能否持续满足任务要求，以及失败能否被识别和妥善处理；具体边界见[正确性与可靠性](../hallucination/reliability-correctness.md)。

事实性与忠实性需要分别考察。[Maynez et al. (2020)](https://aclanthology.org/2020.acl-main.173/) 在摘要任务中讨论了内容缺乏原文支持、但仍可能符合事实的情况。本页借用这一判断角度区分“是否符合事实”和“是否有输入依据”，具体任务仍需明确允许使用哪些信息。

幻觉（Hallucination）相关研究主要关注事实错误、缺乏输入支持和图文不一致等现象。研究时需要说明判断依据与标注规则，分别检查回答是否错误、是否缺乏支持，以及是否满足所采用的幻觉定义。这样才能明确某项实验实际测量了什么。

## 评估、缓解与模型改进

长期研究范围包括幻觉评估与检测（Hallucination Evaluation and Detection），以及幻觉缓解（Hallucination Mitigation）。前者关注怎样发现并描述问题，后者关注怎样减少问题输出的发生。

评估方向可以研究适合相关任务的 benchmark、标注方式和指标，分别考察事实性、忠实性与鲁棒性。需要关注总体表现，也需要分析错误与输入内容、领域知识及扰动条件之间的关系。是否需要建设新的 benchmark，应由现有资源能否回答研究问题来决定。

模型改进方向可以涉及多模态表征（Multimodal Representation）、跨模态对齐（Cross-modal Alignment）与训练方法。研究重点包括图文信息之间的对应关系、联合表征是否保留任务所需的信息，以及怎样利用不同模态的互补信息。自监督学习（Self-Supervised Learning）等训练方向也属于长期范围，具体技术选择保持开放。

外部知识（External Knowledge）同样可以用于探索幻觉的核查与缓解。这里关注的是证据如何参与判断或生成，以及这种参与是否有助于减少缺乏依据的内容。

## 当前技术主线的位置

不确定性量化（Uncertainty Quantification，UQ）是当前主要技术研究方向，切入点是输出可靠性估计及幻觉或错误检测。现阶段将重点放在可比较的不确定性信号上，便于通过实验识别更具体的问题。

UQ 是整体研究中的一条技术路线。多模态对齐、鲁棒性评估、模型训练和幻觉缓解仍保留各自的研究空间。后续若这些方向更适合解释或处理观察到的问题，可以据此调整研究重点。当前的具体安排见[当前研究重点（Current Focus）](current-focus.md)。

## 多模态与医疗应用的位置

多模态可信性（Multimodal Trustworthiness）是长期研究的核心部分。它既承接通用 LLM 中的输出可靠性问题，也关注视觉信息如何被表示、如何与文本对应，以及回答是否符合输入图像。由通用语言模型向 LVLM 扩展时，这些关系需要单独研究。

医疗应用（Medical Applications）是长期应用场景，可以围绕医学图像与报告理解、医学问答或报告生成等任务展开。研究重点是输出是否符合医学知识、是否忠实于具体输入，以及有关方法在这些任务中是否仍然有效。

研究可以先在通用任务中建立清楚的方法比较，再逐步进入多模态和医疗场景。具体模型、数据和任务将根据研究问题选择；对医疗场景的结论需要相应实验支持。

## 参考文献（References）

- Maynez, J., Narayan, S., Bohnet, B., McDonald, R. (2020). *On Faithfulness and Factuality in Abstractive Summarization*. ACL, 1906–1919. [Paper](https://aclanthology.org/2020.acl-main.173/)
