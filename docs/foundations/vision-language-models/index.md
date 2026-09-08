---
tags:
  - foundations
  - vlm
---

# 视觉语言模型（Vision-Language Model，VLM）

VLM 处理视觉信息与语言信息之间的联系。本节只建立理解多模态可信性所需的基础：图像怎样进入模型，视觉与文本怎样建立对应，以及回答怎样获得具体视觉证据支持。

VLM 是一个较宽的范围，并非所有模型都生成文本。例如，[Radford et al. (2021)](https://proceedings.mlr.press/v139/radford21a.html "文献引用") 提出的对比语言图像预训练（Contrastive Language–Image Pre-training，CLIP）通过图文表示进行匹配；它本身不是逐 token 回答问题的语言生成系统。LVLM 则通常将视觉处理与 LLM 结合，支持以图像和语言指令为条件的文本生成。[Liu et al. (2023)](https://proceedings.neurips.cc/paper_files/paper/2023/hash/6dcf277ea32ce3288914faf369fe6de0-Abstract-Conference.html "文献引用") 的视觉指令微调（Instruction Tuning）工作是这类结构的一个代表。

从文本模型扩展到视觉输入后，研究对象多了一个需要核查的条件来源。即使回答流畅，也仍需追问：图像细节是否被保留，问题中的对象是否与正确区域对应，生成的属性或关系能否由该图像支持。语言模型概率与视觉证据的支持程度，应分别考察。

阅读时可以沿一个具体问题检查这些层次：若询问“左侧杯子的颜色”，表征需要保留杯子与颜色信息，对齐需要联系问题中的词与视觉内容，定位则需要确定“左侧杯子”指向哪里。这个例子说明各页为何分开组织，也说明识别图像主题并不足以完成所有视觉问答（Visual Question Answering，VQA）。

## 页面导航

- [大型视觉语言模型](lvlm-overview.md)：理解视觉编码器（Vision Encoder）、连接模块（Cross-modal Connector）和语言模型的分工。
- [多模态表征](multimodal-representation.md)：理解不同模态的信息如何表示、交互和保留。
- [跨模态对齐](cross-modal-alignment.md)：区分整体图文匹配、细粒度对应与生成目标。
- [视觉定位](visual-grounding.md)：将语言表达连接到具体区域和证据。

这四页分别讨论结构、内部信息、跨模态对应和证据联系。它们为后续 UQ 与幻觉（Hallucination）研究提供前置知识，不在此评判某种可靠性方法最好。一般语言生成机制沿用 [语言模型](../language-models/index.md) 中的说明；医疗场景作为长期应用方向，后续再单独展开。

## 参考文献

- Radford, A., Kim, J. W., Hallacy, C., et al. (2021). *Learning Transferable Visual Models From Natural Language Supervision*. Proceedings of the 38th International Conference on Machine Learning, PMLR 139, 8748–8763. [Paper](https://proceedings.mlr.press/v139/radford21a.html)
- Liu, H., Li, C., Wu, Q., Lee, Y. J. (2023). *Visual Instruction Tuning*. Advances in Neural Information Processing Systems, 36, 34892–34916. [Paper](https://proceedings.neurips.cc/paper_files/paper/2023/hash/6dcf277ea32ce3288914faf369fe6de0-Abstract-Conference.html)
